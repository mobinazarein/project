<template>
    <v-card>
        <v-row no-gutters class="ma-2">
            <v-col cols="12" md="12" class="d-flex align-center justify-space-between">
                <div class="d-flex align-center justify-center">
                    <img src="../../public/icons/Sign.png" width="15px" height="15px">
                    <p class="mr-1 " style="font-weight: bold;"> پروفایل</p>
                </div>
                <div>
                    <v-btn prepend-icon="mdi-key" style="color: green;" text class="v-custom-btn ml-2 mb-2"
                        @click="pageData.changePassDialog = true">
                        تغییر رمز عبور
                    </v-btn>
                </div>
            </v-col>
        </v-row>
        <v-row no-gutters>
            <v-col cols="12" md="6" class="text-center" style="margin-right: auto; margin-left: auto;">
                <div class="d-md-flex">
                    <v-text-field label="نام" rounded="pill" variant="outlined" class="ma-2"></v-text-field>
                    <v-text-field label="نام خانوادگی" rounded="pill" variant="outlined" class="ma-2"></v-text-field>
                </div>
                <div class="d-md-flex">
                    <v-text-field label="شماره همراه" rounded="pill" variant="outlined" class="ma-2"></v-text-field>
                    <v-text-field label="ایمیل" rounded="pill" variant="outlined" class="ma-2"></v-text-field>
                </div>

            </v-col>
        </v-row>
        <v-row no-gutters class="ma-2">
            <v-col cols="12" md="12" class="d-flex align-center justify-space-between">
                <div class="d-flex align-center justify-center">
                    <img src="../../public/icons/Sign.png" width="15px" height="15px">
                    <p class="mr-1 " style="font-weight: bold;"> کاربران</p>
                </div>
                <div>
                    <v-btn prepend-icon="mdi-plus-outline" style="color: green;" text class="v-custom-btn ml-2 mb-2"
                        to="/profile/new-user">
                        افزودن کاربر جدید
                    </v-btn>
                </div>
            </v-col>
        </v-row>
        <v-row no-gutters>
            <v-col cols="12" md="12" v-if="!isMobile">
                <div class="ma-2" style="border: 1px solid lightgray; border-radius: 10px;">
                    <v-skeleton-loader v-if="isLoading" type="table"></v-skeleton-loader>
                    <v-data-table v-show="!isLoading" :headers="pageData.userHeader" :items="pageData.userItem"
                        class="custom-table">
                        <template v-slot:item.operation="{ item, index }">
                            <v-icon class="ml-2"
                                @click="$router.push({ path: '/profile/new-user', query: { id: item.id } })">mdi-pencil-outline</v-icon>
                            <v-icon @click="openDeleteDialog(item.id, 'user')">mdi-delete-outline</v-icon>
                        </template>
                        <template v-slot:item.status="{ item }">
                            <div :style="getStatus(item.title)">
                                {{ item.title }}
                            </div>
                        </template>
                    </v-data-table>
                </div>
            </v-col>
            <v-col v-else cols="12">
                <v-row>
                    <v-col v-for="(item, index) in pageData.userItem" :key="index" cols="12" md="6" lg="4">
                        <loading :loading="isLoading" />
                        <v-card class="ma-2" color="#F7F7F7B2">
                            <div class="d-flex justify-space-between align-center">
                                <div class="d-flex align-center mr-2">
                                    <span
                                        style="border-radius: 50%; background-color: #3A3A3A12; width: 30px; height: 30px; text-align: center; line-height: 30px; margin-right: 8px;">
                                        {{ index + 1 }}
                                    </span>
                                    <v-card-title class="text-center">{{ item.first_name }}</v-card-title>
                                </div>
                                <div class="ml-2">{{ item.mobile }}</div>
                            </div>
                            <!-- {{ item.name }} -->
                            <v-card-text style="font-weight: bold;"> ایمیل : {{ item.email }}
                            </v-card-text>
                            <div class="d-flex justify-space-between">
                                <v-card-text class="d-flex">
                                    <p style="font-weight: bold;">سطح دسترسی: </p>
                                    <p class="access mr-2 ">
                                        {{ item.title }}
                                    </p>
                                </v-card-text>
                                <v-card-actions>
                                    <v-btn icon>
                                        <v-icon>mdi-pencil-outline</v-icon>
                                    </v-btn>
                                    <v-btn icon>
                                        <v-icon>mdi-delete-outline</v-icon>
                                    </v-btn>
                                </v-card-actions>
                            </div>
                        </v-card>
                    </v-col>
                </v-row>
            </v-col>
        </v-row>
        <v-dialog v-model="pageData.changePassDialog" width="500">
            <v-card>
                <v-row no-gutters class="ma-2">
                    <v-col cols="12" md="12" class="d-flex align-center justify-space-between ma-2">
                        <div class="d-flex align-center justify-center">
                            <img src="../../public/icons/Sign.png" width="15px" height="15px">
                            <p class="mr-1 " style="font-weight: bold;"> تغییر رمز عبور</p>
                        </div>
                        <v-btn icon @click="pageData.changePassDialog = false" class="ma-3">
                            <v-icon>mdi-close</v-icon>
                        </v-btn>
                    </v-col>
                </v-row>
                <v-row no-gutters justify="center">
                    <v-col cols="12" md="9">
                        <v-text-field variant="outlined" rounded="pill" label="رمز عبور فعلی"
                            v-model="pageData.current_password" class="ml-2 mr-2"></v-text-field>
                        <v-text-field variant="outlined" rounded="pill" label="رمز عبور جدید"
                            v-model="pageData.password" class="ml-2 mr-2"></v-text-field>
                        <v-text-field variant="outlined" rounded="pill" label="تکرار رمز عبور جدید"
                            v-model="pageData.password_confirmation" class="ml-2 mr-2"></v-text-field>
                        <div class="d-flex align-center justify-center">
                            <v-btn class="cancelBtn ma-3" @click="pageData.changePassDialog = false">انصراف</v-btn>
                            <v-btn class="acceptBtn" @click="changePassword">تایید</v-btn>
                        </div>
                    </v-col>
                </v-row>
            </v-card>
        </v-dialog>
    </v-card>
    <DeleteItems :model-value="isDialogOpen" @update:model-value="isDialogOpen = $event" @confirm="confirmDelete" />

