<template>
  <div id="app">
    <nav class="navbar navbar-toggleable-md navbar-inverse fixed-top bg-inverse">
      <div class="container">
        <button class="navbar-toggler navbar-toggler-right" type="button" data-toggle="collapse" data-target="#navbarsExampleDefault" aria-controls="navbarsExampleDefault" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
        <a class="navbar-brand" href="#">SprintTracker</a>
      </div>
    </nav>
    <div class="main-container container">
      <router-view/>
    </div>
  </div>
</template>

<script src="../node_modules/vue-awesome/dist/vue-awesome.js"></script>
<script>
export default {
  name: 'app',
};
</script>

<style>
  .main-container {
    margin-top: 100px;
  }
</style>
