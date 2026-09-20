<script setup>
import { computed, reactive, ref } from "vue";

const alumnos = ref([]);
const busqueda = ref("");

const alumno = reactive({
  cedula: "",
  nombre: "",
  apellido: "",
  edad: "",
  correo: "",
  curso: ""
});

const cursos = [
  "Licenciatura en Informática",
  "Ingeniería de Sistemas",
  "Administración de Empresas",
  "Contaduría Pública",
  "Derecho",
  "Psicología",
  "Otro curso"
];

const alumnosFiltrados = computed(() => {
  const texto = busqueda.value.toLowerCase().trim();

  if (!texto) return alumnos.value;

  return alumnos.value.filter((estudiante) =>
    `${estudiante.nombre} ${estudiante.apellido} ${estudiante.cedula} ${estudiante.curso}`
      .toLowerCase()
      .includes(texto)
  );
});

function registrarAlumno() {
  if (
    !alumno.cedula ||
    !alumno.nombre ||
    !alumno.apellido ||
    !alumno.correo ||
    !alumno.curso
  ) {
    alert("Completa todos los campos obligatorios.");
    return;
  }

  alumnos.value.push({
    ...alumno,
    id: Date.now()
  });

  alert("Estudiante registrado correctamente.");
  limpiar();
}

function limpiar() {
  alumno.cedula = "";
  alumno.nombre = "";
  alumno.apellido = "";
  alumno.edad = "";
  alumno.correo = "";
  alumno.curso = "";
}

function eliminarAlumno(id) {
  alumnos.value = alumnos.value.filter((estudiante) => estudiante.id !== id);
}
</script>

<template>
  <main class="app-shell">
    <header class="hero">
      <div>
        <p class="eyebrow">UNIVERSIDAD · GESTIÓN ACADÉMICA</p>
        <h1>Listado de estudiantes</h1>
        <p class="subtitle">
          Registra, consulta y organiza los estudiantes junto a su curso universitario.
        </p>
      </div>
      <div class="hero-icon">🎓</div>
    </header>

    <section class="stats-grid">
      <article class="stat-card">
        <span class="stat-label">Estudiantes registrados</span>
        <strong>{{ alumnos.length }}</strong>
      </article>
      <article class="stat-card">
        <span class="stat-label">Cursos disponibles</span>
        <strong>{{ cursos.length }}</strong>
      </article>
      <article class="stat-card">
        <span class="stat-label">Resultados visibles</span>
        <strong>{{ alumnosFiltrados.length }}</strong>
      </article>
    </section>

    <section class="content-grid">
      <article class="panel registration-panel">
        <div class="panel-heading">
          <div>
            <p class="section-kicker">NUEVO REGISTRO</p>
            <h2>Agregar estudiante</h2>
          </div>
          <span class="panel-symbol">＋</span>
        </div>

        <form @submit.prevent="registrarAlumno" class="student-form">
          <label>
            Número de identificación
            <input v-model="alumno.cedula" type="text" placeholder="Ej. 1000123456" required />
          </label>

          <div class="two-columns">
            <label>
              Nombre
              <input v-model="alumno.nombre" type="text" placeholder="Nombre" required />
            </label>

            <label>
              Apellido
              <input v-model="alumno.apellido" type="text" placeholder="Apellido" required />
            </label>
          </div>

          <div class="two-columns">
            <label>
              Edad
              <input v-model="alumno.edad" type="number" min="1" max="100" placeholder="Edad" />
            </label>

            <label>
              Correo electrónico
              <input v-model="alumno.correo" type="email" placeholder="correo@universidad.edu" required />
            </label>
          </div>

          <label>
            Curso universitario
            <select v-model="alumno.curso" required>
              <option disabled value="">Selecciona un curso</option>
              <option v-for="curso in cursos" :key="curso" :value="curso">
                {{ curso }}
              </option>
            </select>
          </label>

          <div class="form-actions">
            <button type="button" class="button secondary" @click="limpiar">Limpiar</button>
            <button type="submit" class="button primary">Guardar estudiante</button>
          </div>
        </form>
      </article>

      <article class="panel list-panel">
        <div class="panel-heading list-heading">
          <div>
            <p class="section-kicker">DIRECTORIO ACADÉMICO</p>
            <h2>Estudiantes registrados</h2>
          </div>
          <span class="count-badge">{{ alumnosFiltrados.length }}</span>
        </div>

        <div class="search-wrapper">
          <span>⌕</span>
          <input v-model="busqueda" type="search" placeholder="Buscar por nombre, identificación o curso..." />
        </div>

        <div v-if="alumnosFiltrados.length === 0" class="empty-state">
          <div class="empty-icon">📚</div>
          <h3>No hay estudiantes para mostrar</h3>
          <p>Agrega un registro desde el formulario o cambia el texto de búsqueda.</p>
        </div>

        <div v-else class="student-list">
          <article v-for="estudiante in alumnosFiltrados" :key="estudiante.id" class="student-card">
            <div class="avatar">
              {{ estudiante.nombre.charAt(0).toUpperCase() }}{{ estudiante.apellido.charAt(0).toUpperCase() }}
            </div>

            <div class="student-info">
              <h3>{{ estudiante.nombre }} {{ estudiante.apellido }}</h3>
              <p class="student-id">ID: {{ estudiante.cedula }}</p>
              <p class="course-tag">{{ estudiante.curso }}</p>
              <p class="student-email">{{ estudiante.correo }}</p>
            </div>

            <button class="delete-button" type="button" title="Eliminar estudiante" @click="eliminarAlumno(estudiante.id)">
              ×
            </button>
          </article>
        </div>
      </article>
    </section>

    <footer class="footer">
      Sistema de registro académico · Diseñado con Vue y CSS
    </footer>
  </main>
