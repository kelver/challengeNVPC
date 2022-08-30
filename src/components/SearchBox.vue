<template>
    <div class="w-full flex-grow flex items-center">
        <div class="md:flex space-y-4 text-sm flex-grow">
            <label for="default-search"
                   class="mb-2 text-sm font-medium text-gray-900 sr-only dark:text-gray-300">Busca</label>
            <div class="w-full md:w-3/4">
                <input type="text" id="default-search" v-model="this.term" @keyup="search(this.term, this.order)"
                   class="block p-4 pl-10 w-full text-sm text-gray-900 rounded-lg border border-gray-300
                    focus:ring-blue-500 focus:border-blue-500" placeholder="Buscar Repositório" required>
            </div>
            <div class="w-full md:w-1/4">
                <div class="flex justify-center">
                    <div class="mb-3 mx-2 w-full">
                        <select class="form-select block w-full px-3 py-4 text-base font-normal
                          text-gray-700 bg-white bg-clip-padding bg-no-repeat border border-solid border-gray-300
                          rounded transition ease-in-out m-0 focus:text-gray-700 focus:bg-white focus:border-blue-600
                          focus:outline-none"
                            v-model="this.order" @change="search(this.term, this.order)">
                            <option value="0" selected disabled>Ordem</option>
                            <option value="1">A-Z</option>
                            <option value="2">Z-A</option>
                            <option value="3">Atualizações mais recentes</option>
                            <option value="4">Atualições mais antigas</option>
                        </select>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'SearchBox',
    data() {
        return {
            term: '',
            order: 0,
        }
    },
    emits: ["getRepos"],
    methods: {
        search (term, order) {
            this.$emit('getRepos', {term, order, page:1, isSearch: true})
        }
    }
}
</script>