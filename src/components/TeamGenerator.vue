<template>
  <div class="min-h-screen bg-gray-100 py-8 px-4">
    <div class="max-w-7xl mx-auto">
      <!-- Header -->
      <header class="text-center mb-8">
        <h1 class="text-3xl font-bold text-gray-800 mb-2">Generador de Equipos de Trabajo</h1>
        <p class="text-gray-500">Organiza grupos de trabajo para proyectos y actividades escolares</p>
      </header>

      <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
        <!-- Sección Izquierda: Integrantes -->
        <div class="lg:col-span-5 space-y-4">
          <!-- Card: Importar -->
          <div class="bg-white rounded-xl shadow-sm border border-gray-200 p-5">
            <div class="flex items-center justify-between mb-4">
              <h2 class="text-lg font-semibold text-gray-700">Integrantes</h2>
              <span class="px-3 py-1 bg-blue-100 text-blue-700 text-sm font-medium rounded-full">
                {{ people.length }} registrados
              </span>
            </div>
            
            <div class="flex gap-3 flex-wrap mb-3">
              <label class="cursor-pointer flex-1">
                <input
                  type="file"
                  accept=".csv,.xlsx,.xls"
                  @change="handleFileUpload"
                  class="hidden"
                  ref="fileInput"
                />
                <span class="flex items-center justify-center gap-2 px-4 py-2.5 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-8l-4-4m0 0L8 8m4-4v12" />
                  </svg>
                  Importar
                </span>
              </label>
              <button
                v-if="people.length > 0"
                @click="clearAll"
                class="px-4 py-2.5 bg-gray-100 text-gray-600 rounded-lg hover:bg-gray-200 transition-colors"
              >
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                </svg>
              </button>
            </div>
            <p class="text-xs text-gray-400">Columnas: "Numero de Lista", "Nombre"</p>
          </div>

          <!-- Card: Agregar -->
          <div class="bg-white rounded-xl shadow-sm border border-gray-200 p-5">
            <h3 class="text-sm font-medium text-gray-500 uppercase tracking-wider mb-3">Agregar Nuevo</h3>
            <div class="flex gap-2">
              <input
                v-model="newPerson.listNumber"
                type="text"
                placeholder="#"
                class="w-20 px-3 py-2.5 border border-gray-200 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent text-center"
              />
              <input
                v-model="newPerson.name"
                type="text"
                placeholder="Nombre completo"
                class="flex-1 px-4 py-2.5 border border-gray-200 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              />
              <button
                @click="addPerson"
                class="px-5 py-2.5 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors font-medium"
              >
                +
              </button>
            </div>
            <p v-if="errorMessage" class="text-red-500 text-sm mt-2">{{ errorMessage }}</p>
          </div>

          <!-- Card: Tabla -->
          <div class="bg-white rounded-xl shadow-sm border border-gray-200 p-5">
            <h3 class="text-sm font-medium text-gray-500 uppercase tracking-wider mb-3">
              Listado de Personas
            </h3>
            <div v-if="people.length === 0" class="text-center py-8 text-gray-400">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-12 w-12 mx-auto mb-2 opacity-50" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 11-4 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z" />
              </svg>
              <p>No hay integrantes agregados</p>
            </div>
            <EasyDataTable
              v-else
              :headers="headers"
              :items="people"
              :rows-per-page="5"
              :sort-by="sortBy"
              :sort-type="sortType"
              class="custom-table"
            >
              <template #item-listNumber="{ listNumber }">
                <span class="inline-flex items-center justify-center w-8 h-6 bg-gray-100 text-gray-600 text-xs font-medium rounded">
                  {{ listNumber || '-' }}
                </span>
              </template>
              <template #item-actions="{ index }">
                <button
                  @click="removePerson(index)"
                  class="p-1.5 text-red-500 hover:bg-red-50 rounded-lg transition-colors"
                  title="Eliminar"
                >
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                  </svg>
                </button>
              </template>
            </EasyDataTable>
          </div>
        </div>

        <!-- Sección Derecha: Equipos -->
        <div class="lg:col-span-7 space-y-4">
          <!-- Card: Configuración -->
          <div class="bg-gradient-to-r from-green-600 to-emerald-600 rounded-xl shadow-lg p-5 text-white">
            <div class="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-4 mb-4">
              <div>
                <h2 class="text-lg font-semibold">Generar Equipos</h2>
                <p class="text-green-100 text-sm">{{ people.length }} personas disponibles</p>
              </div>
              <div class="flex items-center gap-3">
                <label class="text-sm font-medium">Tamaño:</label>
                <input
                  v-model.number="teamSize"
                  type="number"
                  min="1"
                  class="w-20 px-3 py-2 rounded-lg text-gray-800 text-center font-semibold focus:outline-none focus:ring-2 focus:ring-white"
                />
              </div>
            </div>
            <div class="flex gap-3 flex-wrap">
              <button
                @click="generateTeams"
                :disabled="people.length === 0 || teamSize < 1"
                class="flex-1 sm:flex-none px-6 py-2.5 bg-white text-green-700 rounded-lg hover:bg-green-50 transition-colors font-semibold disabled:opacity-50 disabled:cursor-not-allowed"
              >
                Generar
              </button>
              <button
                v-if="teams.length > 0"
                @click="exportPDF"
                class="flex-1 sm:flex-none px-6 py-2.5 bg-red-500 text-white rounded-lg hover:bg-red-600 transition-colors font-semibold flex items-center justify-center gap-2"
              >
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
                </svg>
                Exportar PDF
              </button>
            </div>
            <p v-if="teamSize < 1" class="text-red-200 text-sm mt-2">
              El tamaño debe ser al menos 1
            </p>
            <p v-else-if="people.length > 0" class="text-green-100 text-sm mt-2">
              Se crearán {{ Math.ceil(people.length / teamSize) }} equipos de ~{{ teamSize }} personas
            </p>
          </div>

          <!-- Card: Equipos Generados -->
          <div v-if="teams.length > 0" class="bg-white rounded-xl shadow-sm border border-gray-200 p-5">
            <div class="flex items-center justify-between mb-4">
              <h2 class="text-lg font-semibold text-gray-700">Equipos Generados</h2>
              <span class="px-3 py-1 bg-green-100 text-green-700 text-sm font-medium rounded-full">
                {{ teams.length }} equipos
              </span>
            </div>
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 max-h-[500px] overflow-y-auto pr-2">
              <div
                v-for="(team, index) in teams"
                :key="index"
                class="bg-gray-50 rounded-xl p-4 border border-gray-100 hover:border-green-200 hover:shadow-md transition-all"
              >
                <div class="flex items-center justify-between mb-3">
                  <h3 class="font-bold text-gray-800">Equipo {{ index + 1 }}</h3>
                  <span class="px-2 py-0.5 bg-green-100 text-green-700 text-xs font-medium rounded-full">
                    {{ team.members.length }} miembros
                  </span>
                </div>
                <ul class="space-y-1.5 mb-3">
                  <li
                    v-for="(member, memberIndex) in team.members"
                    :key="memberIndex"
                    class="flex items-center gap-2 text-sm"
                  >
                    <span class="w-5 h-5 flex items-center justify-center bg-gray-200 text-gray-600 text-xs rounded-full">
                      {{ memberIndex + 1 }}
                    </span>
                    <span class="font-medium text-gray-700">{{ member.name }}</span>
                    <span v-if="member.listNumber" class="text-gray-400 text-xs">#{{ member.listNumber }}</span>
                  </li>
                </ul>
                <div>
                  <label class="text-xs font-medium text-gray-500 uppercase tracking-wider block mb-1">
                    Tema/Actividad
                  </label>
                  <textarea
                    v-model="team.topic"
                    placeholder="Asigna un tema..."
                    class="w-full px-3 py-2 border border-gray-200 rounded-lg text-sm resize-none focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-transparent"
                    rows="2"
                  ></textarea>
                </div>
              </div>
            </div>
          </div>

          <!-- Estado Vacío -->
          <div v-else class="bg-white rounded-xl shadow-sm border border-gray-200 p-12 text-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-16 w-16 mx-auto mb-4 text-gray-300" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 11-4 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z" />
            </svg>
            <h3 class="text-lg font-medium text-gray-600 mb-1">
              {{ people.length === 0 ? 'Sin integrantes' : 'Sin equipos generados' }}
            </h3>
            <p class="text-gray-400 text-sm">
              {{ people.length === 0 ? 'Agrega personas para comenzar' : 'Configura el tamaño y genera equipos' }}
            </p>
          </div>

          <!-- Footer info -->
          <div v-if="teams.length > 0" class="text-center text-sm text-gray-400">
            <p>Equipos generados el {{ new Date().toLocaleDateString('es-MX', { dateStyle: 'full' }) }}</p>
          </div>
        </div>
      </div>

      <!-- Toast -->
      <transition name="fade">
        <div v-if="showSuccess" class="fixed bottom-6 right-6 bg-green-600 text-white px-6 py-3 rounded-xl shadow-lg flex items-center gap-3 z-50">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
          </svg>
          PDF exportado correctamente
        </div>
      </transition>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted, watch } from 'vue';
