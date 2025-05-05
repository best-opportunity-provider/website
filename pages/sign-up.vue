<script setup>
import { useHead } from '#imports';
import footer from '~/components/Footer.vue';

useHead({
    title: 'Sign up to Offer',
})
</script>

<template>
     <div>
        <div id="title">
            <h1>Welcome Back</h1>
            <div id="title-logo-block">
                <img src="~/public/logo.png">
                <h2>Offer</h2>
            </div>
        </div>
        <div id="container-body">
            <div id="auth-form"> <!--flex-->
                <div id="auth-form-pages">
                    <a id="login-page" class="sign-button" href="/sign-in">Login</a>
                    <a id="register-page" class="selected-page sign-button">Register</a>
                </div>
                <div id="auth-form-labels"> <!--grid-->
                    <div id="email-label">
                        <label for="email">Username</label>
                        <div id="email-label-content">
                            <img src="~/public/mail.png">
                            <input v-model="username_input" @input="handle_username_input"
                                :disabled="request_status === 'pending'" type="text" name="email"
                                placeholder="Username" />
                        </div>
                        <p v-if="username_input_status === 'error'">{{ translations.username_error[lang] }}</p>
                    </div>
                    <div id="email-label">
                        <label for="email">Email</label>
                        <div id="email-label-content">
                            <img src="~/public/mail.png">
                            <input v-model="username_input" @input="handle_username_input"
                                :disabled="request_status === 'pending'" type="text" name="email"
                                placeholder="Email" />
                        </div>
                        <p v-if="username_input_status === 'error'">{{ translations.username_error[lang] }}</p>
                    </div>
                    <div id="password-label">
                        <label for="password">Password</label>
                        <div id="password-label-content">
                            <img src="~/public/password.png">
                            <input v-model="password_input" @input="handle_password_input"
                                :disabled="request_status === 'pending'" type="password" name="password"
                                placeholder="Password" />
                        </div>
                        <p v-if="password_input_status === 'error'">{{ translations.password_error[lang] }}</p>
                    </div>
                </div>
                <div id="additional">
                        <NuxtLink id="additional-label-already-auth">Already have an account?
                        </NuxtLink>
                </div>
                <div id="auth-form-sign">
                    <button @click="async () => { request_status = 'pending', await sign_in() }"
                        :disabled="request_status === 'pending'" id="sign-in-button">
                        <template v-if="request_status !== 'pending'">
                            <img src="~/public/sign.png">
                            <p>Sign In</p>
                        </template>
                        <img v-else src="~/public/loading.gif">
                    </button>
                </div>
                <p v-if="request_status === 'error'">{{ translations.login_error[lang] }}</p>
            </div>
        </div>

    </div>
    <!-- <div id="content-container">
        <div id="form-container">
            <div id="title-container">
                <p>Offer</p>
            </div>
            <div id="form-content-container">
                <div class="form-field-container">
                    <label for="form-email-field" class="form-field-label">Email</label>
                    <input type="email" id="form-email-field" class="form-field-input" name="email"
                        placeholder="email@example.com" />
                </div>
                <div class="form-field-container">
                    <label for="form-username-field" class="form-field-label">Username</label>
                    <input type="text" id="form-username-field" class="form-field-input" name="username"
                        placeholder="Username" />
                </div>
                <div class="form-field-container">
                    <label for="form-password-field" class="form-field-label">Password</label>
                    <input type="password" id="form-password-field" class="form-field-input" name="password"
                        placeholder="Password" />
                </div>
                <button id="form-submit-button">Sign up</button>
            </div>
            <div class="form-tip-container">
                <p>Already have an account? <NuxtLink to="/sign-in">Sign in</NuxtLink>
                </p>
            </div>
        </div>
    </div> -->
    <!-- <Footer /> -->
</template>

<style>
@import url('~/public/css/sign.css');
</style>
