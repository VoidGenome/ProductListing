<template>
    <div>
        <v-responsive fluid>
            <v-toolbar scroll-off-screen fixed class='backgroundColor'>
                <v-container>
                    <v-row class="mb-n7">
                        <v-col cols="5">

                        </v-col>
                        <v-col cols="5">
                            <v-text-field rounded class="textFieldColor" variant='solo' v-model="searchValue"
                                density="compact" :clearable="true" placeholder="Search"
                                prepend-inner-icon="mdi-magnify">

                            </v-text-field>
                        </v-col>
                        <v-col cols="1">
                            <v-tooltip>
                                <template v-slot:activator="{props}">
                                    <v-btn v-bind="props" rounded @click='updateStatus = !updateStatus'
                                        class="updateButtonColor"><v-icon
                                            :class="updateStatus?'spin-icon':''">mdi-update</v-icon></v-btn>
                                </template>
                                <span>Update Details</span>
                            </v-tooltip>
                        </v-col>
                        <v-col cols="1">
                            <v-tooltip>
                                <template v-slot:activator="{ props }">
                                    <v-btn v-bind="props" variant="flat" @click='dialogModel = true'
                                        class="addButtonColor"><v-icon>mdi-plus</v-icon></v-btn>
                                </template>
                                <span>Add items</span>
                            </v-tooltip>
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
            <span v-if='computedArrayOfItems==0 && loading ==false'>No results found </span>
            <v-container>
                <v-row>
                    <div class="mr-10 mb-5" v-for="(item, index) in computedArrayOfItems" :key="index">
                        <v-card height="300px" width="300px" class="rounded-card responsive-card">
                            <v-card-title class="titleStyle">{{ item.title }}</v-card-title>
                            <v-divider />
                            <v-tooltip>
                                <template v-slot:activator="{ props }">
                                    <img v-bind="props" :src="item.image" width="40%" height="40%" />
                                </template>
                                <v-card style="height: auto;width:300px">
                                    <v-container>
                                        <img :src="item.image" width="40%" height="40%" />
                                        <br />
                                        <span>{{ item.description }}</span>
                                    </v-container>
                                </v-card>

                            </v-tooltip>

                            <v-chip color='success'><v-icon>mdi-tag</v-icon><v-icon>mdi-currency-usd</v-icon>{{
                                item.price }}</v-chip>

                            <v-chip size="x-small"
                                :style="+item.rating.rate < 3.0 ? 'color:red' : 'background-color:yellow'"><v-icon>mdi-star-outline</v-icon><span
                                    :style="'font-color:black'">{{item.rating.rate
                                    }}</span></v-chip>
                            <div class="likeButton">
                                <v-icon color='pink'>mdi-heart</v-icon> <small>{{ item.rating.count }}</small>
                            </div>
                            <div class="floating-button" v-if='updateStatus'>
                                <!-- Your floating button content goes here -->
                                <v-tooltip color='green' top>
                                    <template v-slot:activator="{props}">
                                        <v-icon @click="updateDetails(item)" v-bind='props' class="rating-icon mr-4"
                                            color="success">mdi-pencil</v-icon>
                                    </template>
                                    <span>Update Details</span>
                                </v-tooltip>
                                <v-tooltip color='red' top>
                                    <template v-slot:activator="{ props }">
                                        <v-icon @click="deleteItem(item.id)" v-bind='props' class="rating-icon"
                                            color="error">mdi-delete</v-icon>
                                    </template>
                                    <span>Delete Item</span>
                                </v-tooltip>

                            </div>

                        </v-card>


                    </div>
                </v-row>
            </v-container>
            <AddItem :dialogModel="dialogModel" @closeDialog='closeDialog' :itemData="itemData"
                @updateLoadItemsData="updateLoadItemsData" />
            <v-footer v-if=" !loading" class="backgroundColor">

            </v-footer>
        </v-responsive>
    </div>
</template>

<script setup>
import { ref, onMounted, reactive, watchEffect, computed } from "vue"
import Swal from "sweetalert2"
import AddItem from "./AddItem.vue"

let loading = ref(true)
let dialogModel =ref(false)
// let fab = reactive(false)
let category = ref("allCategories")
let arrayOfItems = reactive({items:[]})
let searchValue = ref("")
let itemData = ref("")
let updateStatus = ref(false)
// functions that mutate state and trigger updates
function loadItems() {
    loading.value = true
    fetch(process.env.VUE_APP_STORE_API_PRODUCTS).then(res => res.json())
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
}
function updateDetails(val) {
    itemData.value = val
    dialogModel.value = true
}

function deleteItem(val) {
    console.log(val)
    Swal.fire({
        title: "Delete this item?",
        text: "You won't be able to revert this!",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Confirm",
        toast:true,
    }).then((result) => {
        if (result.isConfirmed) {

            fetch(`https://fakestoreapi.com/products/${val}`, {
                method: "DELETE"
            })
                .then(res => res.json())
                .then(json => {
                    console.log(json)
                    Swal.fire({
                        title: "Deleted!",
                        text: "The Item has been deleted.",
                        icon: "success"
                    });
                })
            loadItems()
            
        }
    });
}
function closeDialog(val) {
    dialogModel.value = val
    itemData.value = ""
}
function updateLoadItemsData(val) {
    dialogModel.value = val
}

onMounted(() => {
    console.log(arrayOfItems, "then")
    loadItems()
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
                // .filter((el) => el.category.toUpperCase().includes(searchValue.value.trim().toUpperCase()))
                .filter((el) => el.title.toUpperCase().includes(searchValue.value.trim().toUpperCase()))
                // .filter((el) => el.category.toUpperCase().includes(searchValue.value.trim().toUpperCase()))
        }
        else {
            return arrayOfItems.items.filter((el) => category.value == el.category)
                // .filter((el) => el.category.toUpperCase().includes(searchValue.value.trim().toUpperCase()))
                .filter((el) => el.title.toUpperCase().includes(searchValue.value.trim().toUpperCase()))
                // .filter((el) => el.category.toUpperCase().includes(searchValue.value.trim().toUpperCase()))
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
.rounded-card {
    border-radius: 15px;
}
.backgroundColor {
    background-image: linear-gradient(to bottom, #534a46, #5a514e, #615956, #68605e, #6f6866, #706967, #716b68, #726c69, #6d6763, #69615e, #645c58, #605753);
}
.textFieldColor{
    color: white;
    
}
.titleStyle{
    font-size: 13px;
}
.updateButtonColor{
    
    background-color: greenyellow;
}
.addButtonColor{
    background-color: green;

}
.floating-button {
    position: absolute;
    bottom: 10px;
    /* Adjust as needed */
    right: 10px;
    /* Adjust as needed */
    z-index: 1;
    /* Ensure it appears above other content */
}
.likeButton {
    position: absolute;
    bottom: 10px;
    /* Adjust as needed */
    left: 10px;
    /* Adjust as needed */
    z-index: 1;
    /* Ensure it appears above other content */
}
.rating-icon {
    transition: transform 0.2s ease;
    /* Add transition for smooth effect */
}

.rating-icon:hover {
    transform: scale(1.2);
    /* Increase size on hover */
}
.spin-icon {
    animation: spin 2s infinite linear;
}

@keyframes spin {
    from {
        transform: rotate(0deg);
    }

    to {
        transform: rotate(360deg);
    }
}
</style>