import EasyDataTable from 'vue3-easy-data-table';
import 'vue3-easy-data-table/dist/style.css';
import Papa from 'papaparse';
import { readXlsx } from 'hucre';
import jsPDF from 'jspdf';
import 'jspdf-autotable';

const STORAGE_KEY = 'teamGeneratorData';

const newPerson = reactive({
  name: '',
  listNumber: '',
});

const people = ref([]);
const teamSize = ref(4);
const teams = ref([]);
const errorMessage = ref('');
const showSuccess = ref(false);
const fileInput = ref(null);

const headers = [
  { text: '#', value: 'listNumber', sortable: true, width: 80 },
  { text: 'Nombre', value: 'name', sortable: true },
  { text: '', value: 'actions', width: 60 },
];

const sortBy = ref('');
const sortType = ref('asc');

const fisherYatesShuffle = (array) => {
  const shuffled = [...array];
  for (let i = shuffled.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
  }
  return shuffled;
};

const findColumnByName = (headers, possibleNames) => {
  for (const name of possibleNames) {
    const found = headers.find(h => 
      h.toLowerCase().includes(name.toLowerCase()) ||
      name.toLowerCase().includes(h.toLowerCase())
    );
    if (found) return found;
  }
  return null;
};

const parseFileData = (data) => {
  if (!data || data.length === 0) return [];
  
  const headers = Object.keys(data[0]);
  const nameCol = findColumnByName(headers, ['nombre', 'name']);
  const listCol = findColumnByName(headers, ['numero de lista', 'numero', 'lista', 'listnumber', 'list', 'number']);
  
  if (!nameCol) {
    errorMessage.value = 'No se encontró la columna de Nombre';
    return [];
  }

  return data.map(row => ({
    name: row[nameCol]?.toString().trim() || '',
    listNumber: listCol ? row[listCol]?.toString().trim() || '' : ''
  })).filter(p => p.name);
};

