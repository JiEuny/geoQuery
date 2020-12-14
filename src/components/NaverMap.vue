<template>
  <div id="wrap" class="section">
    <el-row :gutter="20">
      <el-col :span="12">
        <div id="naverMap" class="map"></div>
      </el-col>
      <el-col :span="12">
        <el-button class="bt" type="success" @click="geoQuery"
          >geo-query 실행</el-button
        >
        <div class="name">Request URL</div>
        <div class="request">{{ url }}</div>
        <div class="name2">Response Body</div>
        <div v-dragscroll class="grid-content bg-purple-light">
          <pre>{{ JSON.stringify(response, null, "\t") }}</pre>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from "axios";

var paths = [];
var newMark = [];
var map = null;
var HOME_PATH = window.HOME_PATH || ".";
var newPath = null;
var lat,  lng,  cont = null;
var markers = [],
  infowindows = [];

export default {
  name: "hello",
  data() {
    return {
      response: "Response",
      polyPaths: [],
      url: "",
    };
  },
  methods: {
    geoQuery: function () {
      const poly =
        paths[0][0] + " " + paths[0][1] + "," +
        paths[1][0] + " " + paths[1][1] + "," +
        paths[2][0] + " " + paths[2][1] + "," +
        paths[3][0] + " " + paths[3][1] + "," +
        paths[0][0] + " " + paths[0][1];

      console.log(poly);
      const headers = {
        "X-M2M-RI": "12345",
        "X-M2M-Origin": "SM",
        Accept: "application/json",
      };
      const url =
        "http://localhost:7599/wdc_base?gt=3&gf=1&ty=3&rcn=8&crd=" + poly;
      axios.get(url, { headers }).then((response) => {
        // console.log(response.data);
        this.response = response.data;
        this.url = url;

        for (const [key, value] of Object.entries(response.data)) {
          // console.log(key)
          for (const [key2, value2] of Object.entries(value)) {
            // console.log(value2)
            for (const body of value2) {
              //   console.log(body);
              map = new naver.maps.Map(document.getElementById("naverMap"), {
                center: new naver.maps.LatLng(
                  37.41229359683477,
                  127.12875737226753
                ),
                zoom: 16,
                zoomControl: true,
                zoomControlOptions: {
                  position: naver.maps.Position.RIGHT_TOP,
                },
              });
              for (const [key3, value3] of Object.entries(body)) {
                // console.log(value3);
                if (key3 == "loc") {
                  // console.log(value3.crd)
                  this.lat = value3.crd[1];
                  this.lng = value3.crd[0];

                } else if (key3 == "lbl") {
                  this.cont = value3[0];
                  contList.push(cont);
                }
              }
              newMark.push([this.lat, this.lng, this.cont]);

            }

            // console.log(newMark[0][0])

            for (var i = 0; i < newMark.length; i++) {
              var eachMark = new naver.maps.Marker({
                position: new naver.maps.LatLng(newMark[i][0], newMark[i][1]),
                map: map,
                icon: "http://maps.google.com/mapfiles/ms/icons/red-dot.png",
              });
              var infowindow = new naver.maps.InfoWindow({
                content: newMark[i][2],
              });
              new naver.maps.Event.addListener(eachMark, "click", function (e) {
                if (infowindow.getMap()) {
                  infowindow.close();
                } else {
                  infowindow.open(map, eachMark);
                }
                console.log(infowindow);
              });

              markers.push(eachMark);
              infowindows.push(infowindow);
            }
          }
        }
      });
    },
  },
  mounted() {
    map = new naver.maps.Map(document.getElementById("naverMap"), {
      center: new naver.maps.LatLng(37.41229359683477, 127.12875737226753),
      zoom: 16,
      zoomControl: true,
      zoomControlOptions: {
        position: naver.maps.Position.RIGHT_TOP,
      },
    });
    initMap();

    console.log("new: " + newMark);

    function makeMarker(newMark) {}

    function initMap() {
      var polygon = new naver.maps.Polygon({
        map: map,
        paths: [[]],
        fillColor: "#ff0000",
        fillOpacity: 0.3,
        strokeColor: "#ff0000",
        strokeOpacity: 0.6,
        strokeWeight: 3,
        clickable: true,
      });

      var newPath = new naver.maps.LatLng(newMark[0], newMark[1]);

      new naver.maps.Marker({
        position: newPath,
        map: map,
      });

      naver.maps.Event.addListener(map, "click", function (e) {
        var point = e.coord;

        var path = polygon.getPaths().getAt(0);
        path.push(point);

        new naver.maps.Marker({
          map: map,
          position: point,
        });

        paths.push([point._lng, point._lat]);
        console.log("path: " + paths);
      });
    }
  },
};
</script>

<style scoped>
#naverMap {
  height: 80vh;
  min-height: 800px;
  width: 100%;
}

.map {
  margin: 20px;
}

.bt {
  margin: 30px;
  float: left;
  font-size: 1.5em;
}

.request {
  background: #e5e9f2;
  text-align: left;
  margin: 0px 80px 0px 50px;
  font-size: 1.3em;
  padding: 1em;
  min-height: 50px;
}

.name {
  font-size: 1.5em;
  margin: 90px 0px 0px 30px;
  text-align: left;
  padding: 1em;
}

.name2 {
  font-size: 1.5em;
  margin: 0px 0px 0px 30px;
  text-align: left;
  padding: 1em;
}

.grid-content {
  min-height: 500px;
  max-height: 800px;
  padding: 1em;
  font-size: 1.5em;
  text-align: left;
}

.bg-purple-light {
  background: #e5e9f2;
  margin: 0px 80px 0px 50px;
  min-height: 700px;
}
</style>