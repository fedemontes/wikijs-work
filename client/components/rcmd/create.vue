<template lang='pug'>
v-container(fluid, grid-list-lg)
   v-content(:class='$vuetify.theme.dark ? "grey darken-4" : "grey lighten-5"'  )
   v-layout(row wrap)
       .projects-header
         img.animated.fadeInUp(src='/_assets/svg/icon-checkmark.svg', alt='Create', style='width: 80px;')
         .projects-header-title
           .headline.primary--text.animated.fadeInLeft Crear proyectos mediante IA 
           .subheading.grey--text.animated.fadeInLeft
       v-spacer

       v-btn.animated.fadeInDown.wait-p3s(color='grey', icon, outlined, to='/projects')
           v-icon mdi-arrow-left

       v-dialog(v-model='generatingProjects', max-width='600' )
        v-card.white
         v-card-text.pa-4 Espere unos momentos. No cierre la ventana o podrían perderse los cambios
          v-progress-linear(
           indeterminate
           color="blue"
           class="mb-0"
          )

       v-dialog(v-model='generateProjectsRcmdDialog', max-width='600' )
          template(v-slot:activator='{ on }')
          v-card
              .dialog-header.is-blue Generando proyecto
              v-card-text.pa-4 Esta operación puede tardar un rato. ¿Está seguro de que desea continuar? 
              v-card-actions
               v-spacer
               v-btn(text, @click='generateProjectsRcmdDialog = false') Cancelar
               v-btn(color='blue', dark, @click='generateProjectsRcmd') Continuar 

       v-btn.animated.fadeInDown(style='margin-left:5px;', color='success', large, active,  @click='handleSubmit')
         v-icon(left) mdi-auto-fix
         span GENERAR PROYECTO 
   v-form.pt-1(ref='form', v-model='valid')

    v-layout(row,wrap)
        v-flex(lg4 xs5)
             v-card.wiki-form.animated.fadeInUp.teal.lighten-5
               v-toolbar(color='teal', dark, dense, flat)
                 v-toolbar-title.subtitle-1 Opciones 
               v-card-text
                 v-select(
                   class="select"
                   v-model='sector'
                   outlined
                   :items='dataSheet.sectores'
                   :rules="requiredRule"
                   prepend-icon='mdi-shape'
                   persistent-hint
                   hint='Sectores'
                   required
                 )
               v-card-text
                 v-select(
                   outlined
                   v-model='perfil_alumnado'
                   :items='dataSheet.perfilestudiante'
                   :rules="requiredRule"
                   prepend-icon='mdi-school'
                   persistent-hint
                   hint='Perfil estudiante'
                   required
                 )

               v-card-text
                 v-select(
                   outlined
                   v-model='perfil_ideal'
                   :items='dataSheet.empleos'
                   :rules="requiredRule"
                   prepend-icon='mdi-account'
                   persistent-hint
                   hint='Perfil trabajador ideal'
                   required
                 )

               v-card-text
                 v-select(
                   outlined
                   v-model='trabajo'
                   :items='dataSheet.trabajos'
                   :rules="requiredRule"
                   prepend-icon='mdi-format-list-bulleted-type'
                   persistent-hint
                   hint='Tipo de trabajo'
                   required
                 )

               v-card-text
                 v-select(
                   outlined
                   v-model='horas'
                   :items='dataSheet.horas'
                   :rules="requiredRule"
                   prepend-icon='mdi-clock-time-nine'
                   persistent-hint
                   hint='Horas'
                   required
                 )

               v-card-text
                 v-select(
                   outlined
                   v-model='empleados'
                   :items='dataSheet.numempleados'
                   :rules="requiredRule"
                   prepend-icon='mdi-counter'
                   persistent-hint
                   hint='Número de empleados'
                   required
                 )

               v-card-text
                 v-select(
                   outlined
                   v-model='tecnologias'
                   :items='dataSheet.tecnologias'
                   :rules="requiredRule"
                   prepend-icon='mdi-robot-industrial'
                   persistent-hint
                   hint='Tecnologías'
                   required
                 )
               v-card-text
                 v-select(
                   outlined
                   v-model='sostenibilidad'
                   :items='dataSheet.areassostenibilidad'
                   :rules="requiredRule"
                   prepend-icon='mdi-tree'
                   persistent-hint
                   hint='Áreas de sostenibilidad'
                   required
                 )
        v-flex
           v-card.wiki-form.animated.fadeInUp
               v-toolbar(color='primary', dark, dense, flat)
                   v-toolbar-title.subtitle-1 Campos de texto 
               v-text-field.pa-3(
                     outlined
                     label='Público objetivo'
                     :counter='500'
                     v-model='publico_objetivo'
                     prepend-icon='mdi-text-short'
                     hint='Público objetivo'
                     persistent-hint
                     :rules="requiredRule"
                     required
                    )
               v-divider
               v-text-field.pa-3(
                     outlined
                     label='Objetivo del proyecto'
                     required
                     :rules="requiredRule"
                     :counter='500'
                     v-model='proyecto_objetivo'
                     prepend-icon='mdi-bullseye-arrow'
                     hint='Objetivo del proyecto'
                     persistent-hint
                    )
               v-divider
               v-switch.pa-3(
                   inset
                   v-model='experiencia'
                   label='¿Tienen experiencia en proyectos similares?'
                   color='primary'
                   persistent-hint
                   hint='Conocer la experiencia de la organización nos dará pistas para crear el proyecto'
                 )



           //-  template(v-if='dataSheet.ProyectosTipo')
           //-     v-list
           //-     template(v-for='(value, key) of dataSheet.ProyectosTipo')
           //-       v-list-item-title {{ value[1][0] }}
           //-     v-divider

