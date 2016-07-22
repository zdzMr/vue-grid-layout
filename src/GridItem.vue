<template>
    <div v-el:item
             class="vue-grid-item"
             :class=""
             :style="style"
        >
        <slot></slot>
        <!--<pre>{{style | json}}</pre>-->
        <!--span class="text">{{id}}</span-->
            <!--<span class="text">{{i}}</span>-->
            <!--<pre>
                x: {{ x | json}}
                y: {{ y | json}}
                w: {{ w | json}}
                h: {{ h | json}}
            </pre>-->
            <span v-if="isResizable" @mousedown="startResize" v-el:handle class="vue-resizable-handle"></span>
        </div>
    </div>
</template>
<style>
    .vue-grid-item {
        transition: all 200ms ease;
        transition-property: left, top;
    }
    .vue-grid-item.cssTransforms {
        transition-property: transform;
    }
    .vue-grid-item.resizing {
        z-index: 1;
        opacity: 0.9;
    }

    .vue-grid-item.vue-draggable-dragging {
        /*transition: none;*/
        /*background-color: red;*/
        z-index: 3;
    }

    .vue-grid-item.vue-grid-placeholder {
        background: red;
        opacity: 0.2;
        transition-duration: 100ms;
        z-index: 2;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        -o-user-select: none;
        user-select: none;
    }

    .vue-grid-item > .vue-resizable-handle {
        position: absolute;
        width: 20px;
        height: 20px;
        bottom: 0;
        right: 0;
        background: url('data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBzdGFuZGFsb25lPSJubyI/Pg08IS0tIEdlbmVyYXRvcjogQWRvYmUgRmlyZXdvcmtzIENTNiwgRXhwb3J0IFNWRyBFeHRlbnNpb24gYnkgQWFyb24gQmVhbGwgKGh0dHA6Ly9maXJld29ya3MuYWJlYWxsLmNvbSkgLiBWZXJzaW9uOiAwLjYuMSAgLS0+DTwhRE9DVFlQRSBzdmcgUFVCTElDICItLy9XM0MvL0RURCBTVkcgMS4xLy9FTiIgImh0dHA6Ly93d3cudzMub3JnL0dyYXBoaWNzL1NWRy8xLjEvRFREL3N2ZzExLmR0ZCI+DTxzdmcgaWQ9IlVudGl0bGVkLVBhZ2UlMjAxIiB2aWV3Qm94PSIwIDAgNiA2IiBzdHlsZT0iYmFja2dyb3VuZC1jb2xvcjojZmZmZmZmMDAiIHZlcnNpb249IjEuMSINCXhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgeG1sbnM6eGxpbms9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGxpbmsiIHhtbDpzcGFjZT0icHJlc2VydmUiDQl4PSIwcHgiIHk9IjBweCIgd2lkdGg9IjZweCIgaGVpZ2h0PSI2cHgiDT4NCTxnIG9wYWNpdHk9IjAuMzAyIj4NCQk8cGF0aCBkPSJNIDYgNiBMIDAgNiBMIDAgNC4yIEwgNCA0LjIgTCA0LjIgNC4yIEwgNC4yIDAgTCA2IDAgTCA2IDYgTCA2IDYgWiIgZmlsbD0iIzAwMDAwMCIvPg0JPC9nPg08L3N2Zz4=');
        background-position: bottom right;
        padding: 0 3px 3px 0;
        background-repeat: no-repeat;
        background-origin: content-box;
        box-sizing: border-box;
        cursor: se-resize;
    }

    .vue-resizable {
        position: relative;
    }
    .vue-resizable-handle {
        z-index: 5000;
        position: absolute;
        width: 20px;
        height: 20px;
        bottom: 0;
        right: 0;
        background: url('data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBzdGFuZGFsb25lPSJubyI/Pg08IS0tIEdlbmVyYXRvcjogQWRvYmUgRmlyZXdvcmtzIENTNiwgRXhwb3J0IFNWRyBFeHRlbnNpb24gYnkgQWFyb24gQmVhbGwgKGh0dHA6Ly9maXJld29ya3MuYWJlYWxsLmNvbSkgLiBWZXJzaW9uOiAwLjYuMSAgLS0+DTwhRE9DVFlQRSBzdmcgUFVCTElDICItLy9XM0MvL0RURCBTVkcgMS4xLy9FTiIgImh0dHA6Ly93d3cudzMub3JnL0dyYXBoaWNzL1NWRy8xLjEvRFREL3N2ZzExLmR0ZCI+DTxzdmcgaWQ9IlVudGl0bGVkLVBhZ2UlMjAxIiB2aWV3Qm94PSIwIDAgNiA2IiBzdHlsZT0iYmFja2dyb3VuZC1jb2xvcjojZmZmZmZmMDAiIHZlcnNpb249IjEuMSINCXhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgeG1sbnM6eGxpbms9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGxpbmsiIHhtbDpzcGFjZT0icHJlc2VydmUiDQl4PSIwcHgiIHk9IjBweCIgd2lkdGg9IjZweCIgaGVpZ2h0PSI2cHgiDT4NCTxnIG9wYWNpdHk9IjAuMzAyIj4NCQk8cGF0aCBkPSJNIDYgNiBMIDAgNiBMIDAgNC4yIEwgNCA0LjIgTCA0LjIgNC4yIEwgNC4yIDAgTCA2IDAgTCA2IDYgTCA2IDYgWiIgZmlsbD0iIzAwMDAwMCIvPg0JPC9nPg08L3N2Zz4=');
        background-position: bottom right;
        padding: 0 3px 3px 0;
        background-repeat: no-repeat;
        background-origin: content-box;
        box-sizing: border-box;
        cursor: se-resize;
    }

    .vue-grid-item:not(.vue-grid-placeholder) {
        background: #ccc;
        border: 1px solid black;
    }

    .vue-grid-item.static {
        background: #cce;
    }
    .vue-grid-item .text {
        font-size: 24px;
        text-align: center;
        /*position: absolute;*/
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        margin: auto;
        height: 24px;
    }
    .vue-grid-item .minMax {
        font-size: 12px;
    }
    .vue-grid-item .add {
        cursor: pointer;
    }

