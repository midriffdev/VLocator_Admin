<script setup lang="ts">
import Logo from '@/layouts/full/logo/Logo.vue';
/* Login form */
import LoginForm from '@/components/auth/LoginForm.vue';
</script>

<template>
    <div class="pa-3">
        <v-row class="h-100vh mh-100 auth">
            <v-col cols="12" lg="7" xl="8"
                class="d-lg-flex align-center justify-center authentication position-relative">
                <div class="auth-header pt-lg-6 pt-2 px-sm-6 px-3 pb-lg-6 pb-0">
                    <div class="position-relative">
                        <img src="@/assets/images/vlocator/logo-light.png" alt="Logo" width="" height="" class="logoStyleHeader"/>
                    </div>
                </div>
                <div class="login-page_img">
                    <img src="@/assets/images/vlocator/login-form-img.png" class="position-relative d-none d-lg-flex"
                        alt="login-background" />
                </div>
            </v-col>
            
            <v-col cols="12" lg="5" xl="4" class="d-flex align-center scrollSignUp justify-center">
                <div class="mt-xl-0 mt-5 widthSizeRegister">
                    <h2 class="text-h3 font-weight-bold mb-2">Welcome to <span class="text-primary">V-Locator</span></h2>
                    <div class="text-subtitle-1 mb-6">Your Admin Dashboard</div>
                    <LoginForm />
                </div>
            </v-col>
        </v-row>
    </div>
</template>