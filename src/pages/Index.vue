<template>
  <q-page class="flex flex-start">


		<q-table
		:columns="camps"
		:rows="jugadors"
		row-key="name"
		hide-bottom
		dense
		class="shadow-5 colorTaula flex"
		separator="cell"	
		>

			<template v-slot:body="props">
				<q-tr :props="props">
					
					<q-td key="posicio" :props="props">
						<span :class="{guanyador: props.row.posicio==1}">
						{{ props.row.posicio }}
						</span>
					</q-td>

					<q-td key="jugador" :props="props">
						<q-btn size="sm" round dense color="negative" icon="delete" @click="eliminarJugador(props.row.nom)" class="q-mr-md" />

						<span :class="{guanyador: props.row.posicio==1}">{{ props.row.nom }}</span>
					</q-td>

						
					<q-td key="total" :props="props" class="colTotal">
						{{ props.row.total }}
					</q-td>


					<q-td v-for="col in props.cols.filter( (col) => col.name.startsWith('Part') )"  :key="col.name" :props="props" @click="modificarPunts(props)">
							
							{{ col.value }}

							<!-- v-model="props.row[col.field]" -->
							<q-popup-edit
								v-model="props.row[col.field]"
								title="punts" 
								color="red-14"
								buttons
								label-set="Ok"
								label-cancel="Cancelar"
								>
								<q-input
									v-model="props.row[col.field]" 
									type="number"
									align="center"
									hide-underline
									class="bg-blue-1"
									autofocus
									@input="sumatori(props.row);"
								/>
							</q-popup-edit>

					</q-td>

				</q-tr>
			</template>

		</q-table>

  </q-page>
</template>







<script>
import { ref, computed, watch, defineComponent } from 'vue';
import { useStore } from "vuex";
import { useQuasar } from 'quasar'

export default defineComponent({
  name: 'PageIndex',

	setup (props){
		const store = useStore()
		const $q = useQuasar()
		
		// const camps = computed( () => store.state.mPuntuacio.camps )
		// let jugadors = computed( () => store.state.mPuntuacio.arrayjugadors )
		// const ultimaPartida = computed( () => store.state.mPuntuacio.ultimaPartida )


		let jugadors = ref([
			{nom: "AA",  total: 0, posicio: null },
			{nom: "BBB", total: 0, posicio: null },
			{nom: "CCC", total: 0, posicio: null },
			{nom: "DDD", total: 0, posicio: null },
		])

		let ultimaPartida =  0

		let camps = ref([
			{name: "posicio",   field: "posicio", label: "Pos",   align: "center" , style: "width: 15px"},
			{name: 'jugador', field: 'nom',   label: 'Jugador', align: 'left', style: "width: 200px;"},
			{name: "total",   field: "total", label: "Total",   align: "center" , style: "width: 50px"}
		])

		
		watch(props.propNouJugador, (newValue, oldValue) => {
			console.log('WATCH propNouJugador:      ', "newValue: " + newValue, "oldValue: " + oldValue)
		})











    const sumatori = (objFila) => {
      console.log("***** INICI SUMATORI *****");

      var suma = 0;
      // fem recorregut per totes les propitats
      for (var propietat in objFila){
        //console.log(propietat + ": " + JSON.stringify(objFila[propietat]));
        
        // suma totes les propietats que comencin per P i que el seu valor no sigui null
        //console.log("propietat: " + propietat +
           //"\tobjFila[propietat].valor: " + objFila[propietat].valor);
        
        //if (propietat.startsWith("P") && (objFila[propietat].valor != null)){
        if (propietat.startsWith("P") && (objFila[propietat] != null)){
         //suma += parseInt(objFila[propietat].valor);
         suma += parseInt(objFila[propietat]);
        }
      }

      console.log("suma: " + suma);
      //objFila.total = suma; 
      store.dispatch('actionSumatori', {numFila: objFila.__index, suma: suma})
      .then(() => {
        store.dispatch('actionCalculaPosicio');
      });

      //console.log("objFila.total: " + objFila.total);
      //console.log("***** FINAL SUMATORI 2 *****");
      //this.calculaPosicio();
    }


    const eliminarJugador = (nomJugador) => {

      $q.dialog({
        title: 'Confirmar',
        message: "Eliminar el jugador '" + nomJugador + "' ?",
        ok: true,
        cancel: true
      }).onOk(() =>  {
          console.log("Ok");
          store.dispatch('mPuntuacio/actionEliminarJugador', nomJugador)
          .then(() => {
            store.dispatch('mPuntuacio/actionCalculaPosicio');

            $q.notify({
              message: nomJugador + " eliminat",
              timeout: 2000
            });
          });

      }).onCancel(() =>  {
        console.log("No");
        $q.notify({
					message: "Ok. No eliminem a " + nomJugador,
					timeout: 2000
				});
      })
    }


		return { 
			camps,
			jugadors,
			ultimaPartida,
			sumatori,
			eliminarJugador,
			modificarPunts
		}
	}
})
</script>
