<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated class="gradiente">
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title>
          Puntuació
        </q-toolbar-title>

        <!-- icona AFEGIR JUGADOR -->
				<q-btn
          dense
          round
          @click="qDialogPerson=true"
          aria-label="Afegir jugador"
					color="green-7"
        >
          <q-icon name="person_add" />
        </q-btn>

        <!-- icona AFEGIR PARTIDA -->
				<q-btn
          dense
          round
          @click="generarNovaPartida"
          aria-label="Afegir partida"
					color="amber-10"
					class="q-ml-md"
        >
          <q-icon name="playlist_add" />
        </q-btn>


        <!-- <div>Quasar v{{ $q.version }}</div> -->
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      bordered
    >

      <q-list
        separator
      >
        <q-item-label header>Opcions</q-item-label>

        <q-item>
          <q-select
            v-model="ordrePuntuacio"
            label="Ordre posicio"
            outlined
						dense
            :options="opcions_ordre_puntuacio"
            color="amber-10"
            style="min-width: 100px"
            @update:model-value="(v) => {leftDrawerOpen = false}"
          />
        </q-item>
        <!-- <q-item-separator /> -->
        <q-item >
          <q-chip color="red">Eliminar un jugador:</q-chip> Fer doble click/tab sobre el nom del jugador
        </q-item>
        <!-- <q-item-separator /> -->
        <q-item >
          <q-btn color="negative" noCaps icon="delete" label="Eliminar partides" @click="eliminarPartides" />
        </q-item>
        <!-- <q-item-separator /> -->
        <q-item >
          <q-btn color="negative" noCaps icon="delete" label="Eliminar Jugadors i partides" @click="eliminarJugadorsiPartides" />
        </q-item>
        <!-- <q-item-separator /> -->

      </q-list>



<!-- 
      <q-list>
        <q-item-label
          header
        >
          Essential Links
        </q-item-label>

        <EssentialLink
          v-for="link in essentialLinks"
          :key="link.title"
          v-bind="link"
        />
      </q-list> 
-->



    </q-drawer>

    <q-page-container>
      <!-- <router-view /> -->
			<pagPunts 
			:propNouJugador="nouJugador" 
			:propNovaPartida="novaPartida" 
			:propOrdrePuntuacio="ordrePuntuacio.value"
			:propEliminarPartides="snEliminarPartides"
			:propEliminarJugadorsiPartides="snEliminarJugadorsiPartides"
			/>
    </q-page-container>


		
  </q-layout>

	

	<!-- QUADRE DIALEG    afegir persona -->
	<q-dialog v-model="qDialogPerson" persistent>
		<q-card style="min-width: 350px">
			<q-card-section>
				<div class="text-h6">Nom jugador</div>
			</q-card-section>

			<q-card-section class="q-pt-none">
				<q-input dense v-model="inputJugador" autofocus />
			</q-card-section>

			<q-card-actions align="right" class="text-primary">
				<q-btn label="Cancel" v-close-popup />
				<q-btn label="Afegir" @click="afegirJugador" v-close-popup />
			</q-card-actions>
		</q-card>
	</q-dialog>






</template>

<script>
import pagPunts from 'components/Puntuacions.vue'
// import EssentialLink from 'components/EssentialLink.vue'

// const linksList = [
//   {
//     title: 'Docs',
//     caption: 'quasar.dev',
//     icon: 'school',
//     link: 'https://quasar.dev'
//   },
//   {
//     title: 'Github',
//     caption: 'github.com/quasarframework',
//     icon: 'code',
//     link: 'https://github.com/quasarframework'
//   },
//   {
//     title: 'Discord Chat Channel',
//     caption: 'chat.quasar.dev',
//     icon: 'chat',
//     link: 'https://chat.quasar.dev'
//   },
//   {
//     title: 'Forum',
//     caption: 'forum.quasar.dev',
//     icon: 'record_voice_over',
//     link: 'https://forum.quasar.dev'
//   },
//   {
//     title: 'Twitter',
//     caption: '@quasarframework',
//     icon: 'rss_feed',
//     link: 'https://twitter.quasar.dev'
//   },
//   {
//     title: 'Facebook',
//     caption: '@QuasarFramework',
//     icon: 'public',
//     link: 'https://facebook.quasar.dev'
//   },
//   {
//     title: 'Quasar Awesome',
//     caption: 'Community Quasar projects',
//     icon: 'favorite',
//     link: 'https://awesome.quasar.dev'
//   }
// ];

