<template>
<!-- Masthead-->
        <header class="masthead">
            <div class="container position-relative">
                <div class="row justify-content-center">
                    <div class="col-xl-7">
                        <div class="text-center text-white">
                            <!-- Page heading-->
                            <h1 class="mb-5">Near by Tourist attractions</h1>
                              <form class="form-subscribe">
                                <div class="row">
                                    <div class="col">
                                        <input class="form-control form-control-lg" v-model="placeId" type="text" required  placeholder="Enter Place Name"/>
                                    </div>
                                    <div class="col-auto">
                                      <button class="btn btn-primary btn-lg" @click="submitForm(placeId)" type="button">Submit</button>
                                      </div>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </header>
  <div class="home">
    
    <PlaceInfo
      v-if="placeInfo"
      :placeId="placeId"
    />
  </div>
</template>

<script>
import PlaceInfo from './PlaceInfo.vue';

export default {
  name: 'HomePage',
  components: {
    PlaceInfo,
  },
  data() {
    return {
      placeId: '',
      placeInfo: null,
    };
  },
  methods: {
    submitForm(placeId) {
      this.placeInfo = placeId;
    },
  },
};
</script>
