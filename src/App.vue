<template>
      <!-- Navigation-->
        <nav class="navbar navbar-light bg-light static-top">
            <div class="container">
                <a class="navbar-brand" href="#!">Traveller's Guide</a>
                <div>
                <!-- <a class="btn btn-primary btn-sm me-xxl-3" href="javascript:;">Sign In</a> 
                <a class="btn btn-primary btn-sm" href="javascript:;">Sign Up</a> -->
                </div>
            </div>
        </nav>
      <HomePage />
</template>

<script>
import HomePage from './components/Home.vue'

export default {
  name: 'App',
  components: {
    HomePage
  }
}
</script>

<style>

</style>
