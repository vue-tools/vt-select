<style src="./style.css"></style>

<script>
    import { Dropdown, DropdownMenu, DropdownItem } from 'vt-dropdown'

    export default {
        name: 'Selects',
        props: {
            name: String,
            size: String,
            form: String,
            value: [String, Number, Boolean],
            disabled: Boolean,
            autofocus: Boolean,
            placeholder: String
        },
        data() {
            return {
                label: '',
                options: [],
                activeIcon: false
            }
        },
        render(h) {
            if(this.disabled) {
                return createInputElement(h, this)
            }

            return (
                <div>
                    <div class="ui-select__option">{this.$slots.default}</div>
                    <Dropdown trigger="click"
                        onShow={() => this.activeIcon = true}
                        onHide={() => this.activeIcon = false}
                        onClick={(item) => {
                            this.label = item.$el.dataset.label
                            this.$emit('input', item.$el.dataset.value)
                        }}
                    >
                        {createInputElement(h, this)}
                        <DropdownMenu class="ui-select__dropdown">
                            {createDropdownItem(h, this.options)}
                        </DropdownMenu>
                    </Dropdown>
                </div>
            )
        },
        updated() {
            let values = this.options.map((item) => {
                if(item.display !== 'none') {
                    return String(item.value)
                }
            })

            if(this.value !== ''  && values.indexOf(String(this.value)) === -1) {
                this.label = ''
                this.$emit('input', '')
            }
        },
        mounted() {
            for (let item of this.options) {
                if (this.value == item.value) {
                    this.label = item.label
                    break
                }

                if (item.selected) {
                    this.label = item.label
                    this.$emit('input', item.value)
                    break
                }
            }
        },
        watch: {
            value(val, oldVal) {
                val = String(val)

                // if(!val.length) {
                //     return
                // }
                
                if(this.options.map((item) => String(item.value)).indexOf(val) === -1) {
                    for (let item of this.options) {
                        if (item.value == oldVal) {
                            this.label = item.label
                            break
                        }
                    }
                    
                    this.$emit('input', oldVal)
                } else {
                    for (let item of this.options) {
                        if (val == item.value) {
                            this.label = item.label
                            break
                        }
                    }
                }
            }
        },
        components: {
            Dropdown,
            DropdownMenu,
            DropdownItem
        }
    }

    function createInputElement(h, context) {
        let className = {
            'ui-select__input': true,
            'ui-select__input--mini': context.size === 'mini',
            'ui-select__input--large': context.size === 'large'
        }

        return (
            <div class="ui-select__wrap">
                <input class={className} type="text" name={context.name} form={context.form} value={context.label}
                    autocomplete="off" readonly="readonly" disabled={context.disabled} placeholder={context.placeholder} />
                <span class={{'ui-select__icon': true, 'ui-select__icon--active': context.activeIcon}}></span>
            </div>
        )
    }

    function createDropdownItem(h, options) {
        return options.map((item) => {
            return (<DropdownItem data-value={item.value} data-label={item.label} style={{'display': item.display}} disabled={item.disabled}>{item.vnode}</DropdownItem>)
        })
    }
</script>