const handleFileUpload = (event) => {
  const file = event.target.files[0];
  if (!file) return;

  errorMessage.value = '';
  
  const extension = file.name.split('.').pop().toLowerCase();

  if (extension === 'csv') {
    Papa.parse(file, {
      header: true,
      skipEmptyLines: true,
      complete: (results) => {
        const parsed = parseFileData(results.data);
        if (parsed.length > 0) {
          people.value = [...people.value, ...parsed];
          saveToStorage();
        }
      },
      error: () => {
        errorMessage.value = 'Error al leer el archivo CSV';
      }
    });
  } else if (extension === 'xlsx') {
    const reader = new FileReader();
    reader.onload = async (e) => {
      try {
        const data = new Uint8Array(e.target.result);
        const workbook = await readXlsx(data);
        
        if (!workbook.sheets || workbook.sheets.length === 0) {
          errorMessage.value = 'El archivo no tiene hojas';
          return;
        }
        
        const firstSheet = workbook.sheets[0];
        const rows = firstSheet.rows || [];
        
        if (rows.length === 0) {
          errorMessage.value = 'La hoja está vacía';
          return;
        }
        
        const headers = rows[0] || [];
        
        const findCol = (possibleNames) => 
          headers.findIndex(h => 
            h != null && possibleNames.some(n => String(h).toLowerCase().includes(n))
          );
        
        const nameColIdx = findCol(['nombre', 'name']);
        const listColIdx = findCol(['numero de lista', 'numero', 'lista', 'listnumber', 'list', 'number']);
        
        if (nameColIdx === -1) {
          errorMessage.value = 'No se encontró la columna de Nombre';
          return;
        }
        
        const parsed = rows.slice(1).map(row => ({
          name: nameColIdx >= 0 && row[nameColIdx] ? String(row[nameColIdx]).trim() : '',
          listNumber: listColIdx >= 0 && row[listColIdx] ? String(row[listColIdx]).trim() : ''
        })).filter(p => p.name);
        
        if (parsed.length > 0) {
          people.value = [...people.value, ...parsed];
          saveToStorage();
        } else {
          errorMessage.value = 'No se encontraron datos válidos';
        }
      } catch (err) {
        console.error('Error reading Excel:', err);
        errorMessage.value = 'Error al leer el archivo Excel: ' + (err?.message || 'Unknown error');
      }
    };
    reader.readAsArrayBuffer(file);
  } else {
    errorMessage.value = 'Formato no soportado. Usa CSV o Excel';
  }

  event.target.value = '';
};

