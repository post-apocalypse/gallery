<template>
  <div class="_layout">
    <app-notification />
    <the-navbar/>
    <Transition name="route" mode="out-in">
      <div class="auth">
      <img src="../assets/img/gallery-logo.webp" alt="Auth">
      <div class="_column">
        <div class="_info"><h1>To get started, you need to log in to your account</h1></div>
        <div class="_mark"><p>To add new images to the Gallery, you need to log in</p></div>
        <form class="_column" style="justify-content: space-between;" @submit.prevent="onSubmit">
          <div class="_row">
            <div :class="['_column', {_invalid: eError}]">
              <div :class="['_notf', {'_notf-v': !eError && email}]">
                <p>*&nbsp;</p><small>{{ eError || 'Enter your E-Mail'}}</small>
              </div>
              <input type="email" placeholder="E-Mail" v-model="email" @blur="eBlur">
            </div>
            <div :class="['_column', {_invalid: pError}]">
              <div :class="['_notf', {'_notf-v': !pError && password}]">
                <p>*&nbsp;</p><small>{{ pError || 'Enter your password'}}</small>
              </div>
              <input type="password" placeholder="Пароль" v-model="password" @blur="pBlur">
            </div>
          </div>
          <button class="_btn" :disabled="isSubmitting">
            <h3>Log in</h3>
          </button>
        </form>
      </div>
      </div>
    </Transition>
  </div>
</template>

<script>
import { useField, useForm } from 'vee-validate'
import { useStore } from 'vuex'
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import * as yup from 'yup'
import AppNotification from '../components/AppNotification'
import TheNavbar from '../components/TheNavbar'

export default {
  components: { AppNotification, TheNavbar },
  setup () {
    const store = useStore()
    const router = useRouter()
    const { handleSubmit, isSubmitting } = useForm()
    const { value: email, errorMessage: eError, handleBlur: eBlur } = useField(
      'email',
      yup
        .string()
        .trim()
        .required('Enter your E-Mail')
        .email('Enter the correct E-Mail')
    )
    const { value: password, errorMessage: pError, handleBlur: pBlur } = useField(
      'password',
      yup
        .string()
        .trim()
        .required('Enter your password')
    )
    const onSubmit = handleSubmit(async values => {
      await store.dispatch('login', values)
      if (store.getters.isAuthenticated) {
        router.push('/')
      }
    })
    const currentWidth = ref(0)
    const updateWidth = () => { currentWidth.value = window.innerWidth }
    window.addEventListener('resize', updateWidth)
    onMounted(() => {
      updateWidth()
      email.value = 'default@gallery.app'
      password.value = 'default'
    })

    return {
      email,
      eError,
      eBlur,
      password,
      pError,
      pBlur,
      onSubmit,
      isSubmitting,
      currentWidth
    }
  }
}
</script>

<style lang="scss">
@import '../assets/scss/main';
.auth {
  @include glass-effect;
  @include all-cent;
  gap: $space;
  padding: $space;
  & ._column {
    width: 100%;
  }
  & ._info {
    display: flex;
    align-items: center;
    gap: 0;
    flex-wrap: wrap;
    img {
      max-height: 50px;
      width: auto;
    }
    @media (max-width: $medium) {
      font-size: 0.8em;
    }
    @media (max-width: $small) {
      text-align: center;
    }
  }
  img {
    max-height: 250px;
    width: auto;
  }
  & ._row {
    @media (max-width: $medium) {
      flex-direction: column;
      & ._column {
        gap: $space / 2;
      }
    }
  }
  @media (max-width: $small) {
    flex-direction: column;
    img {
      max-height: 300px;
      width: auto;
    }
  }
}
</style>
