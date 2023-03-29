<template>
 
  <div id="app">
    <header>

      <title>Vue</title>
      <h1 class="text-center"> <b> Lessons Booking App </b> </h1>
      <div class="navbar navbar-dark bg-warning">
       
        <div class="navbar­header">
          <div class="btn-group" role="group" aria-label="Basic example">
  <button type="button" class="btn btn-info mr-2"  @click="deleteAllCaches"> Delete All Caches</button>
  <br>
  <button type="button" class="btn btn-info mr-2" @click="reloadPage"> Reload Page</button>
  <br>
  <button type="button" class="btn btn-info mr-2" @click="unregisterAllServiceWorkers">Unregister Workers</button>
  <a href="#" :href="url" class="link-danger"> All Lessons</a>

</div>
        </div>
        <div class="nav navbar­nav navbar ­right cart">

          <!--Here is a shopping cart button  -->

         
          <b-button variant="outline-warning" v-on:click="showCheckout" >
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 576 512"><!--! Font Awesome Pro 6.4.0 by @font
              awesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyrig
              ht 2023 Fonticons, Inc. --><path d="M0 24C0 10.7 10.7 0 24 0H69.5c22 0 41.5 12.8 50.6 32h411c26.3 0 45.5 25 38.6 50.4l-41 152.3c-8.5 31.4-37 53.3-69.5 53.3H170.7l5.4 28.5c2.2 11.3 12.1 19.5 23.6 19.5H488c13.3 0 24 10.7 24 24s-10.7 24-24 24H199.7c-34.6 0-64.3-24.6-70.7-58.5L77.4 54.5c-.7-3.8-4-6.5-7.9-6.5H24C10.7 48 0 37.3 0 24zM128 464a48 48 0 1 1 96 0 48 48 0 1 1 -96 0zm336-48a48 48 0 1 1 0 96 48 48 0 1 1 0-96z"/></svg>
         <b>Shopping Cart</b>   {{ totalItemsInBasket }}
          </b-button>


        </div>


       

      </div>
  
     
<!-- <div class="position-relative">
  <h4 class="z-index-1 position-relative">Background image with .obj-fit-cover</h4>
  <img class="bg obj-fit-cover" :style="{}" src="images/background.jfif"  alt="bg-img">
</div> -->
<!-- <div class="bg"></div> -->

  

    </header>

    <main>
      <!-- components -->
      <component :is="currentView" :sortedLesson="sortedLesson" :basket="basket" @add-Product="addProduct"
        tempBasket="tempBasket" @remove-Product="removeProduct">
        <!-- @submit-Form="submitForm" -->
      </component>
    </main>
  </div>

  
</template>
 
<script>

import ProductList from './components/Products.vue'
import Checkout from './components/Checkout.vue'


export default {
  name: "app",
  data() {
    return {
      sitename: "Lesson Shop",
      currentView: ProductList,

      lessons: [],
      tempBasket: [],
      basket: [],
      url: "https://lessonappcw2-env.eba-fh3ih8gj.eu-west-2.elasticbeanstalk.com/Products"

    }

  },
  components: { ProductList, Checkout },
  methods: {
    //method to show Checkout or ProductList 
    showCheckout() {
      if (this.currentView === ProductList) {
        this.currentView = Checkout
      } else {
        this.currentView = ProductList
      }
    },
    //fetch data from  aws backend
    displayData() {

      fetch("https://lessonappcw2-env.eba-fh3ih8gj.eu-west-2.elasticbeanstalk.com/Products")
        .then(response => response.json())
        .then(lessons => {
          this.lessons = lessons;

        });
    },

    //function to add product
    addProduct(lesson) {
      if (lesson.spaces >= 1) {
        lesson.spaces = lesson.spaces - 1;
        this.basket.push(lesson);
        if (!(this.tempBasket.includes(lesson))) {
          this.tempBasket.push(lesson);
        }
      }
    },
    //function to remove product   
    removeProduct(product) {
      if (this.basket.includes(product)) {
        let index = this.basket.indexOf(product);
        this.basket.splice(index, 1);
        for (let i = 0; i < this.lessons.length; i++) {
          if (product == this.lessons[i]) {
            this.lessons[i].spaces++;
          }
        }
      }
    },

    productLeft(lesson) {
      for (let i = 0; i < this.lessons.length; i++) {
        if (lesson == this.lessons[i]) {
          return this.lessons[i].spaces;
        }
      }
    },
    //function to delete caches
    deleteAllCaches() {
      caches.keys().then(function (names) {
        for (let name of names)
          caches.delete(name);
      });
    },
    //method to unregister service worker
    unregisterAllServiceWorkers() {
      navigator.serviceWorker.getRegistrations().then(function (registrations) {
        for (let registration of registrations) {
          registration.unregister()
        }
      });
      console.log("ServiceWorkers Unregistered");
    },
    reloadPage() {
      window.location.reload();
    },
    //function to search
      


  },

  computed: {
    totalItemsInBasket: function () {
      return this.basket.length || "";
    },



    sortedLesson() {
      function compare(a, b) {
        if (a.price > b.price) return +1;
        if (a.price < b.price) return -1;
        return 0;
      }
      return this.lessons.sort(compare);
    },




  },
  created() {

    this.displayData();

    if ("serviceWorker" in navigator) {
      navigator.serviceWorker.register("service-worker.js");
    }
  },
}
</script>
<style scoped>
.bg{
  background-image: url("images/background.jfif");
}
  
h1{
    padding-top: 0rem;
    font-size: 4rem;
    text-align: center;
    justify-content: center;
}
.navbar{
    background: white;
    padding: 1rem;
    font-size: 1.5rem;
    border-bottom: 1px solid white;
}
/* .bg {
    background-image: url("./images/background.jf");
    background-repeat: no-repeat;
             background-attachment: fixed;
             background-size: cover;
  
    /* Workaround for some mobile browsers */
    /* min-height: 100%; */
    .bg-img{
  /* The image used */
  background-image: url("images/background.jfif");

  /* Full height */
  height: 100%; 

  /* Center and scale the image nicely */
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
  </style>