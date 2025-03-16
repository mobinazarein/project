<template>
    <v-form @submit.prevent="loginUser">
        <v-container fluid fill-height>
            <v-row justify="center" align="center" class="mt-10">
                <loading :loading="isLoading"></loading>
                <v-col cols="12" md="6" class="d-flex justify-center mt-10">
                    <v-card width="600" class="pa-5 mt-10">
                        <div style="text-align: center;">
                            <img src="../../../public/logo.png" alt="Logo">
                            <div class="d-flex align-center justify-center mt-10">
                                <img src="../../../public/icons/Sign.png" width="15px" height="15px" alt="Sign Icon">
                                <p class="mr-2">ورود </p>
                            </div>
                            <v-text-field variant="outlined" class="mr-10 mt-10 ml-10" required
                                v-model="pageData.email_phone" rounded="pill" label="   ایمیل شما" :disabled="true">
                            </v-text-field>
                            <v-text-field variant="outlined" class="mr-10 mt-3 ml-10" required
                                v-model="pageData.password" rounded="pill" label="    رمز عبور"
                                :type="pageData.showPassword ? 'text' : 'password'">
                                <template v-slot:append>
                                    <v-icon @click="pageData.showPassword = !pageData.showPassword"
                                        :icon="pageData.showPassword ? 'mdi-eye-off' : 'mdi-eye'" />
                                </template>
                            </v-text-field>
                            <p style="text-align: left;cursor: pointer;" class="ml-10 mt-5 mb-10"
                                @click="$router.push('/login/forget-password')">
                                رمز عبور خود را فراموش
                                کرده اید؟</p>
                            <v-btn class="nextStep" append-icon="mdi-arrow-left" @click="loginUser">مرحله بعد</v-btn>
                        </div>
                    </v-card>
                </v-col>
            </v-row>
            <div style="text-align: center" class="mt-10">
                <v-btn style="border-radius: 25px;" prepend-icon="mdi-arrow-right" @click="$router.push('/')">بازگشت به
                    سایت</v-btn>
            </div>
        </v-container>
    </v-form>
    <!-- <div>
        <h1>Login</h1>
        <form @submit.prevent="login">
            <input v-model="username" placeholder="Username" required />
            <input v-model="password" type="password" placeholder="Password" required />
            <button type="submit">Login</button>
        </form>
    </div> -->
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
import { useNuxtApp } from '#app';
import { useAuthStore } from "@/store/default.js";
import { useDefaultStore } from '@/store/default.js';
import { computed } from 'vue';
const defaultStore = useDefaultStore();
const { $api } = useNuxtApp();
const router = useRouter();
const isLoading = ref(false);
const pageData = ref({
    email: null,
    password: null,
    email_phone: '',
    showPassword: false
}).value
async function loginUser() {
    isLoading.value = true;
    const res = await $api.corporateAccess.loginUser({
        email: pageData.email_phone,
        password: pageData.password
    })
    console.log(res);
    if (res) {
        isLoading.value = false;
        localStorage.setItem('accessToken', res.access_token)
        router.push('/')
    }
};
onMounted(() => {
    if (process.browser) {
        pageData.email_phone = localStorage.getItem('email-phone');
        console.log(pageData.email_phone);
    }

})
</script>
<style scoped lang="scss">
@use '/assets/styles/variables' as *;

.nextStep {
    background-color: $darkgreen;
    color: white;
    border-radius: 25px;
    text-align: center;
    height: 45px;
    width: 190px
}
</style>