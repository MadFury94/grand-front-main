<template>
  <div class="mt-2">
    <label :for="name" class="form-label"
      >{{ label }} :<span
        v-if="isNeed"
        class="text-red-500 font-semibold text-md"
        >*</span
      >
    </label>
    <div class="mt-1">
      <VeeField
        :name="name"
        :label="label"
        :modelValue="modelValue"
        @input="$emit('modelUpdate', $event)"
        :class="className"
        :placeholder="placeholder"
        :id="name"
        v-bind="$attrs"
        :type="type"
        :readonly="readonly"
      />
    </div>
    <VeeErrorMessage class="input-error-msg" :name="name" />
  </div>
</template>

<script>
import { useField } from "vee-validate";

export default {
  props: {
    type: {
      type: String,
      default: "text",
    },
    className: {
      type: String,
      default: "input-box",
    },
    name: {
      type: String,
      required: true,
    },
    isNeed: {
      type: Boolean,
      default: false,
    },
    placeholder: {
      type: String,
      default: "",
    },
    label: {
      type: String,
      required: true,
    },
    modelValue: {
      type: String,
      default: "",
    },
    readonly: {
      type: Boolean,
      default: false,
    },
  },
};
</script>

<style scoped></style>