</template>

<style scoped>
:global(*) {
  box-sizing: border-box;
}

:global(body) {
  margin: 0;
  font-family: Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
  background: #f3f6fb;
  color: #172033;
}

:global(button),
:global(input),
:global(select) {
  font: inherit;
}

.app-shell {
  min-height: 100vh;
  padding: 36px clamp(18px, 4vw, 64px);
  background:
    radial-gradient(circle at 10% 0%, rgba(77, 124, 254, 0.12), transparent 30%),
    #f3f6fb;
}

.hero {
  max-width: 1250px;
  margin: 0 auto 28px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 20px;
}

.eyebrow,
.section-kicker {
  margin: 0 0 8px;
  color: #5371c9;
  font-size: 0.75rem;
  font-weight: 800;
  letter-spacing: 0.12em;
}

h1,
h2,
h3,
p {
  margin-top: 0;
}

h1 {
  margin-bottom: 10px;
  font-size: clamp(2rem, 4vw, 3.3rem);
  letter-spacing: -0.05em;
}

.subtitle {
  max-width: 650px;
  margin-bottom: 0;
  color: #65728a;
  font-size: 1rem;
}

.hero-icon {
  display: grid;
  place-items: center;
  width: 88px;
  height: 88px;
  border-radius: 28px;
  background: #e1e9ff;
  font-size: 2.6rem;
  box-shadow: 0 15px 35px rgba(54, 86, 160, 0.12);
}

.stats-grid {
  max-width: 1250px;
  margin: 0 auto 24px;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
}

.stat-card,
.panel {
  border: 1px solid #e1e7f1;
  border-radius: 22px;
  background: rgba(255, 255, 255, 0.9);
  box-shadow: 0 12px 35px rgba(31, 49, 85, 0.06);
}

.stat-card {
  padding: 22px 24px;
}

.stat-label {
  display: block;
  color: #718096;
  font-size: 0.85rem;
  margin-bottom: 8px;
}

.stat-card strong {
  font-size: 2rem;
  color: #273b78;
}

.content-grid {
  max-width: 1250px;
  margin: 0 auto;
  display: grid;
  grid-template-columns: minmax(300px, 0.85fr) minmax(350px, 1.15fr);
  gap: 24px;
  align-items: start;
}

.panel {
  padding: 28px;
}

.panel-heading {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 16px;
  margin-bottom: 24px;
}

.panel-heading h2 {
  margin-bottom: 0;
  font-size: 1.35rem;
}

