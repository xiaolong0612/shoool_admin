<template>
  <div class="errPage-container">
     <!--error code-->
   <err-code/>
    <!--error code-->
    <h3>请点击右上角bug小图表</h3>
     <code>
      现在的管理后台基本都是spa的形式了，它增强了用户体验，但同时也会怎增加页面出问题的可能性，可能一个小小的疏忽就导致整个页面的死锁。好在Vue官网提供了一个方法来捕获处理异常
    </code>

  </div>
</template>
<script>
    import errCode from './errcode';
    // import code1Img from
    export default {
      components: { errCode },
      data() {
        return {

        }
      },
      methods: {
        back() {
          this.$router.go(-1)
        }
      }
    };
</script>
<style scoped>
.errPage-container{
  padding: 30px;
}
</style>