import { defineComponent, ref, reactive } from 'vue'
import { useQuasar } from 'quasar'

export default defineComponent({
  name: 'MainLayout',

  components: {
		pagPunts,

  },

  setup () {
    const $q = useQuasar()

    const leftDrawerOpen = ref(false)
		
		let qDialogPerson = ref(false)
		let inputJugador = ref(null)


		let nouJugador = ref(null)
		let propNouJugador = ref(null)
		const afegirJugador = () => {
			nouJugador.value = inputJugador.value
			console.log("propNouJugador:  ", propNouJugador)
			inputJugador.value = null			
		}


		let propNovaPartida = ref(false)
		let novaPartida = ref(false)
		const generarNovaPartida = () => {
			novaPartida.value = true
			setTimeout( () => novaPartida.value = false , 500)  
    }



		let ordrePuntuacio = ref({ label: 'menor puntuacio', value: 'ordreMenorPuntuacio' })
		let propOrdrePuntuacio = ref(null)
    let opcions_ordre_puntuacio = ref([
			{ label: 'major puntuacio', value: 'ordreMajorPuntuacio' },
			{ label: 'menor puntuacio', value: 'ordreMenorPuntuacio' }
		])
    
		// const accioSeleccionarOrdre = (v) => {
		// 	console.log("--- ACCIO SELECCIONAR ORDRE ---")
		// 	console.log("ordrePuntuacio", v)
		// 	leftDrawerOpen.value = false;
    // }


    

		let propEliminarPartides = ref(false)
		let snEliminarPartides = ref(false)

		const eliminarPartides = () => {
      $q.dialog({
        title: 'Confirmar',
        message: "Eliminar totes les partides?",
        ok: 'Ok',
        cancel: 'No'
      }).onOk(() => {
            
				snEliminarPartides.value = true
				setTimeout( () => snEliminarPartides.value = false , 500)  

				leftDrawerOpen.value = false;
	
				$q.notify({
					message: "Partides eliminades !!",
					timeout: 2000
				});


      }).onCancel(() => {
        $q.notify({
              message: "no s'ha eliminat cap partida",
              timeout: 2000
            });
      })

    }




		let propEliminarJugadorsiPartides = ref(false)
		let snEliminarJugadorsiPartides = ref(false)

		const eliminarJugadorsiPartides = () => {
      $q.dialog({
        title: 'Confirmar',
        message: "Eliminar tots els jugadors i tote les partides?",
        ok: 'Ok',
        cancel: 'No'
      }).onOk(() => {
            
				snEliminarJugadorsiPartides.value = true
				setTimeout( () => snEliminarJugadorsiPartides.value = false , 500)  

				leftDrawerOpen.value = false;
	
				$q.notify({
					message: "Jugadors i partides eliminats !!",
					timeout: 2000
				});


      }).onCancel(() => {
        $q.notify({
              message: "no s'ha eliminat res",
              timeout: 2000
            });
      })

    }











    return {
      // essentialLinks: linksList,
      leftDrawerOpen,
      toggleLeftDrawer () {
        leftDrawerOpen.value = !leftDrawerOpen.value
      },

			qDialogPerson,
			inputJugador,
			nouJugador,
			propNouJugador,
			afegirJugador,

      opcions_ordre_puntuacio,
      ordrePuntuacio,
      propOrdrePuntuacio,
			// accioSeleccionarOrdre,

			novaPartida,
			propNovaPartida,
			generarNovaPartida,

			snEliminarPartides,
			propEliminarPartides,
			eliminarPartides,

			snEliminarJugadorsiPartides,
			propEliminarJugadorsiPartides,
			eliminarJugadorsiPartides,


    }
  }
})
</script>



<style scoped>
	.gradiente {
		background: rgb(0,15,59);
		background: linear-gradient(90deg, rgba(0,15,59,1) 0%, rgba(85,145,245,1) 100%);	}
</style>