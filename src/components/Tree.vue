<template>
  <div>
    <div class="navbar">

    </div>

    <v-stage
      ref="stage"
      :config="configKonva"
    >
      <v-layer ref="treeLayer">
        <v-shape
          :config="configCore"
        >
        </v-shape>
        <v-path
          v-for="leafConfig in leafPaths"
          :key="leafConfig.id"
          :config="leafConfig"
          @mouseenter="leafFocus"
          @mouseleave="leafUnfocus"
        >
        </v-path>
      </v-layer>
    </v-stage>

    <div class="sidebar">
      <p>{{ focusLeaf }}</p>
    </div>
  </div>
</template>

<script>
  const windowWidth = window.innerWidth
  const windowHeight = window.innerHeight
  const degToRad = Math.PI / 180
  const wid = windowWidth / 1.8
  const hei = windowHeight / 1.8 + 10
  const seasons = ['spring', 'summer', 'fall']
  let season = seasons[Math.floor(Math.random() * seasons.length)]

  let vm = {}

  export default {
    data () {
      return {
        focusLeaf: 'OK',
        leafPaths: [],
        configKonva: {
          width: windowWidth,
          height: windowHeight
        },
        configCore: {}
      }
    },
    methods: {
      leafFocus (leaf) {
        this.focusLeaf = leaf.getStage().index
        const stage = vm.$refs.stage.getStage()
        stage.container().style.cursor = 'pointer'
      },
      leafUnfocus (leaf) {
        const stage = vm.$refs.stage.getStage()
        stage.container().style.cursor = 'default'
      },
      randomizeOpacity (r, g, b) {
        r += Math.max(0, Math.min(255, 50 - Math.floor(Math.random() * 100)))
        g += Math.max(0, Math.min(255, 50 - Math.floor(Math.random() * 100)))
        b += Math.max(0, Math.min(255, 50 - Math.floor(Math.random() * 100)))
        let a = Math.random() * 0.6 + 0.4
        return 'rgba(' + r + ',' + g + ',' + b + ',' + a + ')'
      },
      makeLeaf (ctx, x, y, angle, newAngle) {
        this.branch(ctx, x, y, 0, 0, angle, 0, 1, newAngle, true)
      },
      branch (ctx, x, y, thick, len, angle, iterations, i, a, leaf) {
        ctx.lineWidth = thick
        ctx.lineCap = 'round'
        ctx.strokeStyle = 'saddlebrown'
        ctx.beginPath()
        ctx.moveTo(x, hei - y)
        i = i || 0
        a = a || 0
        leaf = leaf || false
        if (leaf) {
          len = 5
          ctx.lineWidth = 3
          if (season === 'spring') {
            // pink, green
            ctx.strokeStyle = Math.random() * 5 > 3 ? this.randomizeOpacity(255, 192, 203) : this.randomizeOpacity(0, 128, 0)
          } else if (season === 'summer') {
            // green
            ctx.strokeStyle = this.randomizeOpacity(0, 128, 0)
          } else if (season === 'fall') {
            // orange, red, yellow
            let fallColors = [this.randomizeOpacity(255, 165, 0), this.randomizeOpacity(255, 0, 0), this.randomizeOpacity(255, 165, 0)]
            ctx.strokeStyle = Math.random() * 15 < 13 ? 'transparent' : fallColors[Math.floor(Math.random() * fallColors.length)]
          } else {
            ctx.strokeStyle = 'transparent'
          }
          // this is an actual thing.
          ctx.lineCap = 'butt'
        }
        let newLen = (i === 0 || leaf) ? len : len * (0.5 + Math.random() * 0.6)
        let dX = x + (newLen * Math.sin(a * degToRad))
        let dY = y + (newLen * Math.cos(a * degToRad))
        ctx.lineTo(dX, hei - dY)
        let it = 3 + Math.floor(Math.random() * iterations)
        if (!leaf && i >= it) {
          ctx.lineWidth = Math.min(1, ctx.lineWidth)
        }
        ctx.stroke()
        a += 10 - (Math.random() * 20)
        if (!leaf && i < it) {
          let priority = Math.random() * 2 > 1 ? -1 : 1
          this.branch(ctx, dX, dY, Math.max(0.2, thick * 0.7), len * 0.85, angle, iterations, i + 1, a + priority * angle)
          this.branch(ctx, dX, dY, Math.max(0.2, thick * 0.7), len * 0.85, angle, iterations, i + 1, a - priority * angle)
          if (Math.random() * 2 > 1) {
            this.branch(ctx, x + (dX - x) * 0.4, y + (dY - y) * 0.4, Math.max(0.2, thick * 0.7), len * 0.85, angle, iterations, i + 1, a)
          }
          if (i > 1 && Math.random() * 2 > 1) {
            this.makeLeaf(ctx, x + (dX - x) / 2, y + (dY - y) / 2, angle, 2 * (a + angle))
          }
          if (i > 1 && Math.random() * 2 > 1) {
            this.makeLeaf(ctx, x + (dX - x) / 2, y + (dY - y) / 2, angle, 2 * (a - angle))
          }
        } else if (!leaf) {
          this.makeLeaf(ctx, dX, dY, angle, 2 * a + angle)
          this.makeLeaf(ctx, dX, dY, angle, 2 * a - angle)
          this.makeLeaf(ctx, dX, dY, angle, a)
        }
      },
      checkFall (ctx) {
        if (season === 'fall') {
          for (let i = 0; i < 500; i++) {
            ctx.beginPath()
            let colors = [this.randomizeOpacity(255, 165, 0), this.randomizeOpacity(255, 0, 0), this.randomizeOpacity(255, 165, 0)]
            ctx.strokeStyle = colors[Math.floor(Math.random() * colors.length)]
            ctx.lineWidth = 3
            let x = Math.random() * wid
            let y = hei - Math.random() * 20
            ctx.moveTo(x, y)
            ctx.lineTo(x + 5, y)
            ctx.stroke()
          }
        }
      }
    },
    mounted () {
      vm = this
      const stage = vm.$refs.stage.getStage()
      vm.configCore = {
        sceneFunc: function (context) {
          vm.checkFall(context._context)
          vm.branch(context._context, wid / 2, 5, 15, 100, 20 + Math.random() * 5, 15 + Math.floor(Math.random() * 5))
        },
        scaleX: 1.8,
        scaleY: 1.8
      }
      for (let n = 0; n < 300; n++) {
        vm.leafPaths.push({
          x: Math.random() * stage.getWidth(),
          y: Math.random() * stage.getHeight(),
          data: 'M18.014,9.564c-1.439,0.523-2.348,1.874-2.244,3.351l0.035,0.57l-0.576-0.07c-2.094-0.268-3.925-1.175-5.479-2.7   L8.99,9.959l-0.196,0.559c-0.414,1.245-0.149,2.56,0.714,3.445c0.46,0.489,0.356,0.559-0.437,0.268   c-0.276-0.093-0.518-0.163-0.541-0.128c-0.08,0.082,0.196,1.14,0.414,1.559c0.299,0.582,0.909,1.152,1.577,1.49l0.564,0.268   l-0.667,0.012c-0.644,0-0.667,0.012-0.598,0.257c0.23,0.756,1.139,1.559,2.152,1.909l0.714,0.244l-0.621,0.372   c-0.921,0.536-2.003,0.838-3.085,0.861c-0.519,0.012-0.944,0.058-0.944,0.093c0,0.116,1.405,0.767,2.221,1.024   c2.451,0.756,5.364,0.43,7.551-0.861c1.554-0.92,3.107-2.746,3.833-4.516c0.392-0.942,0.783-2.665,0.783-3.49   c0-0.536,0.035-0.605,0.679-1.245c0.38-0.372,0.737-0.779,0.806-0.896c0.115-0.221,0.103-0.221-0.483-0.024   c-0.978,0.349-1.117,0.303-0.633-0.221c0.356-0.372,0.783-1.047,0.783-1.245c0-0.035-0.172,0.023-0.369,0.128   c-0.207,0.116-0.667,0.291-1.013,0.395l-0.621,0.198l-0.564-0.385c-0.311-0.209-0.748-0.442-0.978-0.512   C19.442,9.355,18.544,9.378,18.014,9.564z',
          name: 'ok',
          fill: '#1dcaff',
          opacity: 0.80,
          scaleX: 1,
          scaleY: 1,
          shadowColor: 'white',
          shadowBlur: 50,
          shadowOffsetX: 0,
          shadowOffsetY: 0,
          shadowOpacity: 0.8,
          startScale: 1
        })
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .navbar {
    background-color: #333;
    height: 72px;
    padding-left: 24px;
    padding-right: 44px;
  }

  .sidebar {
    background-color: #333;
    color: #FFF;
    position: absolute;
    top: calc(72px + 24px);
    left: 24px;
    width: 225px;
    height: calc(100vh - 72px - 24px - 24px);
  }
</style>
