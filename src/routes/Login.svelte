<script>
  import { navigate } from "svelte-routing";
  import { onMount } from "svelte";
  import Swal from "sweetalert2";
  import axios from "axios";

  export let sesion;

  let contrasenia = "";

  const verificarSesion = () => {
    if (localStorage.getItem("tokens") && localStorage.getItem("tokens") !== "")
      navigate("/informacion");
  };

  onMount(() => verificarSesion());

  const onSubmitHandler = () => {
    axios.get(`http://localhost:4000/contra/${contrasenia}`).then((result) => {
      if (result.data.status) {
        localStorage.setItem("tokens", JSON.stringify(result.data));
        sesion(result.data.permisos);
        Swal.fire({
          title: "Bienvenido!",
          html: `Estas en la sesion de: <b>${
            result.data.permisos === 0 ? "Vicerrectoria" : "Decanatura"
          }</b>`,
          timer: 4000,
        });
        navigate("/informacion", { replace: true });
      } else {
        Swal.fire("Error", "Contraseña incorrecta, no encontrada", "error");
        contrasenia = "";
      }
    });
  };
</script>

<style>
  .centradoLogin {
    margin-top: 15%;
    margin-bottom: 17%;
  }
</style>

<main>
  <div
    class="row justify-content-center bg-white rounded aling-middle
    centradoLogin">
    <form class="container" on:submit|preventDefault={onSubmitHandler}>
      <input
        type="password"
        class="form-control"
        placeholder="Contraseña"
        bind:value={contrasenia}
        required />
      <div class="mt-3">
        <button type="submit" class="btn btn-primary float-left">
          Ingresar
        </button>
        <button type="reset" class="btn btn-danger float-right">Limpiar</button>
      </div>
    </form>
  </div>
</main>
