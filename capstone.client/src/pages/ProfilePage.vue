<template>
  <div class="container-fluid background-img" v-if="!state.loading">
    <div class="row justify-content-around">
      <!-- profile stuff -->
      <div class="col-12 col-md-3" v-if="state.activeAccount">
        <div class="card bg-secondary shadow mt-2 mt-md-5">
          <div class="card-body">
            <h5 class="card-title d-flex justify-content-between">
              <div class="d-inline">
                {{ state.activeAccount.name }}
              </div>

              <div class="d-inline">
                <i class="fas fa-star star"></i>
                {{ getUserRating() }}
                <!-- {{ userRating }}
                {{ rating }} -->
                <button type="button"
                        class="btn p-0 ml-3 btn-none text-primary"
                        title="edit bio"
                        aria="edit bio"
                        data-toggle="modal"
                        data-target="#editBioModal"
                        v-if="route.params.id==state.account.id"
                >
                  <i class="fas fa-edit"></i>
                </button>
              </div>
            </h5>
            <div>
              <img class="img-fluid rounded mb-2" :src="state.activeAccount.picture" alt="profile picture">
            </div>
            <div v-if="state.activeAccount.id !== state.account.id">
              <router-link :to="{name: 'Messages', params: {id: state.activeAccount.id}}">
                <button
                  class="btn btn-success my-1"
                >
                  Message
                </button>
              </router-link>
            </div>
            <div class=" d-none d-md-block">
              <div class="card shadow mb-2">
                <div class="card-title m-0 lightgrey d-flex justify-content-between">
                  <span class="pl-2 py-2">
                    ABOUT ME
                  </span>
                  <!-- <button type="button"
                          class="btn btn-none text-primary"
                          title="edit bio"
                          aria="edit bio"
                          data-toggle="modal"
                          data-target="#editBioModal"
                  >
                    <i class="fas fa-edit"></i>
                  </button> -->
                </div>
                <div class="pl-2 py-2">
                  {{ state.activeAccount.bio }}
                </div>
              </div>

              <div class="card review-scroll shadow mb-2 ">
                <div class="card-title lightgrey m-0 d-flex justify-content-between">
                  <span class="pl-2 py-2 pl-3">
                    MY REVIEWS
                  </span>
                </div>
                <div class="row justify-content-center">
                  <span class="col-12 ml-4 pr-1">
                    <Review v-for="review in state.reviews" :key="review.id" :review="review" />
                  </span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- listings  -->
      <div class="col-12 col-md-9 mt-2 mt-md-5">
        <Item v-for="item in state.items" :key="item.id" :item="item" />
      </div>
      <div class="col-12 d-block d-md-none">
        <div class=" ">
          <div class="card review-scroll mb-2">
            <div class="card-title m-0 lightgrey d-flex justify-content-between">
              <span class="pl-2 py-2 ">
                ABOUT ME
              </span>
            </div>
            <div class="pl-2 py-2 pr-2" v-if="state.activeAccount.bio">
              {{ state.activeAccount.bio }}
            </div>
          </div>

          <div class="card review-scroll mb-2">
            <div class="card-title m-0 lightgrey d-flex justify-content-between">
              <span class="pl-2 py-2 ">
                REvIEW
              </span>
            </div>
            <div class="pl-2 py-2 pr-2">
              <span>
                <Review v-for="review in state.reviews" :key="review.id" :review="review" />
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

import { computed, onMounted, reactive, watch } from 'vue'
import { AppState } from '../AppState'
import { useRoute } from 'vue-router'
import { itemsService } from '../services/ItemsService'
import { reviewsService } from '../services/ReviewsService'
import { accountService } from '../services/AccountService'

export default {
  name: 'Home',
  setup() {
    const route = useRoute()
    const state = reactive({
      items: computed(() => AppState.items),
      activeAccount: computed(() => AppState.activeAccount),
      reviews: computed(() => AppState.reviews.filter(r => r.creatorId !== AppState.account.id)),
      account: computed(() => AppState.account),
      user: computed(() => AppState.user),
      loading: computed(() => AppState.loading)
    })
    watch(() => state.loading, () => {
      accountService.getActive(route.params.id)
    })
    onMounted(async() => {
      try {
        if (!state.loading) {
          await accountService.getActive(route.params.id)
        }
        await reviewsService.getReviewsByUserId(route.params.id)
        await itemsService.getItemsByUserId(route.params.id)
        // await reviewsService.getUserReviewScore(route.params.id)
        // const rating = await reviewsService.getUserReviewScore(route.params.id)
        // return rating
      } catch (error) {
        // eslint-disable-next-line no-console
        console.error(error)
      }
    })

    return {
      state,
      route,
      getUserRating() {
        // debugger
        const totalReviews = state.reviews.length
        let sumOfReviews = 0
        state.reviews.forEach(r => {
          sumOfReviews += r.rating
        })
        const userRating = sumOfReviews / totalReviews
        return Math.round(userRating)
        // return userRating
      }
    }
  }
}
</script>

<style lang="scss">
.star{
  color:yellow
}
.background-img{
  background-image: url('../assets/img/Untitled.jpg');
  background-size: cover;
}

.review-scroll{
    overflow-y: scroll;
          overflow-x: hidden;
    // overflow-:auto;
    // flex-wrap: nowrap;
    max-height: 25vw;// display: inline-block;
}
@media screen and (max-width:760px){
  .review-scroll{
    overflow-y: scroll;
    max-height: 45vw;// display: inline-block;
      overflow-x: hidden;
  }
}
.lightgrey{
  background-color: rgba(236, 236, 236, 0.315);
}
</style>
