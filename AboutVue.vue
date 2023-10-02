<template>
  <div>
    <h1>Introduction</h1>
    <p>Meet the Team:</p>
    <ul class="ull"
>
      <li>Team Member 1: {{ member1 }}</li>
      <br>
      <li>Team Member 2: {{ member2 }}</li>
      <br>
      <li>Team Member 3: {{ member3 }}</li>
    </ul>
  </div>

  <div class="card">
  


  </div>
</template>

<style>
.ull {
  float: none;
  background-color: white;
}

h1 {
  text-align: center;
}
</style>


<script>
export default {
  props: {
    member1: String,
    member2: String,
    member3: String
  },
}
</script>
