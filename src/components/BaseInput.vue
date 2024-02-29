<template>

  <label for="input">{{ inputLabel }}: </label>
  <input :type="tipoInput"
         :value="modelValue"
         name="input"
         id="input"
         @input="$emit('update:modelValue', $event.target.value)">

</template>

<script>
  export default {
    name: 'BaseInput',
    inheritAttrs: false,
    emits: ['update:modelValue'],
    props: {
      inputLabel: {String, default: ''},
      tipoInput: {String, default: 'text'},
      modelValue: [String, Number]
    }
  }

</script>


<style>

</style>