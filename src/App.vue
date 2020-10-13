<template>
	<div class="app">
		<h1>Unsplash Image Gallery!</h1>
		<SearchBar v-model="query"></SearchBar>
		<ImageGrid :images="images" :loading="loading"></ImageGrid>
	</div>
</template>

<script>
	import SearchBar from "./components/SearchBar";
  import ImageGrid from "./components/ImageGrid";


const ACCESS_KEY = "2J5p-V98wBnLNnbi0P5U_noLosW515GU8qoS7pvQd-Q";

function debounceTime(timeInMilisecond, cb) {
  let timeOutId;

  return () => {
    timeOutId && clearTimeout(timeOutId);
    timeOutId = setTimeout(() => {
      cb();
    }, timeInMilisecond);
  };
}

export default {
  name: "App",
  components: {
    SearchBar,
    ImageGrid
  },
  data() {
    return {
      page: 1,
      images: [],
      loading: false,
      query: ''
    };
  },
  methods: {
    searchByQuery: async function() {
      this.page = 1;
      await this.getImages();
    },
    async onScroll() {
      if (
        window.innerHeight + window.scrollY + 100 >=
          document.body.offsetHeight &&
        !this.loading
      ) {
        ++this.page;
        await this.getImages(() => --this.page);
      }
    },
    async getImages(errCb) {
      let apiUrl = "https://api.unsplash.com/photos?";
      if (this.query?.trim())
        apiUrl = `https://api.unsplash.com/search/photos?query=${this.query}`;
      apiUrl += `&page=${this.page}`;
      apiUrl += `&client_id=${ACCESS_KEY}`;

      this.loading = true;
      try {
        const imagesFromApi = await globalThis
          .fetch(apiUrl)
          .then(res => res.json());
        const images = imagesFromApi.results ?? imagesFromApi;
        if (this.page === 1) this.images = images;
        else this.images = this.images.concat(images);

      } catch (e) {
        errCb && errCb();
        console.log(e);
      } finally {
        this.loading = false;
      }
    }
  },
  watch: {
    query(val, old) {
      if (val !== old && val?.trim()) {
        this.searchByQuery();
      }
    }
  },
  async created() {
    globalThis.addEventListener("scroll", this.onScroll);
    await this.getImages();
    this.searchByQuery = debounceTime(500, this.searchByQuery.bind(this));
  },
  unmounted() {
    globalTHis.removeEventListener("scroll", this.onScroll);
  }
};
</script>

<style>
	@import url("https://fonts.googleapis.com/css2?family=Comfortaa:wght@600&display=swap");

	* {
		box-sizing: border-box;
	}

	body {
		margin: 0;
		padding: 0;
		font-family: "Comfortaa", Arial, Helvetica, sans-serif;
	}

	.app {
		text-align: center;
		min-height: 100vh;
		background: #dcdcdc;
		padding: 100px 50px;
	}

	.error {
		display: inline-block;
		text-align: center;
		margin: 30px;
		background: rgb(221, 85, 70);
		color: rgb(247, 232, 230);
		padding: 20px 30px;
		border-radius: 5px;
		text-decoration: none;
		transition: 0.3s ease all;
	}

	.error:hover {
		background: rgb(235, 95, 79);
		box-shadow: 3px 3px 0 rgb(141, 38, 26);
	}

	h1 {
		margin-top: 0;
		margin-bottom: 20px;
		color: #bbb;
	}

	form {
		margin-bottom: 50px;
	}

	input[type="text"] {
		min-width: 400px;
		border-radius: 50px;
		padding: 15px 20px;
		border: 1px solid #fff;
		outline: none;
		margin-right: 12px;
		font-size: 18px;
		transition: 0.3s ease border-color;
	}

	input[type="text"]:active,
	input[type="text"]:focus {
		border-color: #999;
	}

	button {
		padding: 15px 20px;
		border-radius: 50px;
		border: none;
		background: #222;
		color: #bbb;
		font-size: 18px;
		cursor: pointer;
		transition: 0.3s ease all;
	}

	button:hover {
		background: #111;
		color: #eee;
	}

	.image-grid {
		display: grid;
		grid-gap: 15px;
		grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
		grid-auto-rows: minmax(50px, 200px);
	}

	.image {
		background: #fff;
		padding: 3px;
		border-radius: 3px;
		box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
			0 2px 4px -1px rgba(0, 0, 0, 0.06);
		transition: 0.3s cubic-bezier(0.68, -0.55, 0.265, 1.55) transform;
	}

	.image:hover {
		transform: translateY(-2px);
	}

	.image img {
		display: flex;
		width: 100%;
		height: 100%;
		object-fit: cover;
	}

	.image:nth-child(3n) {
		grid-column-end: span 2;
	}
</style>