const addPerson = () => {
  errorMessage.value = '';
  
  if (!newPerson.name.trim()) {
    errorMessage.value = 'Por favor, ingresa el nombre';
    return;
  }

  const isDuplicate = people.value.some(
    p => p.name.toLowerCase() === newPerson.name.toLowerCase()
  );
  
  if (isDuplicate) {
    errorMessage.value = 'Esta persona ya está agregada';
    return;
  }

  people.value.push({
    name: newPerson.name.trim(),
    listNumber: newPerson.listNumber.trim()
  });

  newPerson.name = '';
  newPerson.listNumber = '';
  saveToStorage();
};

const removePerson = (index) => {
  people.value.splice(index, 1);
  saveToStorage();
};

const clearAll = () => {
  if (confirm('¿Eliminar todos los integrantes?')) {
    people.value = [];
    teams.value = [];
    saveToStorage();
  }
};

const generateTeams = () => {
  errorMessage.value = '';
  
  if (people.value.length === 0) {
    errorMessage.value = 'No hay personas para generar equipos';
    return;
  }

  if (teamSize.value < 1) {
    errorMessage.value = 'El tamaño del equipo debe ser al menos 1';
    return;
  }

  const shuffledPeople = fisherYatesShuffle(people.value);
  
  teams.value = [];
  
  const numTeams = Math.ceil(shuffledPeople.length / teamSize.value);
  const baseTeamSize = Math.floor(shuffledPeople.length / numTeams);
  const remainder = shuffledPeople.length % numTeams;

  let currentIndex = 0;
  
  for (let i = 0; i < numTeams; i++) {
    const teamSizeForThis = baseTeamSize + (i < remainder ? 1 : 0);
    const teamMembers = shuffledPeople.slice(currentIndex, currentIndex + teamSizeForThis);
    
    teams.value.push({
      members: teamMembers,
      topic: ''
    });
    
    currentIndex += teamSizeForThis;
  }

  saveToStorage();
};

const exportPDF = () => {
  const doc = new jsPDF();
  
  doc.setFontSize(20);
  doc.setTextColor(40, 40, 40);
  doc.text('Equipos de Trabajo', 14, 22);
  
  doc.setFontSize(10);
  doc.setTextColor(100, 100, 100);
  doc.text(`Fecha: ${new Date().toLocaleDateString('es-MX')}`, 14, 32);
  doc.text(`Total de equipos: ${teams.value.length}  |  Total de integrantes: ${people.value.length}`, 14, 38);

  let yPosition = 48;

  teams.value.forEach((team, index) => {
    if (yPosition > 260) {
      doc.addPage();
      yPosition = 20;
    }

    doc.setFontSize(14);
    doc.setTextColor(0, 120, 0);
    doc.text(`Equipo ${index + 1}`, 14, yPosition);
    
    doc.setFontSize(10);
    doc.setTextColor(80, 80, 80);
    doc.text(`(${team.members.length} miembros)`, 45, yPosition);
    
    yPosition += 6;
    
    const memberList = team.members.map((m, i) => 
      `${i + 1}. ${m.name}${m.listNumber ? ` (#${m.listNumber})` : ''}`
    );
    
    doc.setFontSize(10);
    doc.setTextColor(0, 0, 0);
    
    memberList.forEach(member => {
      doc.text(member, 20, yPosition);
      yPosition += 5;
    });

    if (team.topic) {
      yPosition += 2;
      doc.setFontSize(10);
      doc.setTextColor(100, 100, 100);
      doc.text(`Tema: ${team.topic}`, 20, yPosition);
      yPosition += 5;
    }

    yPosition += 10;
  });

  doc.save(`equipos_trabajo_${new Date().toISOString().split('T')[0]}.pdf`);
  
  showSuccess.value = true;
  setTimeout(() => {
    showSuccess.value = false;
  }, 3000);
};

const saveToStorage = () => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify({
    people: people.value,
    teams: teams.value,
    teamSize: teamSize.value
  }));
};

const loadFromStorage = () => {
  const stored = localStorage.getItem(STORAGE_KEY);
  if (stored) {
    try {
      const data = JSON.parse(stored);
      people.value = data.people || [];
      teams.value = data.teams || [];
      teamSize.value = data.teamSize || 4;
    } catch {
      localStorage.removeItem(STORAGE_KEY);
    }
  }
};

onMounted(() => {
  loadFromStorage();
});

watch([people, teams, teamSize], () => {
  saveToStorage();
}, { deep: true });
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s, transform 0.3s;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateY(10px);
}

:deep(.vue3-easy-data-table__footer) {
  font-size: 0.75rem;
}

:deep(.vue3-easy-data-table__body) {
  font-size: 0.875rem;
}

:deep(.vue3-easy-data-table__header) {
  font-size: 0.75rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}
</style>
