<script>
  import { onMount } from "svelte";
  import Map from "./components/Map.svelte";
  import { features } from "./stores";

  onMount(() => {
    //fetch data from airtable
    const fields = ["Name", "Address", "X", "Y", "Last Updated", "Description"];

    const fields_param = fields.map(field => `fields%5B%5D=${field}`).join("&");

    fetch(
      `https://api.airtable.com/v0/app3tVhWKeW5VpzEF/Locations?api_key=keyFBPWv6Khoq2XHg&${fields_param}`
    )
      .then(res => res.json())
      .then(data => {
        features.set(data.records);
      });
  });
</script>

<style>
  main {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 2%;
  }

  .map {
    height: 600px;
    width: 100%;
  }

  .notes {
    margin-top: 1rem;
  }

  .notes p {
    margin: 1px;
    color: rgb(73, 73, 73);
  }
</style>

<main>
  <h2>Senior Meals Pickup during COVID-19</h2>
  <p>
    As of 3/20 7:56PM Manhattan senior center meal pick up locations + times
    (click on a point for more information)
  </p>
  <div class="map">
    <Map />
  </div>
  <div class="notes">
    <p>
      This page is a work in progress. Additional filter features will be added
      shortly. Contact
      <a href="mailto:zhi@beta.nyc">zhi@beta.nyc</a>
      .
    </p>
    <p>
      Data Sources can be found
      <a target="_blank" href="https://airtable.com/shr0AhGnyvs8L86nd">
        in this airtable.
      </a>
    </p>
    <p>
      Speical thanks to Paul Gale for starting the orginal map
      <a
        href="https://www.google.com/maps/d/u/0/viewer?mid=1Qn4l9Rtz9frQi15ISqumC7y9FnnP-8bo">
        here.
      </a>
    </p>
  </div>

</main>
