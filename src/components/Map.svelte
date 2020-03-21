<script>
  import { onMount } from "svelte";
  import { features } from "../stores";

  let container;
  let layer;
  let map;
  let zoom = 13;

  onMount(() => {
    map = L.map(container, { zoomControl: false }).setView([40.73, -74], zoom);
    L.tileLayer(
      "https://cartodb-basemaps-{s}.global.ssl.fastly.net/rastertiles/voyager/{z}/{x}/{y}{r}.png"
    ).addTo(map);
  });

  $: if ($features.length && map) {
    if (layer) map.removeLayer(layer);
    const data = {
      type: "FeatureCollection",
      //generate featuers array
      features: $features
        .filter(feature => feature.fields.X && feature.fields.Y)
        .map(feature => {
          const {
            Name: name,
            X: x,
            Y: y,
            Address: address,
            Description: description
          } = feature.fields;

          return {
            type: "Feature",
            properties: {
              name,
              address,
              description,
              x,
              y
            },
            geometry: {
              type: "Point",
              coordinates: [x, y]
            }
          };
        })
    };

    //map data, and add popup for each feature
    layer = L.geoJSON(data, {
      onEachFeature: (feature, layer) => {
        const prop = feature.properties;

        //keep new lines in description
        const description = prop.description
          ? prop.description.split(`\n`).join("<br/>")
          : "No description";

        const html = `
            <h2>${prop.name}</h2>
            <p><strong>Description:</strong><p>
            <span>${description}</span>
            <p><a href="https://maps.google.com/?q=${
              prop.address ? prop.address : `${prop.y},${prop.x}`
            }" _target="_blank"><strong>Address:</strong></a></p>
            <p>${prop.address ? prop.address : ""}</p>
        `;
        layer.bindPopup(html);
      }
    }).addTo(map);
    // const group = new L.featureGroup([layer]);
    // map.fitBounds(group.getBounds());
    // map.setZoom(zoom);
  }
</script>

<style>
  #map {
    width: 100%;
    height: 100%;
    z-index: 1;
  }
  :global(.leaflet-popup-content) {
    overflow: auto;
    max-height: 100%;
    word-wrap: break-word;
    line-height: 0em;
    font-size: 120%;
    max-width: 80%;
  }
  :global(.leaflet-popup-content p) {
    margin: 10px 0 !important;
  }
</style>

<div id="map" bind:this={container} />
