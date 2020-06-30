<script>
  import { navigate } from "svelte-routing";
  import { onMount } from "svelte";
  import FusionCharts from "fusioncharts";
  import Charts from "fusioncharts/fusioncharts.charts";
  import FusionTheme from "fusioncharts/themes/fusioncharts.theme.fusion";
  import SvelteFC, { fcRoot } from "svelte-fusioncharts";

  let permisos = JSON.parse(localStorage.getItem("tokens")).permisos;
  let programa = "";
  let facultad = "";
  let dependencia = "";
  let factor = "";

  const reiniciar = () => {
    programa = "";
    facultad = "";
    dependencia = "";
    factor = "";
  };

  fcRoot(FusionCharts, Charts, FusionTheme);

  const chartConfigs = {
    type: "pie2d", //Select the chart type
    width: 600, //Set the width of the chart
    height: 400, //Set the height of the chart
    dataFormat: "json", //Set the input dataFormat to json
    dataSource: {
      chart: {
        caption: "Countries With Most Oil Reserves [2017-18]",
        subCaption: "In MMbbl = One Million barrels",
        xAxisName: "Country", //Assign a relevant name to your x-axis
        yAxisName: "Reserves (MMbbl)", //Assign a relevant name to your y-axis
        numberSuffix: "K",
        theme: "fusion",
      },
      data: [
        { label: "Venezuela", value: "290" },
        { label: "Saudi", value: "260" },
        { label: "Canada", value: "180" },
        { label: "Iran", value: "140" },
        { label: "Russia", value: "115" },
        { label: "UAE", value: "100" },
        { label: "US", value: "30" },
        { label: "China", value: "30" },
      ],
    },
  };
</script>

<svelte:head>
  <title>Información</title>
</svelte:head>

<main>
  <ul class="nav nav-pills">
    <li class="nav-item">
      <a
        class={`nav-link ${permisos === 1 ? 'disabled' : ''}`}
        href=""
        on:click={() => navigate('/preguntas')}>
        Preguntas
      </a>
    </li>
    <li class="nav-item">
      <a
        class={`nav-link ${permisos === 1 ? 'disabled' : ''}`}
        href=""
        on:click={() => navigate('/estudiantes')}>
        Estudiantes
      </a>
    </li>
    <li class="nav-item">
      <a class="nav-link active">Información</a>
    </li>
  </ul>

  <div class="container center p-3 mb-3 rounded">
    <form class="card card-body">
      <div class="row row-cols-2">
        <div class="form-group col">
          <select class="custom-select" bind:value={programa}>
            <option defaultValue>Programa</option>
            <option value="1">One</option>
            <option value="2">Two</option>
            <option value="3">Three</option>
          </select>
        </div>
        <div class="form-group col">
          <select class="custom-select" bind:value={facultad}>
            <option defaultValue>Facultad</option>
            <option value="1">One</option>
            <option value="2">Two</option>
            <option value="3">Three</option>
          </select>
        </div>
        <div class="form-group col">
          <select class="custom-select" bind:value={dependencia}>
            <option defaultValue>Dependencia</option>
            <option value="1">One</option>
            <option value="2">Two</option>
            <option value="3">Three</option>
          </select>
        </div>
        <div class="form-group col">
          <select class="custom-select" bind:value={factor}>
            <option defaultValue>Factor</option>
            <option value="1">One</option>
            <option value="2">Two</option>
            <option value="3">Three</option>
          </select>
        </div>
      </div>
      <div class="mt-3">
        <button type="submit" class="btn btn-primary float-left">
          Ingresar
        </button>
        <button
          type="reset"
          class="btn btn-danger float-right"
          onClick={reiniciar}>
          Limpiar
        </button>
      </div>
    </form>
  </div>
  <div class="d-flex flex-row justify-content-center alig-items-center">
    <table>
      <thead>
        <tr>
          <th />
          <th />
        </tr>
      </thead>
    </table>
    <tbody>
      <tr>
        <td>
          <SvelteFC {...chartConfigs} />
        </td>
        <td>
          <SvelteFC {...chartConfigs} />
        </td>
      </tr>

      <tr>
        <td>
          <SvelteFC {...chartConfigs} />
        </td>
        <td>
          <SvelteFC {...chartConfigs} />
        </td>
      </tr>
    </tbody>
  </div>
</main>
