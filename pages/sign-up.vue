<script setup>
import { useHead } from '#imports';
import footer from '~/components/Footer.vue';

useHead({
    title: 'Sign up to Offer',
})
</script>

<template>
    <div id="content-container">
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
    </div>
    <Footer />
</template>

<style>
@import url('~/public/css/sign.css');
</style>
