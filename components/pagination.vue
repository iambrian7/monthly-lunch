<template>
  <div class="pagination">

    <!-- <a v-if="pages.prevPage" @click="$emit('prev')" class="button pagination-previous"> -->
    <a @click="$emit('prev')" class="button pagination-previous">
      Prev
    </a>
    <span class="page-stats">{{pages.from}} - {{pages.to}} of {{pages.total}} </span>
    <!-- <a v-else disabled class="button pagination-previous">
      Prev
    </a> -->
    <!-- <a v-if="pages.nextPage" @click="$emit('next')" class="button pagination-next"> -->
    <a @click="$emit('next')" class="button pagination-next">
      Next
    </a>
    <!-- <a v-else disabled class="button pagination-next">
      Next
    </a> -->
  </div>
</template>

<script>
 module.exports = {
  props: ['pages'],
  mounted(){
    console.log("pagination mounted........")
  }
  }
</script>

<style>
  .pagination{
      border: 1px solid black;
    font-size: 1em;
    /* height: 50px; */
    display: flex;
    margin-top: 12px;
    align-items: center;
    background: white;
    color: black;
  }
  .button{
    /* width: 150px;
    height: 30px; */
    border: 1px solid black;
    background: green;
    padding: 5px;
    margin: 3px 20px;
    cursor: pointer;
  }
  .button:hover {
    background: red;
  }
</style>