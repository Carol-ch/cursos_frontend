<script>
  import { onMount } from 'svelte';

  let cursos = [];
  let nuevoCurso = { curso: '', creditos: 0, horasSemanal: 0, ciclo: '', nombreDocente: '' };
  let editando = null;

  const API_URL = 'https://cursos-backend.onrender.com/api/cursos';

  async function cargarCursos() {
    const res = await fetch(API_URL);
    cursos = await res.json();
  }

  async function crearCurso() {
    await fetch(API_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(nuevoCurso)
    });
    nuevoCurso = { curso: '', creditos: 0, horasSemanal: 0, ciclo: '', nombreDocente: '' };
    await cargarCursos();
  }

  async function eliminarCurso(id) {
    await fetch(`${API_URL}/${id}`, { method: 'DELETE' });
    await cargarCursos();
  }

  function cargarEdicion(curso) {
    editando = { ...curso };
  }

  async function guardarEdicion() {
    await fetch(`${API_URL}/${editando._id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(editando)
    });
    editando = null;
    await cargarCursos();
  }

  onMount(cargarCursos);
</script>

<style>
  body {
    font-family: Arial, sans-serif;
    padding: 2rem;
    background-color: #f8f9fa;
  }

  h1 {
    margin-bottom: 1rem;
    color: #333;
  }

  ul {
    list-style: none;
    padding: 0;
    margin-bottom: 2rem;
  }

  li {
    background: #fff;
    padding: 1rem;
    margin-bottom: 0.5rem;
    border: 1px solid #ddd;
    border-radius: 5px;
  }

  input {
    margin-right: 10px;
    padding: 0.4rem;
    margin-bottom: 0.5rem;
  }

  button {
    margin-right: 5px;
    padding: 0.4rem 0.8rem;
    border: none;
    border-radius: 3px;
    cursor: pointer;
  }

  button:hover {
    opacity: 0.9;
  }

  .crear-btn {
    background-color: #198754;
    color: white;
  }

  .editar-btn {
    background-color: #0d6efd;
    color: white;
  }

  .eliminar-btn {
    background-color: #dc3545;
    color: white;
  }

  .cancelar-btn {
    background-color: #6c757d;
    color: white;
  }
</style>

<h1>Cursos</h1>

<ul>
  {#each cursos as curso}
    <li>
      <strong>{curso.curso}</strong> — Créditos: {curso.creditos}, Horas/Sem: {curso.horasSemanal}, Ciclo: {curso.ciclo}, Docente: {curso.nombreDocente}
      <br />
      <button class="editar-btn" on:click={() => cargarEdicion(curso)}>✏️ Editar</button>
      <button class="eliminar-btn" on:click={() => eliminarCurso(curso._id)}>❌ Eliminar</button>
    </li>
  {/each}
</ul>

<h2>Agregar nuevo curso</h2>
<input placeholder="Curso" bind:value={nuevoCurso.curso} />
<input placeholder="Créditos" type="number" bind:value={nuevoCurso.creditos} />
<input placeholder="Horas semanales" type="number" bind:value={nuevoCurso.horasSemanal} />
<input placeholder="Ciclo" bind:value={nuevoCurso.ciclo} />
<input placeholder="Nombre del docente" bind:value={nuevoCurso.nombreDocente} />
<br />
<button class="crear-btn" on:click={crearCurso}>Crear</button>

{#if editando}
  <h2>Editar curso</h2>
  <input placeholder="Curso" bind:value={editando.curso} />
  <input placeholder="Créditos" type="number" bind:value={editando.creditos} />
  <input placeholder="Horas semanales" type="number" bind:value={editando.horasSemanal} />
  <input placeholder="Ciclo" bind:value={editando.ciclo} />
  <input placeholder="Nombre del docente" bind:value={editando.nombreDocente} />
  <br />
  <button class="editar-btn" on:click={guardarEdicion}>Guardar</button>
  <button class="cancelar-btn" on:click={() => (editando = null)}>Cancelar</button>
{/if}
