<template>
    <div
        :class="['add-item', `addItem${i}`]"
        ref="addItem"
    >
        <slot></slot>
    </div>
</template>
<style lang="scss" scoped>
</style>
<script>
import interact from 'interactjs'

export default {
    name: "AddElement",
    props: {
        clone: {
            type: Boolean,
            default: () => false,
            required: false
        },
        toArea: {
            type: Object,
            required: false
        },
        i: {
            required: true
        },
        w: {
            required: true
        },
        h: {
            required: true
        }
    },
    data() {
        return {
            isDraging: false
        }
    },
    inject: ['eventBus'],
    methods: {
        calcXY(top, left) {
            const colWidth = this.calcColWidth()
            const toArea = this.toArea

            let x = Math.round((left - toArea.margin[0]) / (colWidth + toArea.margin[0]))
            let y = Math.round((top - toArea.margin[1]) / (toArea.rowHeight + toArea.margin[1]))
            // Capping
            x = Math.max(Math.min(x, toArea.colNum - this.w), 0)
            y = Math.max(Math.min(y, toArea.maxRows - this.h), 0)
            return { x, y }
        },
        calcColWidth() {
            const toArea = this.toArea
            const colNum = toArea.colNum
            const calcColWidth = (toArea.width - (toArea.margin[0] * (colNum + 1))) / colNum
            return calcColWidth
        },
        getNewPosition(x, y) {
            const toArea = this.toArea
            const layoutPosition = toArea.$el.getBoundingClientRect()

            return {
                top: y - layoutPosition.y,
                left: x - layoutPosition.x
            }
        },
        dragMoveListener(event) {
            this.isDraging = true
            const target = event.target
            const toArea = this.toArea
            const x = (parseFloat(target.getAttribute('data-x')) || 0) + event.dx
            const y = (parseFloat(target.getAttribute('data-y')) || 0) + event.dy
            const layoutPosition = toArea.$el.getBoundingClientRect()
            const calcColWidth = this.calcColWidth()
            const newPosition = this.getNewPosition(x, y)
            const calcXY = this.calcXY(newPosition.top, newPosition.left)
            // translate the element
            target.style.webkitTransform =
                target.style.transform =
                'translate(' + x + 'px, ' + y + 'px)'
            // update the posiion attributes
            target.setAttribute('data-x', x)
            target.setAttribute('data-y', y)
            this.toArea.dragEvent(event.type, this.i, calcXY.x, calcXY.y, this.h, this.w)
        }
    },
    mounted() {
        const self = this
        interact(`.addItem${self.i}`)
            .draggable({ onmove: self.dragMoveListener, onend: self.dragMoveListener})
            .on('move', event => {
                const interaction = event.interaction;
                // if the pointer was moved while being held down
                // and an interaction hasn't started yet
                if (interaction.pointerIsDown && !interaction.interacting()) {
                    const original = event.currentTarget
                    // create a clone of the currentTarget element
                    const clone = event.currentTarget.cloneNode(true)

                    // insert the clone to the page
                    // TODO: position the clone appropriately
                    // document.body.appendChild(clone)
                    clone.style.position = 'absolute'
                    clone.style.zIndex = '1'
                    document.querySelector('.add-container').insertBefore(clone, original.nextSibling)
                    // start a drag interaction targeting the clone
                    interaction.start({ name: 'drag' }, event.interactable, clone)
                }
            })
            .on('dragend', event => {
                // 删除复制出来的元素
                const original = event.currentTarget
                original && original.parentNode.removeChild(original)

            })

    }
}
</script>