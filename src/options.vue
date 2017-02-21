<template>
    <div>
        <slot></slot>
    </div>
</template>

<script>
    export default {
        props: {
            value: String,
            label: String,
            disabled: Boolean,
            selected: Boolean
        },
        data() {
            return {
                index: 0
            }
        },
        methods: {
            checkParent() {
                if(this.$parent.$options.name !== 'Selects') {
                    console.error('Options component must within the Selects component')
                    return false
                }

                return true
            }
        },
        updated() {
            this.$parent.options[this.index].display = this.$el.style.display
        },
        mounted() {
            if(this.checkParent()) {
                this.index = this.$parent.options.length
                
                this.$parent.options.push({
                    value: this.value,
                    label: this.label,
                    disabled: this.disabled,
                    selected: this.selected,
                    vnode: this.$slots.default,
                    display: this.$el.style.display
                })
            }
        },
        beforeDestroy() {
            this.$parent.options.splice(this.index, 1)
        }
    }
</script>