<template>
    <div :class="['form-group','row',inputState]"
         :id="id || null"
         role="group"
         :aria-describedby="describedBy"
    >
        <label v-if="label || $slots['label']"
               :for="target"
               :id="labelId"
               :class="[labelSrOnly ? 'sr-only' : 'col-form-label',labelLayout,labelAlignClass,labelOffsetLayout]"
        >
            <slot name="label"><span v-html="label"></span></slot>
        </label>
        <div :class="inputLayout" ref="content">
            <slot></slot>
            <div v-if="feedback || $slots['feedback']"
                 class="form-text form-control-feedback"
                 :id="feedbackId"
                 role="alert"
                 aria-live="assertive"
                 aria-atomic="true"
            >
                <slot name="feedback"><span v-html="feedback"></span></slot>
            </div>
            <small v-if="description || $slots['description']"
                   class="form-text text-muted"
                   :id="descriptionId"
            >
                <slot name="description"><span v-html="description"></span></slot>
            </small>
        </div>
    </div>
</template>

<script>
    import {warn} from '../utils';

    const INPUT_SELECTOR = [
        '[role="radiogroup"]',
        'input',
        'select',
        'textarea',
        '.form-control',
        '.form-control-static',
        '.dropdown',
        '.dropup'
    ].join(',');

    export default {
        data() {
            return {
                target: null
            };
        },
        computed: {
            labelId() {
                return (this.id && this.label) ? (this.id + '__BV_label_') : null;
            },
            descriptionId() {
                return (this.id && this.description) ? (this.id + '__BV_description_') : null;
            },
            feedbackId() {
                return (this.id && this.feedback) ? (this.id + '__BV_feedback_') : null;
            },
            describedBy() {
                if (this.id && (this.label || this.feedback || this.description)) {
                    return [
                        this.labelId,
                        this.descriptionId,
                        this.feedbackId
                    ].filter(i => i).join(' ');
                }
                return null;
            },
            inputState() {
                return this.state ? `has-${this.state}` : '';
            },
            computedLabelCols() {
                if (this.labelSize) {
                    warn('b-form-fieldset: prop label-size has been deprecated. Use label-cols instead');
                    return this.labelSize;
                }
                return this.labelCols;
            },
            labelOffsetLayout() {
              return this.horizontal ? ('offset-'+ this.labelOffset) : null;
            },
            labelLayout() {
                if (this.labelSrOnly) {
                    return null;
                }
                return this.horizontal ? ('col-sm-' + this.computedLabelCols) : 'col-12';
            },
            labelAlignClass() {
                if (this.labelSrOnly) {
                    return null;
                }
                return this.labelTextAlign ? `text-${this.labelTextAlign}` : null;
            },
            inputLayout() {
                return this.horizontal ? ('col-sm-' + (12 - this.computedLabelCols - this.labelOffset)) : 'col-12';
            }
        },
        methods: {
            updateTarget() {
                if (this.labelFor) {
                    // User supplied for target
                    return this.labelFor;
                }
                // Else find first input with ID
                const content = this.$refs.content;
                if (!content) {
                    return null;
                }
                const input = content.querySelector(this.inputSelector);
                this.target = (input && input.id) ? input.id : null;
            }
        },
        mounted() {
            this.updateTarget();
        },
        updated() {
            this.updateTarget();
        },
        props: {
            id: {
                type: String,
                default: null
            },
            labelFor: {
                type: String,
                default() {
                    if (this && this.for) {
                        // Deprecate prop for
                        warn("b-form-fieldet: prop 'for' has been deprecated. Use 'label-for' instead");
                        return this.for;
                    }
                    return null;
                }
            },
            for: {
                type: String,
                default: null
            },
            state: {
                type: String,
                default: null
            },
            horizontal: {
                type: Boolean,
                default: false
            },
            labelCols: {
                type: Number,
                default: 3,
                validator(value) {
                    if (value >= 1 && value <= 11) {
                        return true;
                    }
                    warn('b-form-fieldset: label-cols must be a value between 1 and 11');
                    return false;
                }
            },
            labelOffset: {
                type: Number,
                default: 0,
                validator(value) {
                    if (value >= 0 && value <= 10) {
                        return true;
                    }
                    warn('b-form-fieldset: label-offset must be a value between 0 and 10');
                    return false;
                }
            },
            labelSize: {
                type: Number
            },
            labelTextAlign: {
                type: String,
                default: null
            },
            label: {
                type: String,
                default: null
            },
            labelSrOnly: {
                type: Boolean,
                default: false
            },
            description: {
                type: String,
                default: null
            },
            feedback: {
                type: String,
                default: null
            },
            inputSelector: {
                type: String,
                default: INPUT_SELECTOR
            }
        }
    };
</script>
