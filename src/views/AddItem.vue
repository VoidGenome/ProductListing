<template>
    <div>
        <v-dialog v-model='dialog' class="dialogStyle">
            <v-card class='dialogStyle'>
                <v-card-title>{{itemData?'Update Data':'Add Item' }}</v-card-title>
                <v-container>
                    <v-text-field v-model="objToSend.title" density="compact" label="Item's Title"></v-text-field>
                    <v-text-field v-model="objToSend.price" density="compact" label="Item's Price"></v-text-field>
                    <v-select v-model="objToSend.category" :items="items" label="Item's Category"></v-select>
                    <v-textarea v-model="objToSend.description" density="compact" variant="outlined"
                        label="Item's Description"></v-textarea>
                    <v-text-field v-model="objToSend.image" label="Item's Image"
                        hint="Kindly put the link of the desired Image"></v-text-field>
                    <v-row>
                        <v-col cols="6">
                            <v-btn block color="success" @click="itemData?updateItem():submitItem()">
                                {{itemData?"Update":"Submit"}}
                            </v-btn>
                        </v-col>
                        <v-col cols="6">
                            <v-btn @click="closeDialog()" block color="error">
                                Cancel
                            </v-btn>
                        </v-col>
                    </v-row>
                </v-container>

            </v-card>
        </v-dialog>
    </div>
</template>

<script setup >

import { ref, defineProps, computed, defineEmits, watch } from 'vue';
import Swal from "sweetalert2"

// Define props
const props = defineProps({
    dialogModel: Boolean,
    itemData:Object
});
let items = ref(["men's clothing","electronics","women's clothing", "jewelery"])
const emit = defineEmits(["closeDialog","updateLoadItemsData"]);

let objToSend = ref({})

// ✅ do this: access props using `props.name`
const dialog = computed(() => {
    return props.dialogModel
})

let itemData = computed(() => {
    return props.itemData
})

// function fillData() {
    
// }
watch(itemData, () => {
    if (itemData.value) {
        objToSend = itemData

    }
    else return
})
function closeDialog() {
        emit("closeDialog",false)
}
function updateLoadItemsData() {
    emit("updateLoadItemsData",false)
}
function submitItem() {
    console.log("Adding Item")
    let obj = {
        title: objToSend.value.name,
        price: objToSend.value.price,
        description: objToSend.value.description,
        image: objToSend.value.image,
        rating: {
            rate: 0,
            count:0
        }
    }
    fetch('https://fakestoreapi.com/products', {
        method: "POST",
        body: JSON.stringify(
            // {
            //     title: 'test product',
            //     price: 13.5,
            //     description: 'lorem ipsum set',
            //     image: 'https://i.pravatar.cc',
            //     category: 'electronic'
            // }
            obj
        )
    })
        .then(res => res.json())
        .then(json => {

            console.log(json)
            Swal.fire({
                icon: "success",
                title: "Your item has been saved",
                showConfirmButton: false,
                timer: 1500
            });
            updateLoadItemsData()

        })
    console.log(obj)
}
function updateItem() {
    console.log("Updating")
    let obj = {
        title: objToSend.value.name,
        price: objToSend.value.price,
        description: objToSend.value.description,
        category:objToSend.value.category,
        image: objToSend.value.image,
        rating: {
            rate: objToSend.value.rating.rate,
            count: objToSend.value.rating.count
        }
    }
    let idtoUpdate = objToSend.value.id
    console.log(idtoUpdate)
    fetch(`https://fakestoreapi.com/products/${idtoUpdate}`, {
        method: "PUT",
        body: JSON.stringify(
            {
                title: obj.title,
                price: obj.price,
                description: obj.description,
                image: obj.image,
                category: obj.category
            }
        )
    })
        .then(res => res.json())
        .then(json => {

            console.log(json)
            Swal.fire({
                icon: "success",
                title: "Your item has been Updated",
                showConfirmButton: false,
                timer: 1500
            });
            updateLoadItemsData()
        })
    console.log(obj)
}

</script>
<!-- <script>
export default {
    props:{
        dialogModel: Boolean
    },
    data: () => ({
    
    }),
   
}
</script> -->

<style>
.dialogStyle{
height:auto;
width:1000px;
}
</style>