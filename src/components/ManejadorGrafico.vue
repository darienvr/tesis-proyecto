<template>
    <div class="w-full border-2 relative border-[#002B6B] ">
    
      <div class="w-[95%] mx-auto py-2">
        <div class="w-[85%] flex justify-between float-right text-xs font-light py-1">
    
          <div class="flex gap-3">
            <div class="flex gap-2">
              <p class="w-[15px] h-[15px] bg-[#AAAAAA]"></p>
              <p>No Aplica</p>
            </div>
            <div class="flex gap-2">
              <p class="w-[15px] h-[15px] bg-[#85D254]"></p>
              <p>Finalizados</p>
            </div>
          </div>
    
          <div class="flex gap-3">
            <div class="flex gap-1 items-center">
              <i class="fa-solid fa-circle-exclamation"></i>          
              <p>La orientación y el orden no seran editables tras añadir datos a las secciones</p>
            </div>
    
            <div class="flex gap-2">
              <p class="w-[15px] h-[15px] bg-[#DEE4EB]"></p>
              <p>Paños</p>
            </div>
    
            <div class="flex gap-2">
              <p class="w-[15px] h-[15px] bg-[#D4E3FC]"></p>
              <p>Niveles</p>
            </div>
          </div>
        </div>
      </div>
    
    <!-- CONTENIDO CENTRAL -->
    
      <div class="w-[95%] mx-auto">
          <div class="p-2 relative border border-dashed border-gray-400 w-full h-[1300px] flex items-center justify-center">
             <div class="absolute inset-0">
                <svg class="w-full h-full" xmlns="http://www.w3.org/2000/svg">
                   <line x1="0" y1="0" x2="100%" y2="100%" stroke="#999" stroke-dasharray="5,5" />
                   <line x1="100%" y1="0" x2="0" y2="100%" stroke="#999" stroke-dasharray="5,5" />
                </svg>
             </div>
             <div
             :style="{
                ...shapeClasses,
                transform: `scale(${zoomLevel / 160})`,
                transformOrigin: 'center'
              }"
                class="modalSecondary p-5 bg-[#DEE4EB]  h-full flex flex-col items-center justify-center relative"
              >
                <div
                  class="bg-white z-10 flex flex-col justify-between p-4 border border-gray-300 shadow-md rounded-md 
                        w-full h-[95%] max-w-[90%] overflow-auto"
                  style="word-wrap: break-word;">
                  <div class="flex items-center gap-2 mb-2">
                    <i class="fa-solid fa-chart-bar text-black"></i>
                    <p class="font-semibold truncate" :style = "{ fontSize: dynamicCentral }">Resumen</p>
                  </div>
                  <div v-for="(nivel, index) in nivelesComputed" :key="index" class="mb-2 font-normal flex-col justify-between">
                    <p class="text-sm font-normal truncate" :style = "{ fontSize: dynamicCentral }">
                      {{ nivel.nombre }}
                      <span :class="nivel.actual >= nivel.total ? 'text-green-500' : 'text-orange-500'">
                        {{ Math.round((nivel.actual / nivel.total) * 100) }}%
                      </span>
                    </p>
                    <p class="text-xs text-gray-600 truncate"  :style = "{ fontSize: dynamicCentral }">
                      Concreto {{ nivel.actual }} / {{ nivel.total }}
                    </p>
                    <div class="w-full bg-gray-200 rounded-full h-2.5 mt-1 relative">
                      <div v-if="Math.round((nivel.actual / nivel.total) * 100)"
                        :style="{ width: (nivel.actual / nivel.total) * 100 + '%' }"
                        :class="nivel.actual >= nivel.total ? 'bg-green-500' : 'bg-orange-500'"
                        class="h-2.5 rounded-full transition-all duration-500"
                      ></div>
                    </div>
                  </div>
    
                  <hr class="my-2 border-gray-300" />
    
                  <div>
                    <p class="text-sm font-normal truncate" :style = "{ fontSize: dynamicCentral }">
                      TOTAL <span class="text-orange-500">{{ totalPorcentaje }}%</span>
                    </p>
                    <p class="text-xs text-gray-600 font-normal truncate" :style = "{ fontSize: dynamicCentral }">
                      Concreto {{ totalActual }} / {{ totalMaximo }}
                    </p>
                    <div class="w-full bg-gray-200 rounded-full h-2.5 mt-1 relative">
                      <div
                        :style="{ width: totalPorcentaje + '%' }"
                        :class="totalPorcentaje === 100 ? 'bg-green-500' : 'bg-orange-500'"
                        class="h-2.5 rounded-full transition-all duration-500"
                      ></div>
                    </div>
                  </div>
                </div>
    
             <!-- Sección Derecha -->
              
              <div v-if="secciones[3].niveles && secciones[3].banos" ref="SeccionDerecha" class="absolute z-40   font-normal" :style="{ 
                  left: (widthShape+10) + computedBottom + 'px', 
                  top: computedBottom + 'px' ,
                  fontSize: (12 * 150/zoomLevel) + 'px',
                  ...(seccionesMasBanosHorizontal.includes(secciones[3].nombre) ? { height: heightShape + 'px' } : {})
                }">
                <p class=" vertical-text absolute text-center font-normal " :style="{ 
                  left: rightWidth + 'px', 
                  top: computedBottom + 'px' 
                }">{{secciones[3].direccion}} </p>
                <table class="styled-table border-collapse border border-gray-500 z40"
                :class="{ 'h-full': seccionesMasBanosHorizontal.includes(secciones[3].nombre) }"
                >
                  <thead>
                    <tr>
                      <th class="border border-gray-500 px-2 py-1  font-normal" :style="{ fontSize: dynamicFontSize }">-</th>
                      <th v-for="(nivel, index) in secciones[3].niveles" :key="'der-nivel-' + index" class="border border-gray-500 px-2 py-1  font-normal" :style="{ fontSize: dynamicFontSize }">
                        {{ index + 1 }}
                      </th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="(pano, indexP) in secciones[3].banos" :key="'der-pano-' + indexP">
                      <td class="border border-gray-500 px-2 py-1   font-normal"
                      :style="{ height: (heightShape / maxBanosHorizontal)- (38/ maxBanosHorizontal)  + 'px',
                      fontSize: dynamicFontSize
                       }"
                      >{{ indexP + 1 }}</td>
                      <td v-for="(_, indexN) in secciones[3].niveles" :key="'der-cell-' + indexN" class="border border-gray-500 px-2 py-1 font-normal"
                      :style="{ height: (heightShape / maxBanosHorizontal)- (38/ maxBanosHorizontal)  + 'px',
                      fontSize: dynamicFontSize
                      }"
                      @mousedown="iniciarSeleccion('derecha', indexP, indexN)"
                      @mousemove="actualizarSeleccion2(indexP, indexN)"
                      @mouseup="finalizarSeleccion($event)"
                      :class="[
                        getColor('derecha', indexP, indexN), 
                        { 'resaltado': seleccionMultiple.some(c => c.fila === indexP && c.columna === indexN && c.seccion === secciones[3].nombre) }
                      ]">
                      {{ getNumeroDerecha(indexN, indexP) }}{{ getColor('derecha', indexP, indexN) }}
    
    
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
    
              <div v-if="secciones[3].banos < maxBanosHorizontal"  class="absolute z-40   font-normal bg-gray-400 w-full" :style="{ left: (widthShape+10) + 'px', 
              top: secciones[3].banos * ((heightShape / maxBanosHorizontal)- (38 / maxBanosHorizontal)) + 45 + 'px',
              height: heightShape - (secciones[3].banos * ((heightShape / maxBanosHorizontal)- (38 / maxBanosHorizontal)) + 45) + 'px',
              width: widthSeccionDerecha + 'px' ,
               }">
              </div>
    
              <!-- Sección Izquierda -->
              <div ref="SeccionIzquierda" v-if="secciones[2].niveles && secciones[2].banos" class="absolute z-40   font-normal" :style="{ right: (widthShape+10) + 'px', 
              top: (computedTop ) + 'px', height: heightShape + 'px',
              ...(seccionesMasBanosHorizontal.includes(secciones[2].nombre) ? { height: heightShape + 'px' } : {})
              }">
    
              <p class=" vertical-textLeft absolute text-center font-normal " :style="{ 
                  right: leftWidth + 'px', 
                  top: computedBottom + 'px',
                  fontSize: (12 * 150/zoomLevel) + 'px' 
                }">{{secciones[2].direccion}} </p>            
                <table class="styled-table border-collapse border border-gray-500 "
                :class="{ 'h-full': seccionesMasBanosHorizontal.includes(secciones[2].nombre) }">
                  <thead>
                    <tr>
                      <th class="border border-gray-500 px-2 py-1   font-normal"  :style="{ fontSize: dynamicFontSize }">-</th>
                      <th v-for="(nivel, index) in secciones[2].niveles" :key="'izq-nivel-' + index" class="border border-gray-500 px-2 py-1   font-normal"  :style="{ fontSize: dynamicFontSize }">
                        {{ secciones[2].niveles - index }}
                      </th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="(pano, indexP) in secciones[2].banos" :key="'izq-pano-' + indexP">
                      <td class="border border-gray-500 px-2 py-1   font-normal" 
                      :style="{ height: (heightShape / maxBanosHorizontal) - (38 / maxBanosHorizontal)  + 'px', fontSize: dynamicFontSize}"
                      >{{ indexP + 1 }}</td>
                      <td v-for="(_, indexN) in secciones[2].niveles" :key="'izq-cell-' + indexN" class=" border cursor-pointer border-gray-500 px-2 py-1   font-normal"
                      @mousedown="iniciarSeleccion('izquierda', indexP, indexN)"
                      @mousemove="actualizarSeleccion2(indexP, indexN)"
                      @mouseup="finalizarSeleccion($event)"
                      :class="[
                        getColor('izquierda', indexP, indexN), 
                        { 'resaltado': seleccionMultiple.some(c => c.fila === indexP && c.columna === indexN  && c.seccion === secciones[2].nombre) }
                      ]"
                      :style="{ height: (heightShape / maxBanosHorizontal) - (38 / maxBanosHorizontal)  + 'px' ,
                      fontSize: dynamicFontSize
                      }"
                      >
                      {{ getNumeroIzquierda(indexN, indexP) }}
                    </td>
                    </tr>
                  </tbody>
                </table>
              </div>
              <div v-if="secciones[2].banos < maxBanosHorizontal"  class="absolute z-40   font-normal bg-gray-400 w-full" :style="{ right: (widthShape+10) + 'px', 
              top: secciones[2].banos * ((heightShape / maxBanosHorizontal)- (38 / maxBanosHorizontal)) + 45 + 'px',
              height: heightShape - (secciones[2].banos * ((heightShape / maxBanosHorizontal)- (38 / maxBanosHorizontal)) + 45) + 'px',
              width: widthSeccionIzquierda+ 'px' ,
               }">
              </div>
    
              <!-- Sección Superior -->
              <div ref="SeccionSuperior" v-if="secciones[1].niveles && secciones[1].banos" class="absolute z-40   font-normal" :style="{ bottom: (heightShape + 10)+ 'px', left: 0,
                ...(seccionesMasBanosVertical.includes(secciones[1].nombre) ? { width: widthShape + 'px' } : {})
               }">
                <p class="text-left font-normal pt-1" :style="{ fontSize: (12 * 140/zoomLevel) + 'px'}">{{secciones[1].direccion}} </p>
                <table class="reverse-table border-collapse border border-gray-500"
                :class="{ 'w-full': seccionesMasBanosVertical.includes(secciones[1].nombre) }">
                  <thead>
                    <tr>
                      <th class="border border-gray-500 px-2 py-1   font-normal" :style="{ fontSize: dynamicFontSize }">-</th>
                      <th v-for="(pano, index) in secciones[1].banos" :key="'sup-pano-' + index" class="border border-gray-500 px-2 py-1   font-normal" :style="{ fontSize: dynamicFontSize }">
                        {{ index + 1 }}
                      </th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="(nivel, indexN) in secciones[1].niveles" :key="'sup-nivel-' + indexN">
                      <td class="border border-gray-500 px-2 py-1   font-normal"
                      :style="{ fontSize: dynamicFontSize }">{{ secciones[1].niveles - indexN }}</td>
                      <td v-for="(_, indexP) in secciones[1].banos" :key="'sup-cell-' + indexP" class="border border-gray-500 px-2 py-1   font-normal"
                         @mousedown="iniciarSeleccion('superior', indexN, indexP)"
                          @mousemove="actualizarSeleccion2(indexN, indexP)"
                          @mouseup="finalizarSeleccion($event)"
                          :class="[
                            getColor('superior', indexN, indexP), 
                            { 'resaltado': seleccionMultiple.some(c => c.fila === indexN && c.columna === indexP &&  c.seccion === secciones[1].nombre) }
                          ]"
                          :style="{ width: (widthShape / maxNivelesVertical)- (38 / maxNivelesVertical)  + 'px' ,
                          fontSize: dynamicFontSize
                          }"
                        >
    
                        {{ getNumeroSuperior(indexN, indexP) }}
    
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
    
              <div v-if="secciones[1].banos < maxNivelesVertical"  class="absolute z-40   font-normal bg-gray-400 w-full" :style="{ bottom: (heightShape + 10)+ 'px', 
              left: secciones[1].banos * ((widthShape / maxNivelesVertical)- (38 / maxNivelesVertical)) + 45 + 'px',
              width: widthShape - (secciones[1].banos * ((widthShape / maxNivelesVertical)- (38 / maxNivelesVertical)) + 45) + 'px',
              height: heightSeccionSuperior - 25 + 'px' ,
               }">
              
              </div>
    
             <!-- Sección Inferior -->
    
              <div ref="SeccionInferior" v-if="secciones[0].niveles && secciones[0].banos" class="absolute z-40   font-normal" :style="{
                  top: (heightShape + 10) + 'px', left: 0,
                  ...(seccionesMasBanosVertical.includes(secciones[0].nombre) ? { width: widthShape + 'px' } : {})
                }">
                <table class="reverse-table border-collapse border border-gray-500   font-normal"
                :class="{ 'w-full': seccionesMasBanosVertical.includes(secciones[0].nombre) }">
                  <thead>
                    <tr>
                      <th class="border border-gray-500 px-2 py-1   font-normal" :style="{ fontSize: dynamicFontSize }">-</th>
                      <th v-for="(pano, index) in secciones[0].banos" :key="'inf-pano-' + index" class="border border-gray-500 px-2 py-1   font-normal" :style="{ fontSize: dynamicFontSize }">
                        {{ index + 1 }}
                      </th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="(nivel, indexN) in secciones[0].niveles" :key="'inf-nivel-' + indexN">
                      <td class="border border-gray-500 px-2 py-1  font-normal"
                      :style="{ fontSize: dynamicFontSize }">{{ indexN + 1 }}</td>
                      <td v-for="(_, indexP) in secciones[0].banos" :key="'inf-cell-' + indexP" class="border border-gray-500 px-2 py-1   font-normal"
                      @mousedown="iniciarSeleccion('inferior', indexN, indexP)"
                      @mousemove="actualizarSeleccion2(indexN, indexP)"
                      @mouseup="finalizarSeleccion($event)"
                      :class="[
                        getColor('inferior', indexN, indexP), 
                        { 'resaltado': seleccionMultiple.some(c => c.fila === indexN && c.columna === indexP && c.seccion === secciones[0].nombre) }
                      ]"
                      :style="{ width: (widthShape / maxNivelesVertical)- (38 / maxNivelesVertical)  + 'px' ,
                      fontSize: dynamicFontSize
                      }"
                      >
                      <!-- {{ getNumeroInferior(indexN, indexP) }} -->
                      {{ 1 }}
                      </td>
                    </tr>
                  </tbody>
                </table>
                <p class="text-left font-normal pt-1" :style="{ fontSize: (12 * 140/zoomLevel) + 'px'}">{{secciones[0].direccion}} </p>
              </div>
    
    
              <div v-if="secciones[0].banos < maxNivelesVertical"  class="absolute z-40  text-sm font-normal bg-gray-400 w-full" :style="{ top: (heightShape + 10)+ 'px', 
              left: secciones[0].banos * ((widthShape / maxNivelesVertical)- (38 / maxNivelesVertical)) + 45 + 'px',
              width: widthShape - (secciones[0].banos * ((widthShape / maxNivelesVertical)- (38 / maxNivelesVertical)) + 45) + 'px',
              height: heightSeccionInferior - 25 + 'px' ,
               }">
    
              </div>
              
              <transition name="fade">
              <div v-if="mostrarModal"   class="absolute z-40 bg-white flex flex-col p-1 gap-1 rounded shadow-lg"
              :style="{ top: modalPosition.top + 'px', left: modalPosition.left + 'px' }" >
                <div class="flex flex-col gap-1 items-center">
                  <button v-for="opcion in opcionesOption" :key="opcion.valor"
                    @click="aplicarEstado(opcion.valor)" class="flex gap-1 items-center text-sm">
                    {{ opcion.texto }} <span :class="opcion.icono">o</span> 
                  </button>
                </div>
              </div>
            </transition>
            </div>
          </div>
    
          <div class="mb-2 flex items-center gap-2">
            <label>Zoom:</label>
            <input
              type="range"
              min="50"
              max="200"
              v-model="zoomLevel"
              class="w-40"
            />
            <span>{{ zoomLevel }}%</span>
          </div>
    
       </div>
    </div>
    </template>
     
     <script>
     export default {
       name: "ManejadorGrafico",
       props: {
          secciones: Array,
          opciones: Array,
          opcionesOption: Array,
      },
       data() {
         return {
          showModal: false,
          tempValue: null,
          tempField: null,
          permitirEdicion : {},
          tempSeccion: null,
          temporizadorEdicion: {},  
          temporizadores: {},
          temporizadorModal: {},
          permitirEdicion: {}, 
          modalActive: false,
          modalCooldown : false,
          permitirBajarSinModal: {} ,
          lastFocusedInput: null,
           heightSeccionSuperior: 0, 
           heightSeccionInferior:0,
           widthSeccionDerecha:0,
           widthSeccionIzquierda: 0,
           isShapeExpanded: false,
           isHourOrientation: false,
           isOrder: false,
           selectedShape: "vertical",
           widthShape: 0,
           newChangeTable: {
              tabla: "",
              desde: "",
              hasta: "",
              option: "",
            },
           heightShape: 0,
           selectedOrientation: "",
           modalPosition: { top: 0, left: 0 },
           opcionesCelda: ["superior", "inferior", "izquierda", "derecha"],
           shapes: [
             { label: "Rectángulo Vertical", value: "vertical" },
             { label: "Rectángulo Horizontal", value: "horizontal" },
             { label: "Cuadrado", value: "square" }
           ],
  
          mostrarOpcionesOption: false,
          rightWidth: 0,
          leftWidth: 0,
  
          niveles: [
            { nombre: "NIVEL 1", actual: 15, total: 23 },
            { nombre: "NIVEL 2", actual: 37, total: 37 },
            { nombre: "NIVEL 3", actual: 20, total: 37 },
    
          ],
          seleccion: {
            derecha: [],
            izquierda: [],
            superior: [],
            inferior: []
          },
          arrastrando: false,
          opcionesBase: ["Superior", "Inferior", "Izquierda", "Derecha"],
          modalAbierto: false,
          seleccionMultiple: [], 
          seleccionando: false,
          seccionActual: '',
          seccionSeleccionada: null,
         mostrarModal: false,
          celdaInicio: null,
          indiceSeleccionado: null,
          isOption: false,
          celdasResaltadas: [], 
          mostrarOpcionesCelda: false,
          mostrarModal: false,
          modalVisible: false,
          newChangeTable:{
            tabla: "",
            desde: "",
            hasta: "",
            option: ""
          },
          zoomLevel: 100 
         };
       },
       computed: {
        dynamicFontSize() {
          const baseZoom = this.zoomLevel >= 90 ? 120 : 100;
          return (12 * baseZoom / this.zoomLevel) + 'px';
        },
        dynamicCentral() {
          const baseZoom = this.zoomLevel >= 90 ? 120 : 90;
          return (12 * baseZoom / this.zoomLevel) + 'px';
        },
        nivelesComputed(){
          const resumenNiveles = {};
    
          this.secciones.forEach((seccion) => {
            const usaBanosComoTotal = ["superior", "inferior", "derecha", "izquierda"].includes(seccion.nombre);
            const totalPorNivel = usaBanosComoTotal ? seccion.banos : seccion.niveles;
    
            for (let i = 0; i < seccion.niveles; i++) {
              const nivelKey = `Nivel ${i + 1}`;
              if (!resumenNiveles[nivelKey]) {
                resumenNiveles[nivelKey] = { nombre: `NIVEL ${i + 1}`, actual: 0, total: 0 };
              }
              resumenNiveles[nivelKey].total += totalPorNivel;
            }
    
            Object.entries(seccion.seleccion).forEach(([fila, columnas]) => {
              Object.entries(columnas).forEach(([col, estado]) => {
                if (estado === "completo") {
                  let nivel = 0;
    
                  if (seccion.nombre === "superior") {
                    nivel = seccion.niveles - 1 - parseInt(fila);
                  } else if (seccion.nombre === "izquierda") {
                    nivel = seccion.niveles - 1 - parseInt(col);
                  } else if (seccion.nombre === "inferior") {
                    nivel = parseInt(fila);
                  } else if (seccion.nombre === "derecha") {
                    nivel = parseInt(col);
                  }
    
                  const nivelKey = `Nivel ${nivel + 1}`;
                  if (resumenNiveles[nivelKey]) {
                    resumenNiveles[nivelKey].actual++;
                  }
                }
              });
            });
          });
          const nivelesOrdenados = Object.values(resumenNiveles).sort(
            (a, b) => parseInt(a.nombre.split(" ")[1]) - parseInt(b.nombre.split(" ")[1])
          );
  
          const cantidadNiveles = nivelesOrdenados.length;
  
          if (cantidadNiveles > 12) {
            this.zoomLevel = 55;
          } else if (cantidadNiveles >= 9 && cantidadNiveles <= 12) {
            this.zoomLevel = 70;
          } else if (cantidadNiveles === 8) {
            this.zoomLevel = 75;
          } else if (cantidadNiveles === 7) {
            this.zoomLevel = 85;
          } else {
            this.zoomLevel = 100; // Valor por defecto
          }
  
          return nivelesOrdenados;
        },
        totalActual() {
          return this.nivelesComputed.reduce((sum, nivel) => sum + nivel.actual, 0);
        },
        totalMaximo() {
          return this.secciones.reduce((sum, seccion) => {
            const esVertical = ["derecha", "izquierda"].includes(seccion.nombre);
            return sum + (esVertical ? seccion.banos * seccion.niveles : seccion.banos * seccion.niveles);
          }, 0);
        },
        totalPorcentaje() {
          return this.totalMaximo ? Math.round((this.totalActual / this.totalMaximo) * 100) : 0;
        },
      
        shapeClasses() {
        const baseHeight = 50; // Altura base en píxeles
        const extraHeightPerLevel = 24; // Altura adicional por cada nivel
        let height = baseHeight + this.nivelesComputed.length * extraHeightPerLevel;
    
        if (this.selectedShape === "vertical") {
          this.widthShape = height * 2.8;
          this.heightShape = height * 3.3;
          return {
            width: `${height * 2.8}px`,
            height: `${height * 3.3}px`
          };
        }
    
        if (this.selectedShape === "horizontal") {
          this.widthShape = height * 3.5;
          this.heightShape = height * 3.1;
          return {
            width:  `${height * 3.5}px`,
            height: `${height * 3.1}px`
          };
        }
    
        if (this.selectedShape === "square") {
          this.widthShape = height * 3.1;
          this.heightShape = height * 3.1;
          return {
            width: `${height * 3.1}px`,
            height: `${height * 3.1}px`
          };
        }
    
        return {};
      },
    
      computedBottom() {
        if (this.$refs.SeccionDerecha) {
          return this.heightShape - this.$refs.SeccionDerecha.offsetHeight;
        }
        return 0; 
      },
      computedTop() {
        if (this.$refs.SeccionIzquierda) {
          return this.heightShape - this.$refs.SeccionIzquierda.offsetHeight;
        }
        return 0; 
      },
        opcionesDisponibles() {
          const valoresUsados = this.opciones.map((item) => item.valor);
          return this.opcionesBase.filter((opcion) => !valoresUsados.includes(opcion));
        },
        banosDerecha() {
          return this.secciones?.[3]?.banos ?? 0;
        },
        seccionesMasBanosVertical() {
          const verticales = this.secciones.filter(sec => sec.nombre === "inferior" || sec.nombre === "superior");
          const maxBanos = Math.max(...verticales.map(sec => sec.banos || 0));
    
          return verticales
            .filter(sec => sec.banos === maxBanos)
            .map(sec => sec.nombre);
        },
    
        seccionesMasBanosHorizontal() {
          const horizontales = this.secciones.filter(sec => sec.nombre === "izquierda" || sec.nombre === "derecha");
          const maxBanos = Math.max(...horizontales.map(sec => sec.banos || 0));
    
          return horizontales
            .filter(sec => sec.banos === maxBanos)
            .map(sec => sec.nombre);
        },
            maxBanosHorizontal() {
            const banosIzq = this.secciones.find(sec => sec.nombre === "izquierda")?.banos || 0;
            const banosDer = this.secciones.find(sec => sec.nombre === "derecha")?.banos || 0;
            return Math.max(banosIzq, banosDer);
          },
    
          maxNivelesVertical() {
            const nivelesSup = this.secciones.find(sec => sec.nombre === "superior")?.banos || 0;
            const nivelesInf = this.secciones.find(sec => sec.nombre === "inferior")?.banos || 0;
            return Math.max(nivelesSup, nivelesInf);
          },
          numeracionInicial() {
            let orden = this.opciones.map(op => op.valor.toLowerCase());
          
            let seccionesData = {};
            for (let sec of this.secciones) {
                seccionesData[sec.nombre] = sec;
            }
  
            let numeracion = {};
            let ultimoNumero = 1; 
  
            for (let nombre of orden) {
                let seccion = seccionesData[nombre] || { niveles: 0, banos: 0 };
                let niveles = seccion.niveles;
                let banos = seccion.banos;
                let seleccion = seccion.seleccion || {};
  
                if (nombre === "inferior") {
                let numeracionInferior = [];
                let numeroBase = ultimoNumero; 
                let totalNumerosAsignados = 0; 
  
                for (let i = 0; i < niveles; i++) {
                  let fila = new Array(banos).fill(null);
                  let numerosDisponibles = [];
                  let contadorNumeros = 0;
  
                  for (let j = 0; j < banos; j++) {
                      if (!(seleccion[i] && seleccion[i][j] === "no_aplica")) {
                          numerosDisponibles.push(`${i + 1}.${numeroBase + contadorNumeros}`);
                          contadorNumeros++;
                      }
                  }
                  
  
                  let indexNum = 0;
                  for (let j = 0; j < banos; j++) {
                    let targetIndex = this.selectedOrientation === 'Horario' ? banos - 1 - j : j;
  
                    if (seleccion[i] && seleccion[i][j] === "no_aplica") {
                        fila[targetIndex] = null; // Mantener los "no_aplica"
                    } else {
                        if (numerosDisponibles[indexNum] !== undefined) {
                            fila[targetIndex] = numerosDisponibles[indexNum];
                            indexNum++;
                        } else {
                            fila[targetIndex] = null;
                        }
                    }
                } 
                  if (this.selectedOrientation === 'Horario') {
                      fila.reverse();
                  }
              
                  numeracionInferior.push(fila);
                  totalNumerosAsignados += contadorNumeros;
              }
  
                numeracion[nombre] = numeracionInferior;
                ultimoNumero = numeroBase + totalNumerosAsignados;
            } else {
                    numeracion[nombre] = ultimoNumero;
                    ultimoNumero += banos;
                }
            }
  
            console.log("Para la orientación", this.selectedOrientation, "el resultado es", numeracion);
              return numeracion;
          },
    
       opcionesCompletas() {
            return this.opciones.every(opcion => opcion.valor.trim() !== "");
        },
    
      },
       methods: {
        
        iniciarSeleccion(seccion, fila, columna) {
          this.seleccionando = true;
          this.celdaInicio = { fila, columna }; 
          this.seleccionMultiple = [{ fila, columna }];
          this.seccionSeleccionada = seccion;
        },
    
      actualizarSeleccion2(fila, columna) {
        if (this.seleccionando) {
          const inicio = this.celdaInicio;
          this.seleccionMultiple = [];
    
          // Define los límites del área seleccionada
          const minFila = Math.min(inicio.fila, fila);
          const maxFila = Math.max(inicio.fila, fila);
          const minColumna = Math.min(inicio.columna, columna);
          const maxColumna = Math.max(inicio.columna, columna);
          console.log("Sección seleccionada es:" , this.seccionSeleccionada)
          // Selecciona todas las celdas dentro del área rectangular
          for (let f = minFila; f <= maxFila; f++) {
            for (let c = minColumna; c <= maxColumna; c++) {
              this.seleccionMultiple.push({ fila: f, columna: c, seccion: this.seccionSeleccionada  });
            }
          }
        }
      },
      finalizarSeleccion(event) {
      this.seleccionando = false;
    
      if (this.seleccionMultiple.length > 0) {
        this.mostrarModal = true; 
    
        // Obtener la celda de la última posición seleccionada
        const ultimaCelda = this.seleccionMultiple[this.seleccionMultiple.length - 1];
    
        if (event && event.target) { 
          const container = event.target.closest('.relative');
          if (!container) return; 
    
          const rectTd = event.target.getBoundingClientRect();
          const rectContainer = container.getBoundingClientRect(); 
    
          this.modalPosition = {
            top: (rectTd.bottom - rectContainer.top + 5) * (140 / this.zoomLevel),
            left: (rectTd.left - rectContainer.left) * (140 / this.zoomLevel),
          };
        }
      }
    },
    
        aplicarEstado(valor) {
          const nuevaSeccion = JSON.parse(JSON.stringify(this.secciones)); // Clonar la prop localmente
  
          this.seleccionMultiple.forEach(({ fila, columna }) => {
            const seccion = nuevaSeccion.find(s => s.nombre === this.seccionSeleccionada);
            if (seccion) {
              if (!seccion.seleccion[fila]) seccion.seleccion[fila] = {}; // Manejar inicialización si es undefined
              seccion.seleccion[fila][columna] = valor;
            }
          });
          console.log(nuevaSeccion)
          this.$emit('actualizar-secciones', nuevaSeccion); 
          this.mostrarModal = false;
          this.seleccionMultiple = [];
        },
        toggleSection(section) {
          this[section] = !this[section];
        },
        updateRightWidth() {
          if (this.$refs.SeccionDerecha) {
            this.rightWidth = this.$refs.SeccionDerecha.offsetWidth;
            console.log("Ancho de sección derecha:", this.rightWidth);
          }
        },
        updateLeftWidth() {
          if (this.$refs.SeccionIzquierda) {
            this.leftWidth = this.$refs.SeccionIzquierda.offsetWidth;
            console.log("Ancho de sección derecha:", this.leftWidth);
          }
        },

        seleccionar(estado) {
          if (!this.celdaSeleccionada) return;
    
          const { seccion, fila, columna } = this.celdaSeleccionada;
    
          const seccionEncontrada = this.secciones.find(sec => sec.nombre === seccion);
    
          if (!seccionEncontrada) {
            console.error(` Sección "${seccion}" no encontrada.`);
            return;
          }
    
          // Asegurar que seleccion existe
          if (!seccionEncontrada.seleccion) seccionEncontrada.seleccion = [];
          if (!seccionEncontrada.seleccion[fila]) seccionEncontrada.seleccion[fila] = {};
          seccionEncontrada.seleccion[fila][columna] = estado;
    
          this.modalVisible = false;
        },
        cambiarOrientacion(orientacion) {
            if (this.selectedOrientation !== orientacion) {
              this.selectedOrientation = orientacion;
              this.invertirSeleccion();
            }
        },
        getColor(seccion, fila, columna) {
          // Buscar la sección correcta dentro de this.secciones
          const seccionEncontrada = this.secciones.find(sec => sec.nombre === seccion);
    
          // Si la sección no existe, retornar vacío
          if (!seccionEncontrada) {
            console.error(` Sección "${seccion}" no encontrada.`);
            return '';
          }

          // Acceder a la selección de la sección encontrada
          const estado = seccionEncontrada.seleccion?.[fila]?.[columna];
          return estado === 'completo' ? 'bg-green-500' 
              : estado === 'no_aplica' ? 'bg-gray-400' 
              : '';
        },

        invertirSeleccion() {
        this.secciones.forEach((seccion) => {
          if (seccion.nombre) {
            console.log("Ejecutando inversión para:", seccion.nombre);
    
            if (!seccion.seleccion || Object.keys(seccion.seleccion).length === 0) return;
    
            const esHorizontal = ["izquierda", "derecha"].includes(seccion.nombre);
            const esVertical = ["superior", "inferior"].includes(seccion.nombre);
    
            if (esHorizontal) {
              const claves = Object.keys(seccion.seleccion).map(Number).sort((a, b) => a - b);
              const nuevaSeleccion = {};
    
              claves.forEach((fila, index) => {
                const nuevaFila = claves[claves.length - 1 - index]; 
                nuevaSeleccion[nuevaFila] = { ...seccion.seleccion[fila] }; 
              });
    
              seccion.seleccion = JSON.parse(JSON.stringify(nuevaSeleccion));
    
            } else if (esVertical) {
              Object.keys(seccion.seleccion).forEach(fila => {
                const valores = Object.entries(seccion.seleccion[fila]) 
                  .map(([columna, valor]) => [Number(columna), valor])
                  .sort((a, b) => a[0] - b[0]) // Ordenar por clave
                  .reverse(); // Invertir el orden
    
                // Reconstruir el objeto fila con las claves ajustadas
                const nuevaFila = {};
                valores.forEach(([columna, valor], index) => {
                  nuevaFila[index] = valor; 
                });
    
                seccion.seleccion[fila] = nuevaFila;
              });
            }
    
            console.log("Después de la inversión:", seccion.seleccion);
          }
        });
        },

        getNumberDerecha(nivel,bano){
          if(this.selectedOrientation == "Horario"){
            return `${nivel}.${bano}`
          } else {
            const result = this.banosDerecha - bano
            return `${nivel}.${result+1}`
          }
        },

        toggleExpand(index) {
            const opcionesCompletas = this.opciones.every(opcion => opcion.valor.trim() !== "");
    
            if (!opcionesCompletas) {
                //alert("Debes completar todas las opciones de orden antes de acceder a las secciones.");
            return;
            }
          this.secciones[index].expandido = !this.secciones[index].expandido;
        },

        getShapeStyle(shape) {
          switch (shape) {
            case "vertical":
              return { width: "30px", height: "60px" };
            case "horizontal":
              return { width: "60px", height: "30px" };
            case "square":
              return { width: "40px", height: "40px" };
            default:
              return {};
          }
        },
  
        abrirModalX(index, event) {
          this.indiceSeleccionado = index;
          this.modalAbierto = true;
        },

        seleccionarOpcion(opcion) {
          if (this.indiceSeleccionado !== null) {
            this.opciones[this.indiceSeleccionado].valor = opcion;
            this.cerrarModal();
          }
        },
        seleccionarOpcionTabla(opcion) {
          this.newChangeTable.tabla = opcion;
          this.mostrarOpcionesCelda = false; 
        },
        cerrarModal() {
          this.modalAbierto = false;
          this.indiceSeleccionado = null;
        },
        cerrarSiClickFuera(event) {
          if (this.modalAbierto) {
            const modal = this.$el.querySelector(".absolute");
            if (modal && !modal.contains(event.target)) {
              this.cerrarModal();
            }
          }
        },
        getNumeroInferior(indexN, indexP) {
          let fila = this.numeracionInicial.inferior[indexN];
          if (!fila || fila[indexP] === undefined) return ''; 
          return fila[indexP];  
      },
  
        getNumeroSuperior(indexN, indexP) {
            let nivel = this.secciones[1].niveles - indexN; 
            
            if (this.selectedOrientation === 'AntiHorario') {
            return `${nivel}.${this.numeracionInicial.superior + (this.secciones[1].banos - 1 - indexP)}`;
            }
            return `${nivel}.${this.numeracionInicial.superior + indexP}`; 
        },
        getNumeroDerecha(indexN, indexP) {
            let nivel = indexN + 1; 
            
            if (this.selectedOrientation === 'AntiHorario') {
            return `${nivel}.${this.numeracionInicial.derecha + (this.secciones[3].banos - 1 - indexP)}`;
            }
            return `${nivel}.${this.numeracionInicial.derecha + indexP}`; 
       },
        getNumeroIzquierda(indexN, indexP) {
            let nivel = this.secciones[2].niveles - indexN; 
            let columna = this.numeracionInicial.izquierda + (this.secciones[2].banos - 1) - indexP;
            
            if (this.selectedOrientation === 'AntiHorario') {
            return `${nivel}.${this.numeracionInicial.izquierda + indexP}`;
            }
            return `${nivel}.${columna}`; 
       },
    
      },
      watch: {
        niveles: {
          deep: true,
          handler(niveles) {
            niveles.forEach(nivel => {
              if (nivel.total > 0) {
                nivel.porcentaje = Math.round((nivel.actual / nivel.total) * 100);
              } else {
                nivel.porcentaje = 0;
              }
            });
          }
        },
        secciones: {
        deep: true,
        handler() {
          this.$nextTick(() => {
            if (this.$refs.SeccionSuperior) {
              this.heightSeccionSuperior = this.$refs.SeccionSuperior.offsetHeight;
            }
            if (this.$refs.SeccionInferior) {
              this.heightSeccionInferior = this.$refs.SeccionInferior.offsetHeight;
            }
            if (this.$refs.SeccionDerecha) {
              this.widthSeccionDerecha = this.$refs.SeccionDerecha.offsetWidth;
            }
            if (this.$refs.SeccionIzquierda) {
              this.widthSeccionIzquierda = this.$refs.SeccionIzquierda.offsetWidth;
            }
          });
        }
      }
      },
      mounted() {
        this.updateRightWidth();
        this.updateLeftWidth();
        window.addEventListener("resize", this.updateRightWidth);
        window.addEventListener("resize", this.updateLeftWidth);
        document.addEventListener("click", this.cerrarSiClickFuera);
        this.niveles.forEach(nivel => {
          nivel.porcentaje = Math.round((nivel.actual / nivel.total) * 100);
        });
      },
      beforeDestroy() {
        document.removeEventListener("click", this.cerrarSiClickFuera);
        window.removeEventListener("resize", this.updateRightWidth);
        window.removeEventListener("resize", this.updateLeftWidth);
      },
    
     };
     </script>
     
     <style scoped>
     .border-dashed {
       border-style: dashed;
     }
     .dropdownStyle {
      border: 1px solid #ccc;
      border-radius: 4px;
      box-shadow: 2px 2px 8px rgba(0, 0, 0, 0.2); 
      cursor: pointer;
    }
    .fade-enter-active, .fade-leave-active {
      transition: opacity 0.2s ease;
    }
    .fade-enter, .fade-leave-to {
      opacity: 0;
    }
    input[type="number"] {
      -moz-appearance: textfield;
    }
    input:focus {
      outline: none;
      border-color: inherit;
    }
    .styled-table {
      border-collapse: collapse;
      user-select: none; 
      width: 100%;
    }
    .vertical-text {
      writing-mode: vertical-rl; 
      text-orientation: mixed;
      white-space: nowrap; 
    }
    .vertical-textLeft {
      writing-mode: vertical-lr; 
      text-orientation: mixed;
      white-space: nowrap; 
    }
    
    .styled-table th,
    .styled-table td {
      border: 1px solid rgb(221, 221, 221);
      padding: 8px 15px;
      text-align: center;
    }
    
    .styled-table thead th {
      background-color: #dde5f4; 
      font-weight: bold;
    }
    
    .styled-table tbody td:first-child {
      background-color: #f0f0f0;
      font-weight: bold;
    }
    
    .reverse-table {
      border-collapse: collapse;
      width: 100%;
      user-select: none; 
    }
    
    .reverse-table th,
    .reverse-table td {
      border: 1px solid rgb(228, 228, 228);
      padding: 8px 15px;
      text-align: center;
    }
    .resaltado {
      background-color: rgba(173, 216, 230, 0.6);
      transition: background 0.2s;
    }
    
    .reverse-table thead th {
      background-color: #f0f0f0;
      font-weight: bold;
    }
    
    .reverse-table tbody td:first-child {
      background-color: #dde5f4; 
      font-weight: bold;
    }
    
    
    input[type="number"]::-webkit-inner-spin-button,
    input[type="number"]::-webkit-outer-spin-button {
      appearance: auto;
    }
    .dropdownStyle2 {
      border: 1px solid #ccc;
      border-radius: 4px;
      box-shadow: 2px 2px 8px rgba(0, 0, 0, 0.2); 
    }
    
    td {
      cursor: pointer; 
    }
    
    td.resaltado {
      cursor: crosshair; 
    }
    td {
      -webkit-user-select: none; 
      -moz-user-select: none; 
      -ms-user-select: none; 
      user-select: none; 
    }
    .modal-overlay {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0, 0, 0, 0.5);
      display: flex;
      justify-content: center;
      align-items: center;
      z-index: 100;
    }
    .modalSecondary{
      z-index:10,
    }
    
    
     </style>