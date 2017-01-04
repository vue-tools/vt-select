<style src="./style.css"></style>

<script>
    import { Dropdown, DropdownMenu, DropdownItem } from 'vt-dropdown'

    export default {
        name: 'Selects',
        props: {
            name: String,
            size: String,
            form: String,
            value: String,
            disabled: Boolean,
            autofocus: Boolean,
            placeholder: String
        },
        data() {
            return {
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
                        onClick={(item) => this.$emit('input', item.$el.dataset.value)}
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
                    return item.value
                }
            })

            if(this.value !== ''  && values.indexOf(this.value) === -1) {
                this.$emit('input', '')
            }
        },
        mounted() {
            this.options.map((item) => {
                if (item.selected) {
                    this.$emit('input', item.value)
                }
            })
        },
        watch: {
            value(val, oldVal) {
                if(!val.length) {
                    return
                }
                
                if(this.options.map((item) => item.value).indexOf(val) === -1) {
                    this.$emit('input', oldVal)
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
                <input class={className} type="text" name={context.name} form={context.form} value={context.value}
                    autocomplete="off" readonly="readonly" disabled={context.disabled} placeholder={context.placeholder} />
                <span class={{'ui-select__icon': true, 'ui-select__icon--active': context.activeIcon}}></span>
            </div>
        )
    }

    function createDropdownItem(h, options) {
        return options.map((item) => {
            return (<DropdownItem data-value={item.value} style={{'display': item.display}} disabled={item.disabled}>{item.vnode}</DropdownItem>)
        })
    }
</script>