</style>
<script>

    var Vue = require('vue');
    var interact = require("interact.js");

    export default {
        name: "GridItem",
        props: {
            static: {
                type: Boolean,
                required: false,
                default: false
            },
            x: {
                type: Number,
                required: true

            },
            y: {
                type: Number,
                required: true
            },
            w: {
                type: Number,
                required: true
            },
            h: {
                type: Number,
                required: true
            },
            i: {
                required: true
            },
        },
        data: function() {
            return {
                cols: 1,
                /*margin: [10, 10],
                maxRows: Infinity,*/
                minH: 1,
                minW: 1,
                maxH: Infinity,
                maxW: Infinity,
                isDraggable: true,
                isResizable: true,

                isDragging: false,
                className: "",
                style: {
                    "padding": "5px",
                    "margin": "5px",
                    "width": "100%",
                    "box-sizing": "border-box",
                    "float": "left",
                    "min-height": "1px",
                    "position": "relative",
                }
            }
        },
        ready: function() {
            this.cols = this.$parent.colNum;

            /*this.margin = this.$parent.margin;
            this.maxRows = this.$parent.maxRows;*/
            this.minH = this.$parent.minH;
            this.minW = this.$parent.minW;
            this.maxH = this.$parent.maxH;
            this.maxW = this.$parent.maxW;
            this.isDraggable = this.$parent.isDraggable;
            this.isResizable = this.$parent.isResizable;

            this.calcWidth();
            if (this.isResizable) {
                var self = this;
                interact(this.$els.item)
                        .resizable({
                            preserveAspectRatio: false,
                            edges: {left: false, right: true, bottom: true, top: false}
                        })
                        .on('resizemove', function (event) {

                            var target = event.target;

                            // update the element's style
                            //self.style.width = event.rect.width + 'px';
                            //var style = self.style;
                            self.style["height"] = event.rect.height + 'px';
                            self.style["width"] = event.rect.width + 'px';
//                            target.style.height = self.style["height"];
//                            target.style.width = self.style["width"];
                            //self.style = style;

                            console.log("width: " + event.rect.width + ", height: " + event.rect.height);
                        });
            }
        },
        watch: {
        },
        computed: {
        },
        methods: {
            calcWidth() {
                var numCols = this.w;
                if (this.w > this.cols) {
                    numCols = this.cols;
                }
                var colWidth = numCols * 100 / this.cols;
                //console.log("### i=" + this.i + " W=" + this.w + " WIDTH=" + colWidth);
                this.style["width"] = colWidth + "%";
            },
            startResize() {
                console.log("start resize!");
            }
        },
        events: {
        }
    }
</script>