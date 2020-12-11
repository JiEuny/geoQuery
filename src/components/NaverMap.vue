<template>
    <div id='wrap' class="section">
        <el-row :gutter="20">
            <el-col :span="12" >
                <div id="naverMap" class="map"> </div>
            </el-col>
            <el-col :span="12">
                <el-button class="bt" type="success" @click="geoQuery">geo-query 실행</el-button>
                <div class="grid-content bg-purple-light">
                </div>
            </el-col>
        </el-row>
    </div>
</template>

<script>
import axios from "axios"

var paths = [];
export default {
    name: 'hello',
    data() {
        return {
            response: 'Response',
            polyPaths: []

        }
    },
    methods: {
        geoQuery: function() {
            // const poly = 
            // console.log(paths.length);
            // for (var i=0; i++; i<paths.length) {
            //     console.log(paths[i][0]);
            // }
            const poly = paths[0][0] + " " + paths[0][1] + "," + paths[1][0] + " " + paths[1][1] + "," + paths[2][0] + " " + paths[2][1] + "," + paths[3][0] + " " + paths[3][1]
            console.log(poly);
            const headers = {
                "X-M2M-RI": "12345",
                "X-M2M-Origin": "SM",
                "Accept": "application/json"
            };
            const url = "http://localhost:7599/wdc_base?gt=3&gf=1&ty=3&rcn=8&crd=" + poly;
            axios.get(url, { headers }).then((response) => {
                console.log(response.data);
                this.response = response.data;
            });
        }
    },
    mounted() {
        var map = null;
        initMap();

        // var paths = []

        function initMap() {
            map = new naver.maps.Map(document.getElementById('naverMap'), {
                center: new naver.maps.LatLng(37.41229359683477, 127.12875737226753),
                zoom: 16,
                zoomControl: true,
                zoomControlOptions: {
                    position: naver.maps.Position.RIGHT_TOP
                }
            });

            var polygon = new naver.maps.Polygon({
                map: map,
                paths: [[]],
                fillColor: '#ff0000',
                fillOpacity: 0.3,
                strokeColor: '#ff0000',
                strokeOpacity: 0.6,
                strokeWeight: 3,
                clickable: true
            });

            naver.maps.Event.addListener(map, 'click', function(e) {

                var point = e.coord;

                var path = polygon.getPaths().getAt(0);
                path.push(point);
                // paths.push(e.coord);

                new naver.maps.Marker({
                    map: map, 
                    position: point
                });

                // console.log('path: '+paths);
                // console.log(paths.length)
                // console.log('lat: '+ point._lat)

                paths.push([point._lng, point._lat])
                console.log('path: '+paths);
                // this.polyPaths.push(point._lat);
            });

        }
    }
}
</script>

<style scoped>
#naverMap {
    height: 70vh;
    min-height: 600px;
    width: 100%;
}

.map {
    /* padding: 10px; */
    margin: 20px
}

.bt {
    margin: 50px;
    float: left;
  font-size: 1.5em;
}

.grid-content {
  min-height: 500px;
  /* padding: 2em 1em 1em 1em; */
  font-size: 1.5em;
  margin: 20px;
}

.bg-purple-light {
  background: #e5e9f2;
  text-align: left;
  margin: 130px 80px 60px 50px;
  min-height: 600px;
}
</style>