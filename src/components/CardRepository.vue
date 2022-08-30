<template>
    <div class="flex items-center justify-center">
        <!-- Card -->
        <div class="">
            <div v-for="dt in data" class="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-3">
                <div v-for="repo in dt"
                     class="w-72 bg-white border rounded-lg p-5 shadow hover:border-gray-700 delay-100
                      duration-200">
                    <!-- Header -->
                    <div class="flex items-center justify-center">
                        <img :src="repo.owner.avatar_url" class="rounded w-12 h-12 rounded-full" />
                    </div>
                    <div class="flex flex-row">
                        <p class="text-center w-full">
                            <span class="text-gray-300 font-semibold">{{ repo.owner.login }}/</span>
                            <span class="text-gray-500 font-semibold">{{ repo.name }}</span>
                        </p>
                    </div>
                    <!-- Content -->
                    <p class="text-xs text-gray-500 mt-3">
                        {{ repo.description ?? 'Projeto sem descrição.' }}
                    </p>
                    <div class="mt-3">
                        <a :href="repo.html_url" target="_blank" >
                            <div class="shadow-2xl btnRepo mx-auto h-10 w-40 bg-pink-600 flex justify-center
                                items-center rounded-lg cursor-pointer relative overflow-hidden">
                                <p class="text-center text-white text-sm font-semibold my-auto z-10">Acessar Repositório</p>
                            </div>
                        </a>
                    </div>
                </div>
            </div>

        </div>
    </div>
    <div class="flex items-center justify-center w-full mt-4">
        <h2 v-bind:class="[this.isFinished ? 'bg-indigo-300' : 'bg-indigo-700']"
            @click="!this.isFinished ? this.loadMore(this.page) : null"
            class="text-indigo-100 transition-colors duration-150 rounded-lg
                   h-12 px-6 m-2 text-lg py-2 w-2/4 hover:border-2 border-indigo-300">{{ this.textButton }}</h2>
    </div>
</template>

<style>
.btnRepo::before {
    content: "";
    position: absolute;
    margin: auto;
    width: 0%;
    height: 0%;
    background: #FFF;
    transition: 0.5s ease-out;
    border-radius: 100% 100% 100% 100%;
    opacity: 0%;
}

.btnRepo:hover:before {
    width: 500px;
    height: 500px;
    transition: 0.5s ease-out;
    border-radius: 0;
    animation: opacity-in 0.7s forwards ease-out;
}

.btnRepo:hover {
    animation: bouncerino-in 0.5s forwards alternate ease-out;
}

.btnRepo:hover:p {
    transform: scale(5);
    transition: 0.7s ease-out;
}

.btnRepo {
    animation: bouncerino-out 0.5s;
}

@keyframes bouncerino-in {
    100% {
        transform: scale(1.075);
    }
    70% {
        transform: scale(1.08);
    }
    50% {
        transform: scale(1.075);
    }
    40% {
        transform: scale(1.09);
    }
}
@keyframes bouncerino-out {
    100% {
        transform: scale(1);
    }
    0% {
        transform: scale(1.075);
    }
}
@keyframes opacity-in {
    from {
        opacity: 100%;
    }
    to {
        opacity: 0%;
    }
}
</style>

<script>
export default {
    name: 'CardRepository',
    data() {
        return {
            page: 1,
            textButton: 'Ver Mais',
            isFinished: false
        }
    },
    props: {
        data: Object,
        contents: Object,
    },
    emits: ["getRepos"],
    created() {
        this.$emit('getRepos')
    },
    updated() {
        if(this.contents.nextPage <= 1){
            this.textButton = "Todos os repositórios já exibidos.";
            this.isFinished = true;
        }
    },
    methods: {
        async loadMore() {
            this.page = this.contents.nextPage;
            await this.$emit('getRepos', {
                term: this.contents.term ?? '',
                order: this.contents.order ?? 0,
                page: (this.nextPage ?? this.page),
                isSearch: false
            })
        }
    }
}
</script>