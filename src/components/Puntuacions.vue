<template>
  <!-- <q-page class="flex flex-start"> -->


		<q-table
		:columns="camps"
		:rows="jugadors"
		row-key="name"
		hide-bottom
		dense
		class="shadow-5 colorTaula flex"
		separator="cell"
		v-model:pagination="pagination"	
		>

			<template v-slot:body="props">
				<q-tr :props="props">
					
					<q-td key="posicio" :props="props">
						<span :class="{guanyador: props.row.posicio==1}">
						{{ props.row.posicio }}
						</span>
					</q-td>

					<q-td key="jugador" :props="props">
						<q-btn size="sm" round dense color="negative" icon="delete" @click="eliminarJugador(props.row.nom, props.rowIndex)" class="q-mr-md" />

						<span :class="{guanyador: props.row.posicio==1}">{{ props.row.nom }}</span>
					</q-td>

						
					<q-td key="total" :props="props" class="colTotal">
						{{ props.row.total }}
					</q-td>


					<q-td v-for="col in props.cols.filter( (col) => col.name.startsWith('Part') )"  :key="col.name" :props="props">
							
							{{ col.value }}

							<!-- v-model="props.row[col.field]" -->
							<q-popup-edit
								v-model="props.row[col.field]"
								color="red-14"
								dense
								label-set="Ok"
								label-cancel="Cancelar"
								@hide="sumatori(props);"
								>
								<q-input
									v-model="props.row[col.field]" 
									type="number"
									align="center"
									hide-underline
									class="bg-blue-1"
									autofocus
									dense
									
									
								/>
							</q-popup-edit>

					</q-td>

				</q-tr>
			</template>

		</q-table>

  <!-- </q-page> -->
</template>







<script>
import { ref, toRefs, computed, watch, defineComponent, onMounted } from 'vue';
import { useQuasar } from 'quasar'