.panel-symbol,
.count-badge {
  display: grid;
  place-items: center;
  width: 40px;
  height: 40px;
  border-radius: 13px;
  background: #edf2ff;
  color: #526ec5;
  font-size: 1.4rem;
  font-weight: 800;
}

.count-badge {
  font-size: 0.9rem;
}

.student-form {
  display: grid;
  gap: 16px;
}

label {
  display: grid;
  gap: 8px;
  color: #3e4b63;
  font-size: 0.85rem;
  font-weight: 700;
}

input,
select {
  width: 100%;
  min-width: 0;
  border: 1px solid #dce3ef;
  border-radius: 12px;
  padding: 13px 14px;
  color: #172033;
  background: #fbfcff;
  outline: none;
  transition: 0.2s ease;
}

input:focus,
select:focus {
  border-color: #718cf0;
  box-shadow: 0 0 0 4px rgba(113, 140, 240, 0.14);
}

.two-columns {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 14px;
}

.form-actions {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
  margin-top: 8px;
}

.button {
  border: 0;
  border-radius: 12px;
  padding: 13px 18px;
  cursor: pointer;
  font-weight: 800;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.button:hover {
  transform: translateY(-1px);
}

.primary {
  color: white;
  background: linear-gradient(135deg, #5878e5, #405dc1);
  box-shadow: 0 8px 18px rgba(64, 93, 193, 0.22);
}

.secondary {
  color: #53617a;
  background: #eef2f8;
}

.search-wrapper {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 0 14px;
  margin-bottom: 18px;
  border: 1px solid #dce3ef;
  border-radius: 13px;
  background: #fbfcff;
  color: #7b89a2;
}

.search-wrapper input {
  border: 0;
  padding-left: 0;
  box-shadow: none;
  background: transparent;
}

.student-list {
  display: grid;
  gap: 12px;
}

.student-card {
  display: flex;
  align-items: flex-start;
  gap: 14px;
  padding: 16px;
  border: 1px solid #e4e9f2;
  border-radius: 17px;
  background: #fff;
}

.avatar {
  flex: 0 0 48px;
  display: grid;
  place-items: center;
  width: 48px;
  height: 48px;
  border-radius: 15px;
  color: #4059a9;
  background: #e5ebff;
  font-weight: 900;
}

.student-info {
  min-width: 0;
  flex: 1;
}

.student-info h3 {
  margin-bottom: 4px;
  font-size: 1rem;
  overflow-wrap: anywhere;
}

.student-id,
.student-email {
  margin-bottom: 7px;
  color: #7a879c;
  font-size: 0.8rem;
  overflow-wrap: anywhere;
}

.course-tag {
  display: inline-block;
  margin-bottom: 8px;
  padding: 5px 9px;
  border-radius: 8px;
  color: #425ba7;
  background: #edf1ff;
  font-size: 0.75rem;
  font-weight: 800;
}

.delete-button {
  border: 0;
  background: transparent;
  color: #a0aabd;
  font-size: 1.5rem;
  cursor: pointer;
}

.delete-button:hover {
  color: #d45b70;
}

.empty-state {
  padding: 34px 12px;
  text-align: center;
  color: #7a879c;
}

.empty-icon {
  font-size: 2.5rem;
  margin-bottom: 12px;
}

.empty-state h3 {
  color: #34415a;
  margin-bottom: 8px;
}

.empty-state p {
  margin-bottom: 0;
  font-size: 0.9rem;
}

.footer {
  max-width: 1250px;
  margin: 26px auto 0;
  text-align: center;
  color: #8a96aa;
  font-size: 0.8rem;
}

@media (max-width: 900px) {
  .content-grid {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 600px) {
  .app-shell {
    padding: 24px 14px;
  }

  .hero-icon {
    width: 62px;
    height: 62px;
    border-radius: 20px;
    font-size: 1.8rem;
  }

  .stats-grid {
    grid-template-columns: 1fr;
  }

  .panel {
    padding: 20px;
  }

  .two-columns {
    grid-template-columns: 1fr;
  }

  .form-actions {
    flex-direction: column-reverse;
  }

  .button {
    width: 100%;
  }
}
</style>
