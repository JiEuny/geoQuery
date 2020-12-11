<template>
    <div id='wrap' class="section">
        <div id="naverMap"> </div>
    </div>
</template>

<script>
export default {
    name: 'hello',
    data() {
        return {
            // polyPaths: []

        }
    },
    mounted() {
        var map = null;
        initMap();

        var paths = []

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

                paths.push([point._lat, point._lng])
                console.log('path: '+paths);
            });

        }
    }
}
</script>

<style scoped>
#naverMap {
    height: 100vh;
    min-height: 100vh;
    width: 100%;
}
</style>