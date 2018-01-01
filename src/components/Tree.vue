<template>
  <div>
    <v-stage
      ref="stage"
      :config="configKonva"
      @dragstart="handleDragstart"
      @dragend="handleDragend"
    >
      <v-layer ref="layer">
        <v-path
          :config="configCircle"
        >
        </v-path>
        <v-path
          v-for="item in list"
          :key="item.id"
          :config="item"
        >
        </v-path>
      </v-layer>
      <v-layer ref="dragLayer"></v-layer>
    </v-stage>
  </div>
</template>

<script>
  const width = window.innerWidth
  const height = window.innerHeight
  let vm = {}

  export default {
    data () {
      return {
        list: [],
        configKonva: {
          width: width - 20,
          height: height - 8
        },
        configCircle: {
          x: 50,
          y: 40,
          data: 'M18.014,9.564c-1.439,0.523-2.348,1.874-2.244,3.351l0.035,0.57l-0.576-0.07c-2.094-0.268-3.925-1.175-5.479-2.7   L8.99,9.959l-0.196,0.559c-0.414,1.245-0.149,2.56,0.714,3.445c0.46,0.489,0.356,0.559-0.437,0.268   c-0.276-0.093-0.518-0.163-0.541-0.128c-0.08,0.082,0.196,1.14,0.414,1.559c0.299,0.582,0.909,1.152,1.577,1.49l0.564,0.268   l-0.667,0.012c-0.644,0-0.667,0.012-0.598,0.257c0.23,0.756,1.139,1.559,2.152,1.909l0.714,0.244l-0.621,0.372   c-0.921,0.536-2.003,0.838-3.085,0.861c-0.519,0.012-0.944,0.058-0.944,0.093c0,0.116,1.405,0.767,2.221,1.024   c2.451,0.756,5.364,0.43,7.551-0.861c1.554-0.92,3.107-2.746,3.833-4.516c0.392-0.942,0.783-2.665,0.783-3.49   c0-0.536,0.035-0.605,0.679-1.245c0.38-0.372,0.737-0.779,0.806-0.896c0.115-0.221,0.103-0.221-0.483-0.024   c-0.978,0.349-1.117,0.303-0.633-0.221c0.356-0.372,0.783-1.047,0.783-1.245c0-0.035-0.172,0.023-0.369,0.128   c-0.207,0.116-0.667,0.291-1.013,0.395l-0.621,0.198l-0.564-0.385c-0.311-0.209-0.748-0.442-0.978-0.512   C19.442,9.355,18.544,9.378,18.014,9.564z',
          fill: 'green',
          scale: {
            x: 10,
            y: 10
          }
        }
      }
    },
    methods: {
      handleDragstart (starComponent) {
        const shape = starComponent.getStage()
        const dragLayer = vm.$refs.dragLayer.getStage()
        const stage = vm.$refs.stage.getStage()
        // moving to another layer will improve dragging performance
        shape.moveTo(dragLayer)
        stage.draw()
        starComponent.config.shadowOffsetX = 15
        starComponent.config.shadowOffsetY = 15
        starComponent.config.scaleX = starComponent.config.startScale * 1.2
        starComponent.config.scaleY = starComponent.config.startScale * 1.2
      },
      handleDragend (starComponent) {
        const shape = starComponent.getStage()
        const layer = vm.$refs.layer.getStage()
        const stage = vm.$refs.stage.getStage()
        shape.moveTo(layer)
        stage.draw()
        shape.to({
          duration: 0.5,
          easing: Konva.Easings.ElasticEaseOut,
          scaleX: starComponent.config.startScale,
          scaleY: starComponent.config.startScale,
          shadowOffsetX: 5,
          shadowOffsetY: 5
        })
      }
    },
    mounted () {
      vm = this
      for (let n = 0; n < 300; n++) {
        const scale = 1
        const stage = vm.$refs.stage.getStage()
        vm.list.push({
          x: Math.random() * stage.getWidth(),
          y: Math.random() * stage.getHeight(),
          data: 'M18.014,9.564c-1.439,0.523-2.348,1.874-2.244,3.351l0.035,0.57l-0.576-0.07c-2.094-0.268-3.925-1.175-5.479-2.7   L8.99,9.959l-0.196,0.559c-0.414,1.245-0.149,2.56,0.714,3.445c0.46,0.489,0.356,0.559-0.437,0.268   c-0.276-0.093-0.518-0.163-0.541-0.128c-0.08,0.082,0.196,1.14,0.414,1.559c0.299,0.582,0.909,1.152,1.577,1.49l0.564,0.268   l-0.667,0.012c-0.644,0-0.667,0.012-0.598,0.257c0.23,0.756,1.139,1.559,2.152,1.909l0.714,0.244l-0.621,0.372   c-0.921,0.536-2.003,0.838-3.085,0.861c-0.519,0.012-0.944,0.058-0.944,0.093c0,0.116,1.405,0.767,2.221,1.024   c2.451,0.756,5.364,0.43,7.551-0.861c1.554-0.92,3.107-2.746,3.833-4.516c0.392-0.942,0.783-2.665,0.783-3.49   c0-0.536,0.035-0.605,0.679-1.245c0.38-0.372,0.737-0.779,0.806-0.896c0.115-0.221,0.103-0.221-0.483-0.024   c-0.978,0.349-1.117,0.303-0.633-0.221c0.356-0.372,0.783-1.047,0.783-1.245c0-0.035-0.172,0.023-0.369,0.128   c-0.207,0.116-0.667,0.291-1.013,0.395l-0.621,0.198l-0.564-0.385c-0.311-0.209-0.748-0.442-0.978-0.512   C19.442,9.355,18.544,9.378,18.014,9.564z',
          fill: '#1dcaff',
          opacity: 0.80,
          draggable: true,
          scaleX: scale,
          scaleY: scale,
          shadowColor: 'white',
          shadowBlur: 50,
          shadowOffsetX: 0,
          shadowOffsetY: 0,
          shadowOpacity: 0.8,
          startScale: scale
        })
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
