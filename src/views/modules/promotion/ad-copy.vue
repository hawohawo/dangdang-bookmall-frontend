<template>
  <div></div>
</template>

<script>
export default {
  methods: {
    // 获取数据列表
    getAdList () {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/promotion/ad/list'),
        method: 'get'
      }).then(({ data }) => {
        console.log(data.page.list)
      })
    }

  },
  created () {
    this.getAdList()
  }
}
</script>

<style>
</style>