<template>
  <div :class="['vx-input-number',`vx-input-number--size-${size}`,{'is-disabled': disabled}]">
    <div class="vx-input-number--inner">
      <button :disabled="min !== undefined ? value <= min : false" class="vx-input-number--reduce" type="button" @click="handleChange(value-buttonStep)"></button>
      <input
        v-bind="$$props"
        type="number"
        :value="myValue"
        v-on="$$listeners"/>
      <button :disabled="max !== undefined ? max >= value  : false" class="vx-input-number--add" type="button" @click="handleChange(value+buttonStep)"></button>
    </div>
  </div>
</template>

<script>
import { input } from 'utils/mixins'
export default {
  name: 'InputNumber',
  componentName: 'InputNumber',
  mixins: [input],
  props: {
    ...input.props,
    value: {
      type: Number,
      default: 0
    },
    min: {
      type: Number
    },
    max: {
      type: Number
    },
    step: {
      type: Number,
      default: 1
    },
    buttonStep: {
      type: Number,
      default: 1
    },
    size: {
      type: String,
      default: 'default'
    }
  },
  computed: {
    myValue () {
      if (this.value < this.min) {
        return Math.round(this.min * this.stepRate) / this.stepRate
      }
      if (this.value > this.max) {
        return Math.round(this.max * this.stepRate) / this.stepRate
      }
      return Math.round(this.value * this.stepRate) / this.stepRate
    },
    stepRate () {
      return 1 / this.step
    },
    $$props () {
      return {
        ...this.$props,
        ...this.$attrs
      }
    },
    $$listeners () {
      return {
        ...this.$listeners,
        input: this.handleChange
      }
    }
  },
  methods: {
    handleChange (e) {
      let value = Number(e.target ? e.target.value : e)
      let node = this.$el.querySelector('input')
      if (isNaN(value)) {
        node && (node.value = this.value)
      } else {
        if (typeof this.max === 'number' && value > this.max) {
          value = this.max
        }
        if (typeof this.min === 'number' && value < this.min) {
          value = this.min
        }
        value = Math.round(value * this.stepRate) / this.stepRate
        node && (node.value = value)
        this.$emit('input', value).$emit('change', value).$emit('blur')
        this.eDispatch('ElFormItem', 'el.form.blur', [value])
        this.eDispatch('ElFormItem', 'el.form.change', [value])
      }
    }
  }
}
</script>
