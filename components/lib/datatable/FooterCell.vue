<template>
    <td :style="containerStyle" :class="containerClass" role="cell" :colspan="columnProp('colspan')" :rowspan="columnProp('rowspan')" v-bind="{ ...getColumnPT('root'), ...getColumnPT('footerCell') }" :data-p-frozen-column="columnProp('frozen')">
        <component v-if="column.children && column.children.footer" :is="column.children.footer" :column="column" />
        {{ columnProp('footer') }}
    </td>
</template>

<script>
import BaseComponent from 'primevue/basecomponent';
import { DomHandler, ObjectUtils } from 'primevue/utils';
import { mergeProps } from 'vue';

export default {
    name: 'FooterCell',
    hostName: 'DataTable',
    extends: BaseComponent,
    props: {
        column: {
            type: Object,
            default: null
        },
        index: {
            type: Number,
            default: null
        }
    },
    data() {
        return {
            styleObject: {}
        };
    },
    mounted() {
        if (this.columnProp('frozen')) {
            this.updateStickyPosition();
        }
    },
    updated() {
        if (this.columnProp('frozen')) {
            this.updateStickyPosition();
        }
    },
    methods: {
        columnProp(prop) {
            return ObjectUtils.getVNodeProp(this.column, prop);
        },
        getColumnPT(key) {
            const columnMetaData = {
                props: this.column.props,
                parent: {
                    props: this.$props,
                    state: this.$data
                },
                context: {
                    index: this.index,
                    size: this.$parentInstance?.$parentInstance?.size,
                    showGridlines: this.$parentInstance?.$parentInstance?.showGridlines || false
                }
            };

            return mergeProps(this.ptm(`column.${key}`, { column: columnMetaData }), this.ptm(`column.${key}`, columnMetaData), this.ptmo(this.getColumnProp(), key, columnMetaData));
        },
        getColumnProp() {
            return this.column.props && this.column.props.pt ? this.column.props.pt : undefined;
        },
        updateStickyPosition() {
            if (this.columnProp('frozen')) {
                let align = this.columnProp('alignFrozen');

                if (align === 'right') {
                    let right = 0;
                    let next = DomHandler.getNextElementSibling(this.$el, '[data-p-frozen-column="true"]');

                    if (next) {
                        right = DomHandler.getOuterWidth(next) + parseFloat(next.style.right || 0);
                    }

                    this.styleObject.right = right + 'px';
                } else {
                    let left = 0;
                    let prev = DomHandler.getPreviousElementSibling(this.$el, '[data-p-frozen-column="true"]');

                    if (prev) {
                        left = DomHandler.getOuterWidth(prev) + parseFloat(prev.style.left || 0);
                    }

                    this.styleObject.left = left + 'px';
                }
            }
        }
    },
    computed: {
        containerClass() {
            return [this.columnProp('footerClass'), this.columnProp('class'), this.cx('footerCell')];
        },
        containerStyle() {
            let bodyStyle = this.columnProp('footerStyle');
            let columnStyle = this.columnProp('style');

            return this.columnProp('frozen') ? [columnStyle, bodyStyle, this.styleObject] : [columnStyle, bodyStyle];
        }
    }
};
</script>
