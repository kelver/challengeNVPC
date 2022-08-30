<template>
    <div class="flex flex-col min-h-screen">
        <nav class="flex items-center justify-between flex-wrap bg-teal-500 p-6">
            <div class="items-center text-white mr-6 mb-4 w-full">
                <span class="font-semibold text-xl tracking-tight">Desafio NVPC</span>
            </div>

            <SearchBox @get-repos="getRepos" />
        </nav>

        <header class="bg-white shadow">
            <div class="max-w-7xl mx-auto py-6 px-4 sm:px-6 lg:px-8">
                <h1 class="text-3xl tracking-tight font-bold text-gray-900">Repositórios</h1>
            </div>
        </header>
        <main class="">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <!-- Replace with your content -->
                <div class="px-4 py-6 sm:px-0">
                    <div class="rounded-lg h-auto">
                        <CardRepository @get-repos="getRepos" :data="this.data" :contents="this.contents" />
                    </div>
                </div>
                <!-- /End replace -->
            </div>
        </main>
        <FooterComponent />
    </div>
</template>

<script>
// @ is an alias to /src
import CardRepository from '@/components/CardRepository'
import SearchBox from '@/components/SearchBox'
import FooterComponent from '@/components/FooterComponent'
import axios from "axios";
import {toRaw} from "vue";

export default {
    name: 'HomeView',
    data() {
        return {
            data: [],
            contents: {},
            nextPage: 2
        };
    },
    components: {
        SearchBox,
        CardRepository,
        FooterComponent,
    },
    props: {
        search: Object
    },
    methods: {
        async getRepos(search = {term:'', order:0, page:1, isSearch: false}) {
            let arrSort = {
                0: '',
                1: '&sort=full_name&direction=asc',
                2: '&sort=full_name&direction=desc',
                3: '&sort=updated&direction=desc',
                4: '&sort=updated&direction=asc',
            };

            search.isSearch = (search.term && search.term.length > 2);

            let url = 'https://api.github.com/users/kelver/repos?per_page=6&page=' + search.page +
                arrSort[search.order];

            if(search.term && search.term.length > 2){
                url = "https://api.github.com/search/repositories?per_page=6&page=" + search.page + "&q=user%3Akelver+"
                    + search.term;
            }

            await this.getData(url, search, arrSort);
        },
        async getData (url, search, arrSort){

            if((search.term || search.order) &&
                (search.term !== this.contents.term || search.order !== this.contents.order)){
                console.log('zerei')
                this.data = []
            }
            await axios
                .get(url)
                .then((res) => {
                    this.data.push((!search.isSearch) ? res.data : res.data['items']);

                    let newUrl = new URL(url);
                    var search_params = newUrl.searchParams;
                    search_params.set('page', search.page+1);
                    newUrl.search = search_params.toString()

                    this.getNextData(newUrl.toString(), search);
                })
                .catch((error) => {
                    console.log(error);
                });
        },
        async getNextData (urlTestNextPage, search) {
            await axios
                .get(urlTestNextPage)
                .then((res) => {
                    let data = (!search.isSearch) ? res.data : res.data['items']
                    this.nextPage = (data.length > 0) ? parseInt(search.page)+1 : 1;

                    this.contents = {
                        term: search.term,
                        order: search.order,
                        page: search.page,
                        nextPage: this.nextPage,
                    };
                })
                .catch((error) => {
                    console.log(error);
                });
        }
    }
}
</script>
