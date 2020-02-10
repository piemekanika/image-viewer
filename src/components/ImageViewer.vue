<template>
  <modal
    name="modal"
    width="1100px"
    height="auto"
    :click-to-close="false"
  >
    <div class="viewer">
      <div class="viewer__image-container" ref="container">
        <div
          alt="alt" 
          class="viewer__image"
          :style="imageStyles"
          @mouseover="handleMouseover"
          ref="image"
        ></div>
      </div>

      <div class="viewer__controls">
        <button @click="rotateLeft">
          Rotate left
        </button>

        <button @click="zoomOut">
          Zoom out
        </button>

        <button @click="resetALl">
          Reset all
        </button>

        <button @click="zoomIn">
          Zoom in
        </button>

        <button @click="rotateRight">
          Rotate right
        </button>
      </div>
    </div>
  </modal>
</template>

<script>
export default {
  data() {
    return {
      rotateAngle: 0,
      zoom: 1,
      zoomSpeed: 1.1,
      imageSrc: 'https://storage.googleapis.com/afs-prod/media/media:a834b3cc0c7c41d19c3a409b40752134/3000.jpeg',
      windowSize: {
        w: window.outerWidth,
        h: window.outerHeight,
        iw: window.innerWidth,
        ih: window.innerHeight
      },
      madeImageDraggable: false
    }
  },
  computed: {
    imageStyles() {
      const container = this.$refs.container

      const styles = {
        transform: `rotate(${this.rotateAngle}deg) scale(${this.zoom}) translateX(-50%) translateY(-50%)`,
        backgroundImage: `url('${this.imageSrc}')`
      }

      let imageWidth = Infinity

      if (this.$refs.image) {
        imageWidth = this.$refs.image.getBoundingClientRect().width
      }

      if (this.rotateAngle % 180 && container.offsetHeight < imageWidth) {
        styles.width = `${container.offsetHeight}px`
      }

      return styles
    }
  },
  mounted() {
    this.$modal.show('modal')

    document.addEventListener('keydown', this.handleKeydown)
    document.addEventListener('wheel', this.handleMousewheel, { passive: false })
  },
  methods: {
    handleMousewheel(event) {
      if (event.ctrlKey) {
        event.preventDefault()

        if (event.deltaY > 0) {
          this.zoomOut()
        } else {
          this.zoomIn()
        }
      }
    },
    handleKeydown(event) {
      if (event.ctrlKey && [61, 107, 173, 109, 187, 189].includes(event.which)) {
        event.preventDefault()

        if ([187, 107].includes(event.which)) {
          this.zoomIn()
        } else if ([109, 189].includes(event.which)) {
          this.zoomOut()
        }
      }

      if (event.which === 37) {
        this.rotateLeft()
      } else if (event.which === 39) {
        this.rotateRight()
      }
    },
    resetZoom() {
      this.zoom = 1
    },
    zoomIn() {
      this.zoom *= this.zoomSpeed
    },
    zoomOut() {
      this.zoom /= this.zoomSpeed
    },
    rotateLeft() {
      this.rotateAngle -= 90
    },
    rotateRight() {
      this.rotateAngle += 90
    },
    handlePageZoom() {
      this.zoomIn()
    },
    handleMouseover() {
      if (!this.madeImageDraggable) {
        this.madeImageDraggable = true

        const dragElement = (el) => {
          let pos1 = 0, pos2 = 0, pos3 = 0, pos4 = 0

          el.onmousedown = dragMouseDown

          function dragMouseDown(e) {
            e = e || window.event
            e.preventDefault()
            // get the mouse cursor position at startup:
            pos3 = e.clientX
            pos4 = e.clientY
            document.onmouseup = closeDragElement
            // call a function whenever the cursor moves:
            document.onmousemove = elementDrag
          }

          function elementDrag(e) {
            e = e || window.event
            e.preventDefault()
            // calculate the new cursor position:
            pos1 = pos3 - e.clientX
            pos2 = pos4 - e.clientY
            pos3 = e.clientX
            pos4 = e.clientY
            // set the element's new position:
            el.style.top = (el.offsetTop - pos2) + "px"
            el.style.left = (el.offsetLeft - pos1) + "px"
          }

          function closeDragElement() {
            // stop moving when mouse button is released:
            document.onmouseup = null
            document.onmousemove = null
          }
        }

        dragElement(this.$refs.image)
      }
    },
    resetALl() {
      this.resetZoom()
      this.rotateAngle = 0
      this.$refs.image.style.removeProperty('top')
      this.$refs.image.style.removeProperty('left')
    }
  }
}
</script>

<style lang="scss">
.viewer {
  position: relative;

  .viewer__image-container {
    overflow: hidden;
    height: 80vh;
    width: 100%;
    z-index: 1;

    position: relative;

    .viewer__image {
      height: 100%;
      width: 100%;

      position: absolute;
      top: 50%;
      left: 50%;

      transition: transform .2s;

      transform-origin: top left;

      background-size: contain;
      background-repeat: no-repeat;
      background-position: center;

      cursor: grab;
    }
  }

  .viewer__controls {
    z-index: 2;

    background: white;

    display: flex;
    align-items: center;
    justify-content: center;

    padding: 20px;

    > * {
      margin: 0 5px;
    }
  }
}
</style>