<script>
  import { Router, Link, Route, navigate } from "svelte-routing";
  import { onMount } from "svelte";
  import Login from "./routes/Login.svelte";
  import Preguntas from "./routes/Preguntas.svelte";
  import Estudiantes from "./routes/Estudiantes.svelte";
  import Informacion from "./routes/Informacion.svelte";
  import Error404 from "./routes/NotFound.svelte";

  export let url = "";

  let admin = false;

  const cambioSesion = () => {
    admin = !admin;
  };

  const cerrarSesion = () => {
    cambioSesion();
    localStorage.setItem("tokens", "");
    navigate("/");
  };

  const verificarSesion = () => {
    if (localStorage.getItem("tokens") && localStorage.getItem("tokens") !== "")
      cambioSesion();
  };

  onMount(() => verificarSesion());
</script>

<style>
  .botonCerrarSesion {
    position: fixed;
    bottom: 50px;
    right: 20px;
  }
</style>

<svelte:head>
  <link
    href="https://fonts.googleapis.com/icon?family=Material+Icons"
    rel="stylesheet" />
</svelte:head>

<main>
  <Router {url}>
    {#if admin}
      {#if localStorage.getItem('tokens') && JSON.parse(localStorage.getItem('tokens')).permisos === 0}
        <Route path="/preguntas" component={Preguntas} />
        <Route path="/estudiantes" component={Estudiantes} />
      {/if}
      <Route path="/informacion" component={Informacion} />
      <button class="btn btn-danger botonCerrarSesion" on:click={cerrarSesion}>
        Cerrar Sesión
      </button>
    {:else}
      <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <div class="container">
          <a class="navbar-brand" href="">
            Aplicación para la acreditación de alta calidad
          </a>
        </div>
      </nav>
      <Route path="/" exact component={Login} sesion={cambioSesion} />
      <Route component={Error404} />
    {/if}
  </Router>
  <footer class="page-footer font-small blue">
    <div class="footer-copyright text-center py-3">
      © 2020 Copyright:
      <a href="https://usc.edu.co">Universidad Santiago de Cali</a>
    </div>
  </footer>
</main>
