# DOCU3C-
Intern work
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kid's Location Tracker</title>
    <style>
        #map {
            height: 400px;
            width: 100%;
        }
    </style>
</head>
<body>
    <h1>Kid's Location Tracker</h1>
    <div id="map"></div>
    <script>
        let map;
        let marker;
        let geocoder;
        let infowindow;
        let watchId;

        function initMap() {
            map = new google.maps.Map(document.getElementById('map'), {
                center: { lat: -34.397, lng: 150.644 },
                zoom: 15
            });

            geocoder = new google.maps.Geocoder();
            infowindow = new google.maps.InfoWindow();

            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(function (position) {
                    let pos = {
                        lat: position.coords.latitude,
                        lng: position.coords.longitude
                    };

                    map.setCenter(pos);
                    marker = new google.maps.Marker({
                        position: pos,
                        map: map
                    });

                    geocoder.geocode({ 'location': pos }, function (results, status) {
                        if (status === 'OK') {
                            if (results[0]) {
                                infowindow.setContent(results[0].formatted_address);
                                infowindow.open(map, marker);
                            } else {
                                window.alert('No results found');
                            }
                        } else {
                            window.alert('Geocoder failed due to: ' + status);
                        }
                    });

                    watchId = navigator.geolocation.watchPosition(function (position) {
                        let pos = {
                            lat: position.coords.latitude,
                            lng: position.coords.longitude
                        };

                        marker.setPosition(pos);
                        map.setCenter(pos);

                        geocoder.geocode({ 'location': pos }, function (results, status) {
                            if (status === 'OK') {
                                if (results[0]) {
                                    infowindow.setContent(results[0].formatted_address);
                                    infowindow.open(map, marker);
                                } else {
                                    window.alert('No results found');
                                }
                            } else {
                                window.alert('Geocoder failed due to: ' + status);
                            }
                        });

                        // Send notification
                        if (Notification.permission === 'granted') {
                            navigator.serviceWorker.ready.then(function (registration) {
                                registration.showNotification('Kid\'s Location', {
                                    body: 'Your kid is at ' + results[0].formatted_address,
                                    icon: 'https://maps.gstatic.com/mapfiles/api-3/images/spotlight-poi2.png',
                                    vibrate: [200, 100, 200],
                                    tag: 'kid-location'
                                });
                            });
                        }
                    }, function (error) {
                        console.log('Error: ' + error.message);
                    });
                }, function (error) {
                    console.log('Error: ' + error.message);
                });
            } else {
                window.alert('Geolocation is not supported by this browser.');
            }
        }
    </script>
    <script async defer src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_
