<template>
  <div class="row col q-col-gutter-x-xs q-px-sm items-center">
    <div class="col">
      <q-input standout dense v-model="localValue" placeholder="Search" :debounce="500">
        <template v-slot:prepend>
          <q-icon name="fal fa-search icon-md" />
        </template>
      </q-input>
    </div>
    <div>
      <q-btn label="Cancel" unelevated @click="$emit('close')"/>
    </div>
  </div>
</template>

<script>
export default {
  name: 'LocalSearchInput',
  props: {
    value: {
      type: String,
      default: '',
    },
  },
  computed: {
    localValue: {
      get() {
        return this.value;
      },
      set(value) {
        this.$emit('input', value);
      },
    },
  },
};
</script>
