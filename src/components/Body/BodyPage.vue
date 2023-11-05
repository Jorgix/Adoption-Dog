<template>
    <v-layout class="rounded rounded-md">
        <v-app-bar color="primary" scroll-behavior="elevate">
            <v-row>
                <v-img :src="logoUrl" max-width="150" alt="Logomarca" class="custom-logo"></v-img>
                <v-btn stacked class="ml-auto custom-login">
                    <v-icon icon="mdi-login"></v-icon>
                    Acessar
                </v-btn>
            </v-row>
        </v-app-bar>

        <v-card>
            <v-navigation-drawer>
                <v-list density="compact" nav>
                    <v-list-item prepend-icon="mdi-view-dashboard" title="Início" value="home"></v-list-item>
                    <v-list-item prepend-icon="mdi mdi-dog" title="Adote" value="about"></v-list-item>
                </v-list>
            </v-navigation-drawer>
        </v-card>

        <v-main class="d-flex align-center justify-center" style="min-height: 300px">
            <v-container class="grey-lighten-3">
                <v-row>
                    <v-col v-for="(dog, index) in paginatedDogs" :key="index" cols="12" class="my-5" sm="4">
                        <v-card class="mx-auto" max-width="344">
                            <v-img :src="dog.image" height="250px" cover></v-img>

                            <v-card-title>
                                {{ dog.name }}
                            </v-card-title>

                            <v-card-actions>
                                <v-btn color="blue-lighten-2" variant="text">
                                    <v-icon icon="mdi mdi-dog"></v-icon> Faça uma adoção
                                </v-btn>

                                <v-spacer></v-spacer>

                                <v-btn :icon="show ? 'mdi-chevron-up' : 'mdi-chevron-down'" @click="show = !show"></v-btn>
                            </v-card-actions>

                            <v-expand-transition>
                                <div v-show="show">
                                    <v-divider></v-divider>
                                    <v-card-text class="font-weight-medium custom-padding">
                                        Temperamento
                                    </v-card-text>
                                    <v-card-text>
                                        {{ dog.temperament }}
                                    </v-card-text>
                                </div>
                            </v-expand-transition>
                        </v-card>
                    </v-col>
                </v-row>
                <div class="text-center">
                    <v-container>
                        <v-row justify="center">
                            <v-col cols="8">
                                <v-container class="max-width">
                                    <v-pagination v-model="page" class="my-4" :length="Math.ceil(totalItems / itemsPerPage)"
                                        @input="paginateDogs"></v-pagination>
                                </v-container>
                            </v-col>
                        </v-row>
                    </v-container>
                </div>
            </v-container>
        </v-main>
    </v-layout>
</template>
  
<script>
export default {
    name: 'BodyPage',
    data() {
        return {
            logoUrl: 'http://localhost:3000/src/assets/logo.png',
            dogs: [],
            totalItems: 0,
            itemsPerPage: 10,
            page: 1,
            show: true,
        };
    },
    computed: {
        paginatedDogs() {
            const startIndex = (this.page - 1) * this.itemsPerPage;
            const endIndex = startIndex + this.itemsPerPage;
            return this.dogs.slice(startIndex, endIndex);
        },
    },
    methods: {
        async fetchData() {
            const apiKey = 'live_CJWPgE5zdcdT6n18ofOz7stoJsv1tRPRL0hhmlRk8OABY0Inb1OyzZobFtsuGOHA';
            const apiUrl = 'https://api.thedogapi.com/v1/breeds';

            try {
                const response = await fetch(apiUrl, {
                    headers: {
                        'x-api-key': apiKey,
                    },
                });

                const data = await response.json();

                console.log(data);

                this.dogs = data.map((breed) => ({
                    name: breed.name,
                    image: breed.image.url,
                    temperament: breed.temperament,
                }));
                this.totalItems = this.dogs.length;
                this.paginateDogs();
            } catch (error) {
                console.error('Erro ao recuperar os dados dos cães:', error);
            }
        },
        paginateDogs() {
        },
    },
    watch: {
        page: 'paginateDogs',
    },
    created() {
        this.fetchData();
    },
};
</script>
  
<style lang="scss">
.v-responsive.v-img.custom-logo {
    margin-left: 25px;
}

.custom-login {
    margin-right: 10px;
}

.v-card-text.font-weight-medium.custom-padding {
    padding: 10px 15px 0px 15px;
}
</style>