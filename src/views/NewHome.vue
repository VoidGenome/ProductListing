<template>
    <div>
        <v-toolbar>
            <v-container>
                <v-row class="mb-n7">
                    <v-col cols="6">

                    </v-col>
                    <v-col cols="5">
                        <v-text-field density="compact" clearable="true" placeholder="Search"
                            prepend-inner-icon="mdi-magnify">

                        </v-text-field>
                    </v-col>
                </v-row>
            </v-container>
        </v-toolbar>

        <!-------------------------------------------------------------------------------------------------------->
        <!-- <v-overlay v-if="loading"></v-overlay> -->
        <v-row>
            <v-col cols="2">
                <v-chip label outlined size="x-small">CATEGORIES</v-chip>
            </v-col>
            <v-col cols="8">
                <v-radio-group v-model="category" inline density="compact">
                    <v-radio label="All" value="allCategories" ></v-radio>
                    <v-radio label="Men's clothing" value="men's clothing"></v-radio>
                    <v-radio label="Jewelry" value="jewelry"></v-radio>
                    <v-radio label="Women's Clothing" value="women's clothing"></v-radio>
                    <v-radio label="Electronics" value="electronics"></v-radio>
                </v-radio-group>
            </v-col>

        </v-row>
        <v-container>
            <v-row>
                <div class="mr-10 mb-5" v-for="(item, index) in arrayOfItems.items" :key="index">


                    <v-card rounded height="300px" width="300px">
                        <v-card-subtitle>{{ item.title }}</v-card-subtitle>
                        <v-divider />
                        <img :src="item.image" width="160px" height="160px" />
                        <span>{{ item.category }}</span>
                    </v-card>


                </div>
            </v-row>
        </v-container>


    </div>
</template>

<script setup>
import { onMounted,reactive,watch } from "vue"

// const count = ref(0)
let loading = true
let category = ["allCategories"]
let arrayOfItems = reactive({items:[]})
// functions that mutate state and trigger updates
// function increment() {
//     count.value++
    
//     console.log(count)
//     console.log(arrayOfItems.items)
// }

onMounted(() => {
    console.log(arrayOfItems, "then")
    loading = true
    // fetch("https://fakestoreapi.com/products").then(res => res.json())
    //     .then(json => arrayOfItems.items = json)
    //     .catch((err) => {
    //         if (err) {
    //             return console.log("Items cant load")
    //         }
    //     })
    console.log(arrayOfItems, "now")
    loading = false
    console.log(loading)
    // Perform actions after the component is mounted
    
});
watch(category, (newValue, oldValue) => {
    console.log('category changed:', newValue, oldValue);
});

</script>