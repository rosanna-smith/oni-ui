<template>
  <div class="min-w-full pb-4 pt-0 px-2 pl-4">
    <div class="bg-white">
    </div>
    <el-row :gutter="40" :offset="1" class="flex flex-row h-[calc(100vh-220px)] overflow-auto ">
      <div class="p-4">
        <div class="text-gray-600">
          <div class="p-2">
            <p class="font-medium text-2xl">{{ terms.title }}</p>
          </div>
          <div class="p-2" v-html="terms.text"></div>
        </div>
      </div>
    </el-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
      terms: this.$store.state.configuration.ui.terms,
    };
  },
  mounted() {},
};
</script>
