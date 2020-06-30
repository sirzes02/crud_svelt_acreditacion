<script>
  import { navigate } from "svelte-routing";
  import { onMount } from "svelte";
  import Swal from "sweetalert2";
  import axios from "axios";

  let idGlobal = "";
  let titulo = "";
  let opcCorrecta = "";
  let opc1 = "";
  let opc2 = "";
  let opc3 = "";
  let factor = "";
  let preguntas = [];
  let preguntasRespaldo = [];
  let cargado = false;
 
  const cargar = () =>
    axios.get(`http://localhost:4000/preguntas/`).then((result) => {
      preguntas = result.data;
      preguntasRespaldo = result.data;
      cargado = true;
    });

  const reiniciar = () => {
    idGlobal = "";
    titulo = "";
    opcCorrecta = "";
    opc1 = "";
    opc2 = "";
    opc3 = "";
    factor = "";
    preguntas = preguntasRespaldo;
  };

  const agregar = () => {
    if (!idGlobal)
      axios
        .post(`http://localhost:4000/preguntas/`, {
          id: preguntasRespaldo[0].id + 1,
          titulo: titulo,
          opcCorrecta: opcCorrecta,
          opc1: opc1,
          opc2: opc2,
          opc3: opc3,
          factor: factor,
        })
        .then(() =>
          Swal.fire(
            "¡Almacenada!",
            "La pregunta ha sido almacenada.",
            "success"
          )
        )
        .catch((err) => console.log(err));
    else
      axios
        .put(`http://localhost:4000/preguntas/${idGlobal}`, {
          titulo: titulo,
          opcCorrecta: opcCorrecta,
          opc1: opc1,
          opc2: opc2,
          opc3: opc3,
          factor: factor,
        })
        .then(() =>
          Swal.fire(
            "¡Modificada!",
            "La pregunta ha sido modificada.",
            "success"
          )
        )
        .catch((err) => console.log(err));
    reiniciar();
    cargar();
  };

  const eliminar = (id) => {
    Swal.fire({
      title: "¿Estás seguro?",
      text: "Se borrará totalmente la pregunta",
      icon: "warning",
      showCancelButton: true,
      confirmButtonColor: "#3085d6",
      cancelButtonColor: "#d33",
      confirmButtonText: "Si ¡Borrarla!",
      cancelButtonText: "Cancelar",
    }).then((result) => {
      if (result.value) {
        axios
          .delete(`http://localhost:4000/preguntas/${id}`)
          .then(() => {
            reiniciar();
            cargar();
          })
          .catch((err) => console.log(err));
        Swal.fire("¡Borrada!", "La pregunta ha sido borrada.", "success");
      } else Swal.fire("Cancelado", "Proceso de eliminado cancelado.", "error");
    });
  };

  const editar = (pregunta) => {
    idGlobal = pregunta._id;
    titulo = pregunta.titulo;
    opcCorrecta = pregunta.opcCorrecta;
    opc1 = pregunta.opc1;
    opc2 = pregunta.opc2;
    opc3 = pregunta.opc3;
    factor = pregunta.factor;
  };

  const wipe = (text) =>
    text
      .normalize("NFD")
      .replace(/[\u0300-\u036f]/g, "")
      .toLowerCase();

  const buscarGeneral = (busqueda) => {
    reiniciar();

    let arr = [];

    for (const pregunta of preguntas)
      if (wipe(pregunta.titulo).search(wipe(busqueda)) !== -1)
        arr.push(pregunta);
      else if (wipe(pregunta.opcCorrecta).search(wipe(busqueda)) !== -1)
        arr.push(pregunta);
      else if (wipe(pregunta.opc1).search(wipe(busqueda)) !== -1)
        arr.push(pregunta);
      else if (wipe(pregunta.opc2).search(wipe(busqueda)) !== -1)
        arr.push(pregunta);
      else if (wipe(pregunta.opc3).search(wipe(busqueda)) !== -1)
        arr.push(pregunta);
      else if (wipe(pregunta.factor).search(wipe(busqueda)) !== -1)
        arr.push(pregunta);

    preguntas = arr;
  };

  onMount(cargar);
</script>

<style>
  .scroll {
    overflow: scroll;
    height: 500px;
  }

  .loader {
    width: 120px;
    height: 120px;
  }
</style>

