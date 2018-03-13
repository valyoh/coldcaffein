---
layout: post
title: Distance betweent two points using latitude and longitude
tags: [Distance between points]
---

Най-краткото разстояние между две точки от земната повърхност, ако се приеме че земята е сфера, е разстоянието между двете точки лежащи на ортодромията - голямата окръжност, която преминава през тези две точки и центъра ѝ съвпада с центъра на сферата. За да бъде изчислено най-късото разстоянието между две точки от земната повърхност се използва следната формула.

```
a = sin²(Δφ/2) + cos φ1 ⋅ cos φ2 ⋅ sin²(Δλ/2)
c = 2 ⋅ atan2( √a, √(1−a) )
distance = R ⋅ c
```
където R е земният радиус (средно 6371 км) , φ е географската ширина, λ е географската дължина.

Програмният код за изчисляване на Java:
```java
function getDistanceFromLatLonInKm(lat1,lon1,lat2,lon2) {
  var R = 6371; // Radius of the earth in km
  var dLat = deg2rad(lat2-lat1);  // deg2rad below
  var dLon = deg2rad(lon2-lon1); 
  var a = 
    Math.sin(dLat/2) * Math.sin(dLat/2) +
    Math.cos(deg2rad(lat1)) * Math.cos(deg2rad(lat2)) * 
    Math.sin(dLon/2) * Math.sin(dLon/2); 
  var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a)); 
  var d = R * c; // Distance in km
  return d;
}

function deg2rad(deg) {
  return deg * (Math.PI/180)
}
```