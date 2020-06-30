<script>
  import { navigate } from "svelte-routing";
  import { onMount } from "svelte";
  import Swal from "sweetalert2";
  import axios from "axios";

  let busqueda = "";
  let usuarios = [];
  let usuariosRespaldo = [];
  let cargado = false;

  const cargar = () =>
    axios.get(`http://localhost:4000/usuario/`).then((result) => {
      usuarios = result.data;
      usuariosRespaldo = result.data;
      cargado = true;
    });

  const reiniciar = () => {
    busqueda = "";
    usuarios = usuariosRespaldo;
  };

  const eliminar = (id) => {
    Swal.fire({
      title: "¿Estás seguro?",
      text: "Se borrará totalmente el usuario",
      icon: "warning",
      showCancelButton: true,
      confirmButtonColor: "#3085d6",
      cancelButtonColor: "#d33",
      confirmButtonText: "Si ¡Borrarlo!",
      cancelButtonText: "Cancelar",
    }).then((result) => {
      if (result.value) {
        axios
          .delete(`http://localhost:4000/usuario/${id}`)
          .then(() => {
            reiniciar();
            cargar();
          })
          .catch((err) => console.log(err));
        Swal.fire("¡Borrado!", "El usuario ha sido borrada.", "success");
      } else Swal.fire("Cancelado", "Proceso de eliminado cancelado.", "error");
    });
  };

  const wipe = (text) =>
    text
      .normalize("NFD")
      .replace(/[\u0300-\u036f]/g, "")
      .toLowerCase();

  const buscarGeneral = () => {
    let arr = [];

    for (const usuario of usuariosRespaldo)
      if (wipe(usuario.email).search(wipe(busqueda)) !== -1) arr.push(usuario);
      else if (usuario.cedula.search(busqueda) !== -1) arr.push(usuario);
      else if (wipe(usuario.nombre).search(wipe(busqueda)) !== -1)
        arr.push(usuario);
      else if (wipe(usuario.programa).search(wipe(busqueda)) !== -1)
        arr.push(usuario);
      else if (wipe(usuario.facultad).search(wipe(busqueda)) !== -1)
        arr.push(usuario);

    usuarios = arr;
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
  <title>Estudiantes</title>
</svelte:head>

<main>
  <ul class="nav nav-pills">
    <li class="nav-item">
      <a class="nav-link" href="" on:click={() => navigate('/preguntas')}>
        Preguntas
      </a>
    </li>
    <li class="nav-item">
      <a class="nav-link active">Estudiantes</a>
    </li>
    <li class="nav-item">
      <a class="nav-link" href="" on:click={() => navigate('/informacion')}>
        Información
      </a>
    </li>
  </ul>

  <div class="container center p-3 mb-3 bg-white rounded">
    <form class="card card-body" on:submit|preventDefault={() => {}}>
      <div class="form-group">
        <input
          type="text"
          class="form-control"
          placeholder="Busqueda"
          id="busqueda_input"
          required
          bind:value={busqueda}
          on:keyup={buscarGeneral} />
      </div>
      <div class="mt-3">
        <button
          type="reset"
          class="btn btn-danger float-right"
          on:click={reiniciar}>
          Limpiar
        </button>
      </div>
    </form>
  </div>
  <div class="mx-5 mb-3 shadow bg-white rounded d-flex justify-content-center">
    {#if cargado}
      <div class="table-responsive scroll">
        <table class="table">
          <thead>
            <tr>
              <th>Cédula</th>
              <th>Tipo</th>
              <th>Correo</th>
              <th>Nombre</th>
              <th>Programa</th>
              <th>Facultad</th>
              <th>Puntaje</th>
              <th />
            </tr>
          </thead>
          <tbody>
            {#each usuarios as usuario}
              <tr key={usuario._id}>
                <td>{usuario.cedula}</td>
                <td>{usuario.tipo}</td>
                <td>{usuario.email}</td>
                <td>{usuario.nombre}</td>
                <td>{usuario.programa}</td>
                <td>{usuario.facultad}</td>
                <td>{usuario.puntaje}</td>
                <td>
                  <button
                    class="btn btn-danger"
                    on:click={() => eliminar(usuario._id)}>
                    <i class="material-icons">delete_forever</i>
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
</main>
