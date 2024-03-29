<template>
    <div>
        <v-responsive>
            <v-toolbar>
                <v-container>
                    <v-row class="mb-n7">
                        <v-col cols="6">

                        </v-col>
                        <v-col cols="5">
                            <v-text-field v-model="searchValue" density="compact" :clearable="true" placeholder="Search"
                                prepend-inner-icon="mdi-magnify">

                            </v-text-field>
                        </v-col>
                    </v-row>
                </v-container>
            </v-toolbar>

            <!-------------------------------------------------------------------------------------------------------->
            <v-overlay v-model="loading">
            </v-overlay>
            <v-progress-circular v-if="loading" indeterminate="true" />
            <v-row v-if="!loading">
                <v-col cols="1">
                    <v-chip label outlined size="x-small">CATEGORIES</v-chip>
                </v-col>
                <v-col cols="8">
                    <v-radio-group class='small-radio' v-model="category" inline density="compact">
                        <v-radio label="All" value="allCategories"></v-radio>
                        <v-radio label="Men's clothing" value="men's clothing"></v-radio>
                        <v-radio label="Jewelry" value="jewelery"></v-radio>
                        <v-radio label="Women's Clothing" value="women's clothing"></v-radio>
                        <v-radio label="Electronics" value="electronics"></v-radio>
                    </v-radio-group>
                </v-col>

            </v-row>
            <v-container>
                <v-row>
                    <div class="mr-10 mb-5" v-for="(item, index) in computedArrayOfItems" :key="index">


                        <v-card rounded height="300px" width="300px">
                            <v-card-title v-bind="title">{{ item.title }}</v-card-title>  
                            <v-divider />
                            <v-tooltip>
                                <template v-slot:activator="{ image }">
                                    <img v-bind="image" :src="item.image" width="40%" height="40%" />
                                </template>
                                <v-card height="500px" width="500px">
                                    <v-container>
                                        <img :src="item.image" width="80%" height="80%" />
                                    </v-container>
                                </v-card>
                            </v-tooltip>

                        </v-card>


                    </div>
                </v-row>
            </v-container>

        </v-responsive>
    </div>
</template>

<script setup>
import { ref,onMounted,reactive,watchEffect,computed } from "vue"

// const count = ref(0)
let loading = ref(true)
let category = ref("allCategories")
let arrayOfItems = reactive({items:[]})
let searchValue = ref("")
// functions that mutate state and trigger updates


onMounted(() => {
    console.log(arrayOfItems, "then")
    loading.value = true
    fetch("https://fakestoreapi.com/products").then(res => res.json())
        .then(json => {
            arrayOfItems.items = json
            loading.value = false
        }
        )
        .catch((err) => {
            if (err) {
                return console.log("Items cant load")
            }
        })
    console.log(arrayOfItems, "now")

    
    console.log(loading)
    // Perform actions after the component is mounted
    
});
watchEffect(() => {
    console.log('category changed:',category);
});
let computedArrayOfItems = computed(() => {
    if (!searchValue.value) {
        if (category.value == 'allCategories') {
            return arrayOfItems.items
        }
        else {
            return arrayOfItems.items.filter((el) => category.value == el.category)
        }
    }
    else {
        if (category.value == 'allCategories') {
            return arrayOfItems.items
                .filter((el) => el.category.includes(searchValue.value))
                .filter((el) => el.title.includes(searchValue.value))
                .filter((el) => el.category.includes(searchValue.value))
        }
        else {
            return arrayOfItems.items.filter((el) => category.value == el.category)
                .filter((el) => el.category.includes(searchValue.value))
                .filter((el) => el.title.includes(searchValue.value))
                .filter((el) => el.category.includes(searchValue.value))
        }
    }
})

</script>

<style scoped>
/* Make the radio button smaller */
.small-radio >>> i {
    font-size: 15px;
}

.small-radio >>> label {
    font-size: 11px;
    padding-left: 0px;
    margin-left: -4px;
}

.small-radio >>> .v-radio {
    padding: 0px;
    size:12px;
}

.small-radio [class*="__ripple"] {
    left: 0;
}
</style>