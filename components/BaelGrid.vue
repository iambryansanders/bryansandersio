<template>
  <div>
    <div v-if="items2[0]">
      <div v-for="(p) in items2" :key="p.pi">
        <nuxt-link class="xs-text-center xs-flex xs-full-height xs-flex-align-center xs-flex-justify-center xs-text-center" :to="p._path">
           <article class="post-card post tag-getting-started post-card-large">
        <a class="post-card-image-link" href="/welcome/" id="owdoWftAChWtgtxrQUywWPfWl">
          <img
            class="post-card-image"
            :src="p.thumbnail"
          >
        </a>

        <div class="post-card-content" id="ZksstvPOemlrhVfoNWzSihUWR">
          <a class="post-card-content-link" href="/welcome/" id="aWiSBIAqAdacNpVVmeFMZTXal">
            <header class="post-card-header" id="ZgaqSWitVuFGdTiDUFZVbxHpb">
              <span class="post-card-tags" id="VXTnAYktjzaAVHmseYVGgXIXI">{{p.date}}</span>
              <h2 class="post-card-title" id="jCXkbuzoJAOiNGIbztdVZuxxm">{{p.title}}</h2>
            </header>

            <section class="post-card-excerpt" id="jxZjyctpHRxRTuCSCvvzLpxGa">
             
            </section>
          </a>

   
        </div>
      </article>
        </nuxt-link>
      </div>

     
    </div>

    <div v-else class="r full-height browse">
      <div
        class="xs-p2 c-100 xs-flex xs-flex-align-center xs-flex-justify-center xs-text-center"
        :style="`height:calc(100vh - ${navbarheight}px);margin-top:${navbarheight}px`"
      >
        <div v-if="total < 1 && !busy">No Results.</div>
      </div>
    </div>
  </div>
</template>




<script>
export default {
  props: ["items", "allitems"],
  data() {
    return {
      currentPage: null,
      pageNumbers: [],
      pageNumberCount: 0,
      items2: [],
      query: 1,
      busy: false,
      count: 0
    };
  },
  methods: {
    pageCheck() {
      if (this.allitems.length > 12) {
        this.$store.commit("paginateOn", true);
        this.$store.commit("resultsLength", this.allitems.length);
      } else if (this.allitems.length < 12) {
        this.$store.commit("paginateOff", false);
      } else {
        this.$store.commit("paginateOff", false);
      }
    },

    loadMore() {
      this.count = this.offset;
      if (this.total > this.count && this.busy == false) {
        this.busy = true;

        this.items2.splice(0);
        for (var i = 0, j = 12; i < j; i++) {
          let api = this.allitems[this.count];

          this.items2.push(api);
          this.count++;
        }

        this.busy = false;
      }
    },

    onResize(event) {
      this.navHeight();
    },

    navHeight() {
      if (process.browser) {
        var height = document.getElementById("navbar").clientHeight;

        this.$store.commit("SET_NAVHEIGHT", height - 1);
      }
    }
  },
  watch: {
    // whenever question changes, this function will run
    $route({ params, query }) {
      if (this.$route.query.page > 1) {
        this.loadMore();
        this.navHeight();
        this.pageCheck();
      } else if (this.$route.query.page == null) {
        this.$route.query.page = 1;
        this.loadMore();
        this.navHeight();
        this.pageCheck();
      } else {
        this.loadMore();
        this.navHeight();
        this.pageCheck();
      }
    },
    queryParam: function() {
      this.loadMore();
    }
  },
  computed: {
    offset() {
      if (this.queryParam > 1) {
        return Number(this.queryParam - 1) * 12;
      } else {
        return 0;
      }
    },
    prevpage() {
      var prev = Number(this.queryParam) - 1;
      return prev;
    },
    nextpage() {
      var next = Number(this.queryParam) + 1;
      return next;
    },
    navbarheight() {
      return this.$store.state.navheight;
    },
    total() {
      return this.allitems.length;
    },

    queryParam() {
      if (this.$route.query.page == null) {
        return 1;
      } else {
        return Number(this.$route.query.page);
      }
    }
  },

  updated() {
    this.$nextTick(() => {
      this.pageCheck();
      this.navHeight();
      this.$store.commit("SET_GRIDOFFSET", this.offset);
    });
  },
  mounted() {
    if (process.browser) {
      this.loadMore();

      this.$nextTick(() => {
        this.navHeight();
        this.pageCheck();
        window.addEventListener("resize", this.onResize);
      });
    }
  },
  beforeDestroy() {
    // Unregister the event listener before destroying this Vue instance
    window.removeEventListener("resize", this.onResize);
  }
};
</script>

<style>
</style>
