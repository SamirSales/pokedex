<template>
    <div>
        <v-simple-table dense>
            <template v-slot:default>
                <thead>
                    <tr>
                        <th class="text-left">{{ $t('number') }}</th>
                        <th class="text-left">{{ $t('image') }}</th>
                        <th class="text-left">{{ $t('name') }}</th>
                        <th class="text-left">{{ $t('type') }}</th>
                    </tr>
                </thead>
                <tbody>
                    <tr
                        class="pokemon-table-row"
                        v-for="pokemon in pokemons"
                        :key="pokemon.id"
                        @click="onClickRow(pokemon)"
                    >
                        <td>#{{ pokemon.id }}</td>
                        <td>
                            <img :src="pokemon.imageURL" height="96" width="96" />
                        </td>
                        <td>{{ pokemon.name }}</td>
                        <td>
                            <pokemon-type-chip
                                v-for="type in pokemon.types"
                                :type="type.type.name"
                                :key="type.type.name + pokemon.id"
                            ></pokemon-type-chip>
                        </td>
                    </tr>
                </tbody>
            </template>
        </v-simple-table>

        <div class="text-center">
            <v-pagination
                :value="pageNumber"
                :length="numberOfPages"
                :disabled="isLoading"
                color="red"
                circle
                @input="onChangePageNumber(arguments[0])"
            ></v-pagination>
        </div>

        <v-progress-linear v-show="isLoading" indeterminate color="teal"></v-progress-linear>
    </div>
</template>

<script>
import { PokemonTableDataHandler } from '@/store/modules/pokemonTable';
import PokemonStoreHttpRequest from '@/facade/PokemonStoreHttpRequest';
import Configuration from '@/Configuration';

import PokemonTypeChip from './PokemonTypeChip.vue';

export default {
    components: {
        'pokemon-type-chip': PokemonTypeChip
    },

    computed: {
        numberOfPages() {
            return Math.ceil(Configuration.MAX_NUMBER_OF_POKEMONS / this.pageSize);
        },

        pokemons() {
            return PokemonTableDataHandler.getItems(this);
        },

        pageNumber() {
            return PokemonTableDataHandler.getPageNumber(this);
        },

        pageSize() {
            return PokemonTableDataHandler.getPageSize(this);
        },

        isLoading() {
            return PokemonTableDataHandler.isLoading(this);
        }
    },

    created() {
        PokemonStoreHttpRequest.refreshData(this);
    },

    methods: {
        onChangePageNumber(pageNumber) {
            PokemonTableDataHandler.setPageNumber(this, pageNumber);
            PokemonStoreHttpRequest.refreshData(this);
        },

        onClickRow(pokemon) {
            PokemonTableDataHandler.setSelectedPokemon(this, pokemon);
            this.$emit('clickRow', pokemon);
        }
    }
};
</script>

<style scoped>
.pokemon-table-row {
    cursor: pointer;
}

.pokemon-table-row td {
    width: 25%;
}
</style>