</template>


<script>

import axios from 'axios'

export default {
 data() {
   return {
     valid: false,
     generatingProjects: false,
     generateProjectsRcmdDialog: false,
     sector: '',
     empleados: '',
     trabajo: '',
     horas: '',
     tecnologias: '',
     sostenibilidad: '',
     perfil_alumnado: '',
     perfil_ideal: '',
     experiencia: '',
     publico_objetivo: '',
     proyecto_objetivo: '',
     experiencia: false,
     dataSheet: {},
     errors: [],
     value: {},
     loading: false,
     requiredRule: [
        v => !!v || 'Campo obligatorio',
   ],
   }
 },      
 created () {
   axios.get('/r/create/data-sheet')
     .then(response => {
       this.dataSheet = response.data;
     })
     .catch(e => {
       this.errors.push(e)
       console.log(e)
     })
 },
 methods: {
   handleSubmit() {
     this.$refs.form.validate();
     if(!this.valid) {
       return
     }
     this.generateProjectsRcmdDialog = true; 
   },
   async generateProjectsRcmd() {
     this.generateProjectsRcmdDialog = false
     this.generatingProjects = true 
     this.loading = true
     this.$store.commit(`loadingStart`, 'projectsRcmd-generate')

     const data = await axios.post(
          '/r/create/project',
          {
            sector: this.sector.toString(),
            empleados: this.empleados.toString(),
            horas: this.horas.toString(),
            trabajo: this.trabajo.toString(),
            tecnologias: this.tecnologias.toString(),
            sostenibilidad: this.sostenibilidad.toString(),
            perfil_alumnado: this.perfil_alumnado.toString(),
            perfil_ideal: this.perfil_ideal.toString(),
            experiencia: this.experiencia.toString(),
            publico_objetivo: this.publico_objetivo.toString(),
            proyecto_objetivo: this.proyecto_objetivo.toString()
          }        
       )
     console.log(data.data);
     this.loading = false;
     this.generatingProjects = false; 
     this.$store.commit(`loadingStop`, 'projectsRcmd-generate')
     if ( data.data.code == 200 ) { 
       this.$store.commit('showNotification', {
           style: 'success',
           message: 'Project has been created and saved successfully.',
           icon: 'check'
         })
      window.location.href = '/r/projects';
    } else {
       this.$store.commit('showNotification', {
           style: 'failed',
           message: data.message,
           icon: 'alert'
         })
    }
    
   }
 }
}
</script>

<style>
.projects-header {
 display: flex;
 justify-content: flex-start;
 align-items: center;
 padding: 1px;
 }

   .truncate {
     max-width: 1px;
     white-space: nowrap;
     overflow: hidden;
     text-overflow: ellipsis;
   }

.v-card__text {
    padding: 2px;
  }

</style>
