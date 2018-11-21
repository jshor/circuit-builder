<template>
  <Container
    @drag-start="dragStart"
    @drag-end="dragEnd"
    class="ic"
    :class="{ 'ic--dragging': isDragging }">
    <div class="ic__col">
      <div 
        @mouseover="setActiveOrientation('left')"
        @mouseout="removeActiveOrientation('left')">
        <Container
          orientation="vertical"
          group-name="left"
          class="ic__wire-container ic__wire-container--left"
          @drop="(e) => onWireDrop('left', e)"
          :should-accept-drop="() => true"
          :get-child-payload="getCardPayload('left')">
          <Draggable v-for="wire in wires.left.children" :key="wire.id">
            <Wire type="left" />
          </Draggable>
        </Container>
      </div>
    </div>
    <div class="ic__col ic__col--center">
      <div
        @mouseover="setActiveOrientation('top')"
        @mouseout="removeActiveOrientation('top')">
        <Container
          orientation="horizontal"
          group-name="top"
          class="ic__wire-container ic__wire-container--top"
          @drop="(e) => onWireDrop('top', e)"
          :should-accept-drop="() => true"
          :get-child-payload="getCardPayload('top')">
          <Draggable v-for="wire in wires.top.children" :key="wire.id">
            <Wire type="top" />
          </Draggable>
        </Container>
      </div>
      <div class="ic__center">{{ isDragging }}</div>
      <div
          @mouseover="setActiveOrientation('bottom')"
          @mouseout="removeActiveOrientation('bottom')">
        <Container
          orientation="horizontal"
          group-name="bottom"
          class="ic__wire-container ic__wire-container--bottom"
          @drop="(e) => onWireDrop('bottom', e)"
          @mouseover="setActiveOrientation('bottom')"
          @mouseout="removeActiveOrientation('bottom')"
          :should-accept-drop="() => true"
          :get-child-payload="getCardPayload('bottom')">
          <Draggable v-for="wire in wires.bottom.children" :key="wire.id">
            <Wire type="bottom" />
          </Draggable>
        </Container>
      </div>
    </div>
    <div class="ic__col">
      <div
          @mouseover="setActiveOrientation('right')"
          @mouseout="removeActiveOrientation('right')">
        <Container
          orientation="vertical"
          group-name="right"
          @drop="(e) => onWireDrop('right', e)"
          :get-child-payload="getCardPayload('right')"
          :should-accept-drop="() => true"
          class="ic__wire-container ic__wire-container--right">
          <Draggable v-for="wire in wires.right.children" :key="wire.id">
            <Wire type="right" />
          </Draggable>
        </Container>
      </div>
    </div>
  </Container>
</template>

<script>
import { Container, Draggable } from 'vue-smooth-dnd'
import Wire from './Wire'

const applyDrag = (arr, dragResult) => {
  const { removedIndex, addedIndex, payload } = dragResult
  if (removedIndex === null && addedIndex === null) return arr

  const result = [...arr]
  let itemToAdd = payload

  if (removedIndex !== null) {
    itemToAdd = result.splice(removedIndex, 1)[0]
  }

  if (addedIndex !== null) {
    result.splice(addedIndex, 0, itemToAdd)
  }

  return result
}

const generateItems = (count) => {
  return Array(count)
    .fill({})
    .map(() => ({ id: Math.random(), label: 'ABC' }))
}

const wires = {
  top: {
    children: generateItems(3)
  },
  bottom: {
    children: generateItems(3)
  },
  left: {
    children: generateItems(3)
  },
  right: {
    children: generateItems(1)
  }
}

export default {
  name: 'Cards',

  components: {Container, Draggable, Wire},

  data () {
    return {
      wires,
      isDragging: false,
      activeItem: null,
      activeOrientation: 'none'
    }
  },

  methods: {
    onWireDrop (location, dropResult) {
      const newWires = Object.assign({}, this.wires[location])
      this.wires[location].children = applyDrag(newWires.children, dropResult)
    },

    getCardPayload (location) {
      return index => {
        return this.wires[location].children[index]
      }
    },

    dragStart ({ payload }) {
      this.isDragging = true
      this.setActiveItemClass()
    },

    dragEnd () {
      this.isDragging = false
      this.setActiveDraggingItem(false)
    },

    setActiveDraggingItem (isActive) {
      if (isActive) {
        if (!this.activeItem) {
          const divs = document.querySelectorAll('div')

          divs.forEach((activeItem) => {
            if (activeItem.style.position === 'fixed') {
              this.activeItem = activeItem.children[0]
            }
          })
        }
      } else {
        this.activeItem = null
      }
    },

    setActiveItemClass () {
      this.setActiveDraggingItem(true)

      if (this.activeItem) {
        this.activeItem.className = `wire wire--${this.activeOrientation}`
      }
    },

    setActiveOrientation (orientation) {
      this.activeOrientation = orientation
      this.setActiveItemClass(this.activeOrientation)
    },

    removeActiveOrientation (orientation) {
      if (this.activeOrientation === orientation) {
        this.activeOrientation = 'node'
        this.setActiveItemClass(this.activeOrientation)
      }
    }
  }
}
</script>

<style lang="scss">
$port-size: 40px;
$port-radius: 16px;
$port-width: 40px;
$min-dimension: 100px;
$stroke-width: 3px;
$stroke-color: #000;
$bg-color: #fff;

.droppy {
  background-color: red !important;
}

.ic {
  display: flex;

  &--dragging {
    .ic__wire-container:hover {
      background-color: rgba(186, 212, 255, 0.5);
    }
  }
  
  &__col {
    display: flex;
    min-height: $min-dimension;
    
    &--center {
      flex-direction: column;
    }
  }
  
  &__wire-container {
    box-sizing: border-box;
    display: flex;
    justify-content: center;
    
    &--left, &--right {
      width: $port-size;
      min-height: calc(100% - #{$port-width * 2});
      margin: $port-size 0;
      flex-direction: column;
    }
    
    &--top, &--bottom {
      height: $port-size;
      width: 100%;
      padding: 0 $port-width;
    }

    & > div {
      overflow: visible !important;
    }
  }
    
  &__center {
    display: flex;
    font-size: 12px;
    font-family: "Lucida Console", Monaco, monospace;
    justify-content: center;
    align-items: center;
    flex: 1;
    border: $stroke-width solid $stroke-color;
    background-color: $bg-color;
      min-width: $min-dimension;
      min-height: $min-dimension;
  }
}
</style>