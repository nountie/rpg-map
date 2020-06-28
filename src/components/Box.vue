<template>
    <div 
        class="box"
        :class="[boxClass, borderClasses]"
        :style="styles"
        @mouseover="onMouseOver"
        @mouseleave="onMouseLeave"
        @click="onClick"
    >
        <div 
            :style="innerStyles"
            class="box-inner"
            :class="[boxInnerClass]"
        >
            <slot></slot>
        </div>
    </div>
</template>

<script>
export default {
    name: "Box",
    props: ['box', 'hoverPaint', 'unlocked', 'avaibleToUnlock', 'borders'],
    data() {
        return {
            isItHovered: false,
            boxClass: `box-${this.box}`,
            boxInnerClass: `box-${this.box}-inner`,
            transitionValue: "none"
        }
    },
    computed: {
        borderClasses() {
            return {
                'border-left': this.borders['left'],
                'border-right': this.borders['right'],
                'border-bottom': this.borders['bottom'],
                'border-top': this.borders['top'],
            }
        },
        styles() {
            return {
                'box-shadow': this.calculateBorderColor(),
                // 'box-shadow': this.calculateBoxShadowBorder(),
                // 'border-right': (this.borders['right'])? '1px solid red' : '1px solid blue',
                // 'border-bottom': (this.borders['bottom'])? '1px solid red' : '1px solid blue',
                // 'border-left': (this.borders['left'])? '1px solid red' : '1px solid blue',
                // 'border-top': (this.borders['top'])? '1px solid red' : '1px solid blue',
            }
        },
        innerStyles() {
            return {
                'box-shadow': this.calculateBorderColor('small'),
                opacity: this.calculateOpacity(),
                cursor: this.calculateCursor(),
            }
        }
    },
    methods: {
        onMouseOver() {
            this.isItHovered = true;
            this.$emit('boxHovered', this.box);
        },
        onMouseLeave() {
            this.isItHovered = false;
        },
        onClick() {
            this.$emit('boxClicked', this.box);            
        },
        calculateCursor() {
            if(this.avaibleToUnlock || this.unlocked) return "pointer";
            else return "help";
        },
        calculateOpacity() {
            if(this.hoverPaint && this.unlocked) return 0.3;
            if(this.unlocked) return 0;
            if(this.hoverPaint && this.avaibleToUnlock) return 0.8;
            if(this.avaibleToUnlock) return 0.95;
            else return 1;
        },
        calculateBorderColor(type) {
            // console.log(type);
            if(type === 'small') {
                if(this.unlocked || this.avaibleToUnlock) return "none";
                else return "0px 0px 0px 1px rgba(0, 0, 0, 0.18)";
            } else {
                if(this.unlocked) return "0px 0px 0px 1px rgba(0, 0, 0, 0.18)";
                else return "0px 0px 0px 1px rgba(0, 0, 0, 0.18)";
            }
        }

    }
}
</script>

<style lang="scss">
   .box {
        width: 60px;
        height: 32px;
        color: #bbb;
        // box-shadow: 0px 0px 5px 5px rgba(0, 0, 0, 0.1);
        transition: all 0.1s ease-in-out;
        &-inner{
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100%;
            width: 100%;
            transition: all 0.1s ease-in-out;
            transition-delay: 0s;
        }
    }
    .border-top::after,
    .border-left::before {
        content: "";
        background: rgba(54, 54, 54, 0.4);
        position: absolute;
        display: block;
        z-index: 999999;
    }
    .border-top::after {
        top: 0;
        left: 0;
        height: 1px;
        width: 100%;
        // box-shadow: 0px 0px 0px 0px rgba(0, 0, 0, 1);
    }
    .border-left::before {
        top: 0;
        height: 100%;
        width: 1px;
        // box-shadow: 0px 0px 0px 0px rgba(0, 0, 0, 1);
    }

    @for $i from 0 through 30 {
        .box-#{$i} {
            z-index: $i
        }
    }
        
    @for $i from 0 through 10 {
        .box-#{$i*3} {
            position: relative;
            &-inner {
                background-color: lighten(rgb(32, 35, 39), $i * 0.45%);
                // background-color: adjust-hue(green, $i * 100%);
            }
        }
        .box-#{$i*3-1} {
            position: relative;
            &-inner {
                background-color: lighten(rgb(40, 48, 58), $i * 0.45%);
                // background-color: adjust-hue(green, $i * 100%);
            }
        }
        .box-#{$i*3-2} {
            position: relative;
            &-inner {
                background-color: lighten(rgb(34, 36, 44), $i * 0.45%);
                // background-color: adjust-hue(green, $i * 100%);
            }
        }

    }


</style>