export default defineComponent({
  name: 'PageIndex',
	props: [
		'propNouJugador', 'propNovaPartida', 'propOrdrePuntuacio', 'propEliminarPartides', 'propEliminarJugadorsiPartides'
	],

	setup (props){
		const $q = useQuasar()
		
		const {
			propNouJugador, 
			propNovaPartida, 
			propOrdrePuntuacio,
			propEliminarPartides,
			propEliminarJugadorsiPartides
		} = toRefs(props)

		// const camps = computed( () => store.state.mPuntuacio.camps )
		// let jugadors = computed( () => store.state.mPuntuacio.arrayjugadors )
		// const ultimaPartida = computed( () => store.state.mPuntuacio.ultimaPartida )


		let jugadors = ref([
			{nom: "AA",  total: 0, posicio: null },
			{nom: "BBB", total: 0, posicio: null },
			{nom: "CCC", total: 0, posicio: null },
			{nom: "DDD", total: 0, posicio: null },
		])

		let ultimaPartida =  ref(0)

		let camps = ref([
			{name: "posicio",   field: "posicio", label: "Pos",   align: "center" , style: "width: 15px"},
			{name: 'jugador', field: 'nom',   label: 'Jugador', align: 'left', style: "width: 200px;"},
			{name: "total",   field: "total", label: "Total",   align: "center" , style: "width: 50px"}
		])



		// Creació de nova partida / nova columna
		watch(propNovaPartida, (newValue, oldValue) => {
			console.log('WATCH propNovaPartida:      ', "newValue: " + newValue, "oldValue: " + oldValue)

			if (newValue && jugadors.value.length !== 0) {  // quan sigui 'true' i hi hagi algun jugador

				/* generacio nou camp - capçalera de columna */
				console.log("ultimaPartida: ", ultimaPartida)
				ultimaPartida.value++;

				var nouCamp = {
					name: "Part" + ultimaPartida.value, 
					field: "P" + ultimaPartida.value, 
					label: "__  P" + ultimaPartida.value + "  __", 
					align: "center", style: 'width: 15px'
				}
				
				if (ultimaPartida.value == 1){
					// afegim un camp mes a la taula que sera la corresponent a la 1ª nova partida
					camps.value.push(nouCamp);
				}else{  
					// la resta de partides noves s'inseriran despres del camp TOTAL (les antigues partides s'aniran desplaçant cap la dreta a la taula)
					camps.value.splice(3,0,nouCamp);
				}



				/* generacio de nova partida per cada jugador */

				// Creacio de les dades dels jugadors del nou CAMP
				// per cada jugador, 
				for(var i = 0; i < jugadors.value.length; i++){
					// es crea l'objecte "partida" amb el nom Px 
					jugadors.value[i]["P" + ultimaPartida.value] = null;
				}

			}
		})
		


		// Creacio de nou jugador / nova fila
		watch(propNouJugador, (newValue, oldValue) => {
			console.log('WATCH nouJugador -> ', "newValue: " + newValue, "oldValue: " + oldValue)
			if (newValue != null && newValue.length != 0){
				
				let objNouJugador = {}
				objNouJugador.nom = newValue;
				objNouJugador.total = 0;
				objNouJugador.posicio = null;

				for(var i = 1; i <= ultimaPartida.value; i++){
					objNouJugador["P" + i] = null;
				}
				jugadors.value.push(objNouJugador);
			}
		})



		// Eliminació de partides
		watch(propEliminarPartides, (newValue, oldValue) => {
			console.log('WATCH propEliminarPartides:      ', "newValue: " + newValue, "oldValue: " + oldValue)

			if (newValue && jugadors.value.length !== 0) {  // quan sigui 'true' i hi hagi algun jugador

				// Eliminem de la taula "camps" tots els que son partides
				camps.value = camps.value.splice(0,3);


				// Eliminem de la taula jugadors totes les columnes que son partides
				for(var i = 0; i < jugadors.value.length; i++){
						// posem a zero el valor de la propietat "total" i a null el valor de "posicio"
						jugadors.value[i].total = 0;
						jugadors.value[i].posicio = null;

						for (var propietat in jugadors.value[i]){
							
							// per a la propietat que comenci per P i que el seu valor no sigui null
							if (propietat.startsWith("P")){
								delete jugadors.value[i][propietat];
							}
						}
				}
				ultimaPartida.value = 0;				


			}
		})
		

		// Eliminació de jugadors i partides
		watch(propEliminarJugadorsiPartides, (newValue, oldValue) => {
			console.log('WATCH propEliminarJugadorsiPartides:      ', "newValue: " + newValue, "oldValue: " + oldValue)

			if (newValue && jugadors.value.length !== 0) {  // quan sigui 'true' i hi hagi algun jugador

				// Eliminem de la taula "camps" tots els que son partides
				camps.value = camps.value.splice(0,3);


				// Eliminem totes les dades de la taula jugadors
				jugadors.value = []
				ultimaPartida.value = 0;				


			}
		})
		

		// Calcula la suma dels punts del jugador
    const sumatori = (props) => {
      console.log("***** INICI SUMATORI *****");

			const objFila = props.row
			const indexJugadors = props.rowIndex

      var suma = 0;
      // fem recorregut per totes les propitats
      for (var propietat in objFila){
        // suma totes les propietats que comencin per P i que el seu valor no sigui null
        if (propietat.startsWith("P") && (objFila[propietat] != null)){
         suma += parseInt(objFila[propietat]);
        }
      }

			jugadors.value[indexJugadors].total = suma 


			calculaPosicio()

      // store.dispatch('actionSumatori', {numFila: objFila.__index, suma: suma})
      // .then(() => {
      //   store.dispatch('actionCalculaPosicio');
      // });

      //console.log("objFila.total: " + objFila.total);
      //console.log("***** FINAL SUMATORI 2 *****");
      //this.calculaPosicio();
    }

		// Recalcula la posició cada vegada que es canvia l'ordre establert
		watch( propOrdrePuntuacio, (newValue, oldValue) => {
			console.log('WATCH ordrePosicio -> ', "newValue: " + newValue, "    oldValue: " + oldValue)
			calculaPosicio();
		})


		// Recalcula la posició de tots els jugadors
		const calculaPosicio = () => {

			console.log("*** CalculaPosicio ***");

			// creacio del un array on posem els valors totals de cada jugador
			var arrTotals = [];
			for(var i=0; i<jugadors.value.length; i++){
				arrTotals.push(jugadors.value[i].total); 
			}

			// ordenem array segons l'ordre establert
			if (propOrdrePuntuacio.value == "ordreMenorPuntuacio"){
				arrTotals.sort(function(a, b){return a-b});   // Sort numbers in an array in ascending order
			}else{
				arrTotals.sort(function(a, b){return b-a});   // Sort numbers in an array in descending order
			}

			// ---- treure valors duplicats -----
				for (var i = 1; i < arrTotals.length; ) {
					if (arrTotals[i-1] === arrTotals[i])
							arrTotals.splice(i,1);
					else
							i++;
			}

			// per cada jugador li assignem la seva posició
			for(var i=0; i<jugadors.value.length; i++){
				var posicio;
				if (jugadors.value[i].total != null){
					for(var j=0; j<arrTotals.length; j++){  
						if ((jugadors.value[i].total == arrTotals[j])) {
							posicio = j + 1;
						}
					}
					jugadors.value[i].posicio = posicio;
				}
			}

		}


    const eliminarJugador = (nomJugador, index) => {

      $q.dialog({
        title: 'Confirmar',
        message: "Eliminar el jugador '" + nomJugador + "' ?",
        ok: true,
        cancel: true
      }).onOk(() =>  {
          console.log("Ok");
					
					jugadors.value.splice(index,1);
					calculaPosicio()

					$q.notify({
						message: nomJugador + " eliminat",
						timeout: 2000
					});


      }).onCancel(() =>  {
        console.log("No");
        $q.notify({
					message: "Ok. No eliminem a " + nomJugador,
					timeout: 2000
				});
      })
    }



		// guarda les dades a localstorage
		watch( [jugadors, camps, ultimaPartida], ([newValueJugadors, newValueCamps, newValueUltimaPartida], [oldValueJugadors, oldValueCamps, oldValueUltimaPartida]) => {
			console.log('WATCH Guarda dades a LOCALSTORAGE ')
			
			$q.localStorage.set('keyPuntuacio', {
				'jugadors': jugadors.value,
				'camps': camps.value,
				'ultimaPartida': ultimaPartida.value
			})

		})



		// Carrega les dades si n'hi ha al Localstorage
    onMounted(() => {
      if ($q.localStorage.keyPuntuacio) {
				camps.value = $q.localStorage.getItem(camps)
				jugadors.value = $q.localStorage.getItem(jugadors)
				ultimaPartida.value = $q.localStorage.getItem(ultimaPartida)
			}
    })


		return { 
			camps,
			jugadors,
			ultimaPartida,
			sumatori,
			eliminarJugador,

			pagination: ref({
        rowsPerPage: 0
      })
		}
	}
})
</script>
