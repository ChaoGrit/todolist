<template>
<div>
  <ul class="pages-list clearfix">
    <li><a class="page-btn" @click="prePage()" href="javascript:;"><</a></li>
    <li v-for="index in indexes" :class="{active: current == index}"><a class="page-btn" @click="gotoPage(index)" href="javascript:;">{{index<1?"...":index}}</a></li>
    <li><a class="page-btn" @click="nextPage()" href="javascript:;">></a></li>
  </ul>
</div> 
</template>

<script>
  export default {
  	name: 'pagination',
    props: ['initCurrent', 'totalPage'],
    data: function() {
      return {
        current: this.initCurrent
      }
    },
    computed: {
      indexes: function() {
        var first = 1;
        var last = this.totalPage;
        var indexes = [];
        if (last >= 11) {
          if (this.current > 5 && this.current < this.totalPage - 4) {
            first = this.current - 5;
            last = this.current + 4;
          } else {
            if (this.current <= 5) {
              first = 1;
              last = 10;
            } else {
              first = this.totalPage - 9;
              last = this.totalPage;
            }
          }
        }
        while (first <= last) { //加成一个数组
          indexes.push(first);
          first++;
        }
        if (indexes[0] > 1) { //头部省略号判断
          indexes[0] = 1;
          indexes[1] = -1; //手动置0
        }
        if (indexes[indexes.length - 1] < this.totalPage) { //尾部省略号判断
          indexes[indexes.length - 1] = this.totalPage;
          indexes[indexes.length - 2] = -1;
        }
        return indexes;
      }
    },
    methods: {
      nextPage: function(index) {
        if (this.current >= this.totalPage) {
          return;
        }
        this.gotoPage(this.current + 1);
      },
      prePage: function() {
        if (this.current <= 1) {
          return;
        }
        this.gotoPage(this.current - 1);
      },
      gotoPage: function(page) {
        if (page < 1) {
          return;
        }
        if (page != this.current) {
          this.current = page;
        }
        this.$emit('initCurrent', page);
      }
    }
  }
</script>
<style scoped>
	a{text-decoration: none;}
  .clearfix{
    zoom: 1;
  }
  .clearfix:after{
    display: block;
    content: '';
    font-size: 0;
    width: 0;
    height: 0;
    line-height: 0;
    overflow: hidden;
    clear: both;
  }
  .pages-list{
    list-style: none;
  }
  .pages-list>li{
    float: left;
  }
  li>a{
    display: inline-block;
    padding: 8px 15px;
    font-size: 12px;
    background-color: #eee;
    border: 1px solid #ddd;
    color: #23527c;
  }
  li>a:hover{
    opacity: 0.8;
  }
  .page-button{
    display: inline-block;
    padding: 8px 15px;
    font-size: 12px;
    background-color: #eee;
    border: 1px solid #ddd;
    color: #23527c;
  }
  li.active>a{
    color: #eee;
    cursor: default;
    background-color: #23527c;
    border-color: #23527c;
  }
</style>