</template>
<script setup>
definePageMeta({
    layout: 'right-menue-and-header'
})
import { ref, onMounted } from 'vue';
import { useAsyncData, useNuxtApp } from '#app';
import { useDefaultStore } from '@/store/default';
import { storeToRefs } from 'pinia';
import WarehouseDataTable from '~/components/WarehouseDataTable.vue';
const store = useDefaultStore();
const { $api } = useNuxtApp();
const actionType = ref('');
const isDialogOpen = ref(false);
const selectedItemId = ref(null);
const isMobile = ref(false);
const isLoading = ref(true);
const checkMobile = () => {
    isMobile.value = window.innerWidth < 768;
};
const pageData = ref({
    items: [],
    userHeader: [
        { title: 'نام', value: "first_name", align: 'center', sortable: true, },
        { title: 'نام خانوادگی', value: "last_name", align: 'center', sortable: true, },
        { title: 'شماره همراه', value: "mobile", align: 'center', sortable: true, },
        { title: 'ایمیل', value: "email", align: 'center', sortable: true, },
        { title: 'سطح دسترسی', value: "title", align: 'center', sortable: true, },
        { title: 'عملیات', value: "operation", align: 'center', sortable: true, },
    ],
    changePassDialog: false,
    current_password: null,
    password: null,
    password_confirmation: null,
}).value
async function changePassword() {
    const res = await $api.corporateAccess.changePassword({
        current_password: pageData.current_password,
        password: pageData.password,
        password_confirmation: pageData.password_confirmation
    })
    console.log(res);

    if (res.data) {
        console.log(res.data);
        pageData.changePassDialog = false,
            pageData.current_password = '',
            pageData.password = '',
            pageData.password_confirmation = ''
    } else {
        console.error('erroe', res),
            pageData.changePassDialog = false,
            pageData.current_password = '',
            pageData.password = '',
            pageData.password_confirmation = ''
    }
}
// async function fetchUsers() {
//     const res = await $api.users.fetchUsers({});
//     isLoading.value = false;
//     if (res.data) {
//         pageData.userItem = res.data.map(item => ({
//             ...item,
//             title: item.roles && item.roles.length > 0 ? item.roles[0].title : '',

//         }));
//         console.log(res);

//     } else {
//         console.error('no data', res);
//     }
// }
watch(isDialogOpen, (newValue) => { console.log('Dialog open state:', newValue); });
async function deleteDepot(id) {
    isDialogOpen.value = false;
    const res = await $api.depot.deleteDepot(id)
    console.log(res);
    await fetchDepot();

}
async function deleteUser(id) {
    isDialogOpen.value = false;
    const res = await $api.users.deleteUser(id)
    console.log(res);
    await fetchUsers();

}
function openDeleteDialog(id, type) {
    isDialogOpen.value = true;
    selectedItemId.value = id
    console.log(type);
    actionType.value = type;

}
function confirmDelete() {
    if (actionType.value === 'depot') {
        deleteDepot(selectedItemId.value);
    } else {
        deleteUser(selectedItemId.value);
    }
    isDialogOpen.value = false;
}
onMounted(() => {
    checkMobile();
    window.addEventListener('resize', checkMobile);
});
async function fetchUsers() {
    const res = await $api.users.fetchUsers({});
    isLoading.value = false;
    if (res.data) {
        pageData.userItem = res.data.map(item => ({
            ...item,
            title: item.roles && item.roles.length > 0 ? item.roles[0].title : '',

        }));
        console.log(res);

    } else {
        console.error('no data', res);
    }
}
onMounted(async () => {
    await fetchUsers();

})
</script>
<style scoped lang="scss">
@use '/assets/styles/variables' as *;

.loading {
    pointer-events: none;
}

.access {
    font-weight: bold;
    border-radius: 25px;
    background-color: $lightgreen;
    color: $darkgreen;
    width: auto;
    text-align: center;
}

.cancelBtn {
    border: 2px solid $darkborder;
    border-radius: 25px;
    height: 50px;
}

.acceptBtn {
    background-color: $darkgreen;
    border-radius: 25px;
    height: 50px;
    width: 100px;
    color: white
}
</style>