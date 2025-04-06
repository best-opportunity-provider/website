<script setup>
import { useHead } from '#imports'
import Footer from '~/components/Footer.vue'

useHead({
    title: 'Sign in to Offer',
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
                    <label for="form-username-field" class="form-field-label">Username</label>
                    <input type="text" id="form-username-field" class="form-field-input" name="username"
                        placeholder="Username" />
                </div>
                <div class="form-field-container">
                    <label for="form-password-field" class="form-field-label">Password</label>
                    <input type="password" id="form-password-field" class="form-field-input" name="password"
                        placeholder="Password" />
                </div>
                <button id="form-submit-button">Sign in</button>
            </div>
            <div class="form-tip-container">
                <p>New to Offer? <NuxtLink to="/sign-up">Sign up</NuxtLink>
                </p>
            </div>
        </div>
    </div>
    <Footer />
</template>

<style>
@import url('~/public/css/sign.css');
</style>