<svelte:head>
  <title>Preguntas</title>
</svelte:head>

<main>
  <ul class="nav nav-pills">
    <li class="nav-item">
      <a class="nav-link active">Preguntas</a>
    </li>
    <li class="nav-item">
      <a class="nav-link" href="" on:click={() => navigate('/estudiantes')}>
        Estudiantes
      </a>
    </li>
    <li class="nav-item">
      <a class="nav-link" href="" on:click={() => navigate('/informacion')}>
        Información
      </a>
    </li>
  </ul>

  <div class="Preguntas">
    <div class="container center p-3 mb-3 bg-white rounded">
      <form on:submit|preventDefault={agregar} class="card card-body">
        <div class="form-group">
          <input
            type="text"
            class="form-control"
            placeholder="Titulo"
            id="titulo_input"
            required
            bind:value={titulo} />
        </div>
        <div class="form-group">
          <input
            type="text"
            class="form-control"
            placeholder="Opcion correcta"
            id="opcCorrecta_input"
            required
            bind:value={opcCorrecta} />
        </div>
        <div class="form-group">
          <input
            type="text"
            class="form-control"
            placeholder="Opcion 1"
            id="opcion1_input"
            required
            bind:value={opc1} />
        </div>
        <div class="form-group">
          <input
            type="text"
            class="form-control"
            placeholder="Opcion 2"
            id="opcion2_input"
            required
            bind:value={opc2} />
        </div>
        <div class="form-group">
          <input
            type="text"
            class="form-control"
            placeholder="Opcion 3"
            id="opcion3_input"
            required
            bind:value={opc3} />
        </div>
        <div class="form-group">
          <input
            type="number"
            class="form-control"
            placeholder="Factor"
            id="factor_input"
            required
            min="0"
            max="12"
            bind:value={factor} />
        </div>
        <div class="mt-3">
          <button type="submit" class="btn btn-primary float-left">
            {#if !idGlobal}Crear{:else}Actualizar{/if}
          </button>
          <button
            type="reset"
            class="btn btn-danger float-right"
            on:click={reiniciar}>
            Limpiar
          </button>
        </div>
      </form>
    </div>
    <button
      class="btn btn-warning ml-5 mb-3"
      on:click={() => Swal.mixin({
          input: 'text',
          confirmButtonText: 'Confirmar',
          cancelButtonText: 'Cancelar',
          showCancelButton: true,
        })
          .queue([
            {
              title: 'Ingrese datos para buscar',
              text: 'Debe ingresar cualquier palabra como referencia',
            },
          ])
          .then((result) => {
            if (result.value) buscarGeneral(result.value[0]);
          })}>
      Buscar
    </button>
    <div class="float-right mr-5">
      <b>Cantidad:</b>
      {preguntas.length} de {preguntasRespaldo.length}
    </div>
    <div
      class="mx-5 mb-3 shadow bg-white rounded d-flex justify-content-center">
      {#if cargado}
        <div class="table-responsive scroll">
          <table class="table">
            <thead>
              <tr>
                <th>Código</th>
                <th>Título</th>
                <th>Correcta</th>
                <th>Opción 1</th>
                <th>Opción 2</th>
                <th>Opción 3</th>
                <th>Factor</th>
                <th />
              </tr>
            </thead>
            <tbody>
              {#each preguntas as pregunta}
                <tr key={pregunta._id}>
                  <td>{pregunta.id}</td>
                  <td>{pregunta.titulo}</td>
                  <td>{pregunta.opcCorrecta}</td>
                  <td>{pregunta.opc1}</td>
                  <td>{pregunta.opc2}</td>
                  <td>{pregunta.opc3}</td>
                  <td>
                    {pregunta.factor === '0' ? 'General' : pregunta.factor}
                  </td>
                  <td>
                    <button
                      class="btn btn-danger"
                      on:click={() => eliminar(pregunta._id)}>
                      <i class="material-icons">delete_forever</i>
                    </button>
                    <button
                      class="btn btn-warning"
                      on:click={() => editar(pregunta)}>
                      <i class="material-icons">edit</i>
                    </button>
                  </td>
                </tr>
              {/each}
            </tbody>
          </table>
        </div>
      {:else}
        <div class="spinner-border loader" role="status">
          <span class="sr-only">Loading...</span>
        </div>
      {/if}
    </div>
  </div>
</main>
