<template>
  <div class="q-pa-md">
      <div class="row">
        <div class="col-12 col-md-4">
            
        <div class="q-gutter-md row items-start">
            <q-card style="max-width: 250px">
                 <img src="https://cdn.quasar.dev/img/avatar2.jpg">
            <q-card-section>
                <div class="text-h6">Em busca de aventuras</div>
                <div class="text-subtitle2">Olá, {{users.name}}  {{users.lastName}} </div>
            </q-card-section>

        <q-separator />

            <q-card-actions>
                <q-btn label="Perfil" color="primary" flat />
                <q-btn icon="event" round color="primary">
                 <q-popup-proxy @before-show="updateProxy" cover transition-show="scale" transition-hide="scale">
                <q-date v-model="proxyDate">
                <div class="row items-center justify-end q-gutter-sm">
                    <q-btn label="Cancel" color="primary" flat v-close-popup />
                    <q-btn label="OK" color="primary" flat @click="save" v-close-popup />
                </div>
                </q-date>
            </q-popup-proxy>
            </q-btn>
            </q-card-actions>

             <q-separator />

            <q-card-actions vertical>
                <q-btn flat to='/home'>Home</q-btn>
                <q-btn flat to='/events'>Eventos</q-btn>
                <q-btn flat to='/myevents'>Meus Eventos</q-btn>
            </q-card-actions>
            </q-card>
        </div>
        </div>
        
        <div class="col-12 col-md-8">
            <div class="q-pa-md row items-start q-gutter-md">
                 <q-card
                        class="my-card" 
                        flat 
                        bordered
                        v-for="event in events" 
                        :key="event.id">
                <q-card-section horizontal>
                    <q-card-section class="q-pt-xs">
                    <div class="text-h5 q-mt-sm q-mb-xs"> {{ event.name }}</div>
                    <div class="text-caption text-grey" formDate>
                       Inicio : {{event.starTime}}
                    </div>
                    <div class="text-caption text-grey" formDate>
                       Fim : {{event.endTime}}
                    </div>
                    <div class="text-caption text-grey" >
                       Evento : {{event.event}}
                    </div>
                    <div class="text-caption text-grey" formDate>
                       Fim : {{event.endTime}}
                    </div>
                    <div class="text-caption text-grey" >
                       Numero Maximo de Participantes : {{event.maxParticipants}}
                    </div>
                     <div class="text-caption text-grey" >
                       Numero de Participantes : {{event.numbParticipants}}
                    </div>
                    <div class="text-caption text-grey" >
                       Endereço : {{event.neighborhood}}
                    </div>
                    <div class="text-caption text-grey" >
                       Cidade : {{event.city}}
                    </div>

                    <div class="text-caption"  flat formDate>
                    {{event.day}} 
                    </div>
                   
                    <div class="text-caption text-primary text-bold" 
                        flat 
                        v-if="!event.confirmEvent">
                        Evento : Não Confirmado
                    </div>
                     <div class="text-caption text-primary text-bold" 
                        flat 
                        v-if="event.confirmEvent">
                        Evento : Confirmado
                    </div>
                    </q-card-section>
                </q-card-section>

                <q-separator />

                <q-card-section>
                     <q-btn 
                        :disable="event.confirmEvent"
                        v-show="users.uuid"  
                        @click="addMembros(event.numbParticipants,event.maxParticipants) && editEvent(event.id) " 
                        outline 
                        color="primary" 
                        label="Eu quero participar" >
                        {{editEvents.numbParticipants}}
                     </q-btn>
                </q-card-section>
                </q-card>
            </div>
        </div>
    </div>
  </div>
</template>

<script>
import { defineComponent } from 'vue';
import axios from 'axios';
import { date } from 'quasar';
import { ref } from 'vue'
import { mapState, mapActions } from 'vuex';

export default defineComponent({
  name: 'Home',
  data(){
      return {
        editEvents: {
            numbParticipants: null,
            confirmEvent: false
        },
      }
      
  },
    methods: {
        ... mapActions('option', ['getEvents']),

        addMembros(numbParticipants,maxParticipants){
            if(maxParticipants == numbParticipants) {
                this.editEvents.numbParticipants = numbParticipants
                return this.editEvents.confirmEvent = true
            } else {
                return this.editEvents.numbParticipants = numbParticipants + 1
            }        
        },

        editEvent(id){
            axios.put(`${ process.env.API }/events/${id}`, this.editEvents )
                .then(response => {
                    console.log('response', response.data)
                    this.$router.push('/home')
                    this.$q.notify({
                    message: 'Evento Criado com sucesso !!!',
                    actions: [
                        { label: 'Dismiss', color: 'yellow' }
                    ]
                    })
                    this.$q.loading.hide()
            }).catch(error => {
                console.log('error:', error)
                this.$q.dialog({
                title: 'Error',
                message: 'Ops, something went wrong, try again'
                })
                this.$q.loading.hide()
            })
        },

    },
    computed: {
      ... mapState('firebase', ['users']),
      ... mapState('option', ['events']),
      formDate(day){
       return date.formatDate(day, 'MMMM D h:mmA')
      },
      
    },
     created(){
        this.getEvents()
    },
    setup () {
        return {
      selection: ref([ 1 ])
    }
  }
})
</script>

