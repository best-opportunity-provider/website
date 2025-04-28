<script setup>
import { useHead } from '#imports'
import Footer from '~/components/Footer.vue'

useHead({
    title: 'Sign in to Offer',
})
</script>

<!-- <template>
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
</template> -->

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
                    <a id="login-page" class="selected-page sign-button">Login</a>
                    <a id="register-page" class="sign-button" href="/sign-up">Register</a>
                </div>
                <div id="auth-form-labels"> <!--grid-->
                    <div id="email-label">
                        <label for="email">Email Address</label>
                        <div id="email-label-content">
                            <img src="~/public/mail.png">
                            <input type="email" name="email" placeholder="Your email"/>
                        </div>
                    </div>
                    <div id="password-label">
                        <label for="password">Password</label>
                        <div id="password-label-content">
                            <img src="~/public/password.png">
                            <input type="password" name="password" placeholder="Your password"/>
                        </div>
                    </div>
                </div>
                <div id="additional">
                    <div id="additional-remember">
                        <label for="remember">Remember me</label>
                        <input type="checkbox" name="remember" />
                    </div>
                    <a id="additional-label">
                        Forgot password?
                    </a>
                </div>
                <div id="auth-form-sign">
                    <button id="sign-in-button">
                        <img src="~/public/sign.png">
                        <p>Sign In</p>
                    </button>
                </div>
            </div>
        </div>

    </div>
</template>

<style>
@import url('~/public/css/sign.css');
</style>
