<template>
    <div class="add-container">
        <slot></slot>
    </div>
</template>
<style lang="scss" scoped>
</style>
<script>
import _ from 'lodash'
import Vue from 'vue'

export default {
    name: 'AddContainer',
    props: {
        layout: {
            required: true,
            default: () => []
        },
        toLayout: {
            required: true,
            default: () => []
        }
    },
    provide() {
        return {
            eventBus: new Vue()
        }
    },
    data() {
        return {
        }
    },
    created() {
        this.eventBus = this._provided.eventBus
    },
    methods: {

    }
}
</script>