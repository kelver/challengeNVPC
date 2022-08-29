<template>
    <h2 v-bind:class="[isFinished ? 'finish' : 'load-more']" @click='getPosts()' v-cloak>{{ buttonText }}</h2>
</template>

<script>
import axios from "axios";
export default {
    name: 'PaginationComponent',
    props: {
        page: String
    },
    methods: {
        getRepos() {
            if(this.term.length > 3) {
                axios
                    .get("https://api.github.com/users/kelver/repos")
                    .then((res) => {
                        this.user = res.data;
                    })
                    .catch((error) => {
                        console.log(error);
                    });
            }
        },
    },
}
</script>