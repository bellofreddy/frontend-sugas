<script setup>
import authV1MaskDark from '@images/pages/auth-v1-mask-dark.png'
import authV1MaskLight from '@images/pages/auth-v1-mask-light.png'
import { useTheme } from 'vuetify'
const form = ref({
  email: '',
  password: '',
  remember: false,
})

const vuetifyTheme = useTheme()

const authThemeMask = computed(() => {
  return vuetifyTheme.global.name.value === 'light' ? authV1MaskLight : authV1MaskDark
})

const isPasswordVisible = ref(false)
</script>

<template>
  <div class="auth-wrapper d-flex align-center justify-center pa-4">
    <VCard
      class="auth-card pa-4 pt-7"
      max-width="448"
    >
      <VCardItem class="justify-center">
        <!-- eslint-disable vue/no-v-html -->
        <div class="d-flex">
          <img
            src="../../../public/logo.png"
            alt="Logo"
            width="100"
          />
        </div>
        <h2 class="font-weight-medium text-2xl text-uppercase">Sugaas</h2>
      </VCardItem>

      <VCardText class="pt-2">
        <h4 class="text-h4 mb-1">Welcome to Sugaas! 👋🏻</h4>
        <p class="mb-0">Please sign-in to your account and start the adventure</p>
      </VCardText>

      <VCardText>
        <v-form
          ref="form"
          validate-on="login lazy"
          @submit.prevent="login"
        >
          <VRow>
            <!-- email -->
            <VCol cols="12">
              <v-text-field
                v-model="email"
                :rules="emailRules"
                label="Email"
                placeholder="example@example.com"
                autocomplete="email"
              />
            </VCol>

            <!-- password -->
            <VCol cols="12">
              <v-text-field
                v-model="password"
                label="Password"
                :rules="passwordRules"
                placeholder="············"
                :type="isPasswordVisible ? 'text' : 'password'"
                :append-inner-icon="isPasswordVisible ? 'ri-eye-off-line' : 'ri-eye-line'"
                @click:append-inner="isPasswordVisible = !isPasswordVisible"
              />

              <!-- remember me checkbox -->
              <div class="d-flex align-center justify-space-between flex-wrap my-6">
                <VCheckbox
                  v-model="form.remember"
                  label="Remember me"
                />

                <a
                  class="text-primary"
                  href="/validar"
                >
                  Forgot Password?
                </a>
              </div>

              <!-- login button -->
              <VBtn
                block
                type="submit"
                @click="login"
              >
                Login
              </VBtn>
            </VCol>
          </VRow>
        </v-form>
      </VCardText>
    </VCard>

    <!-- bg img -->
  </div>
</template>
<script>
import axios from 'axios'

export default {
  data: () => ({
    API: process.env.VUE_APP_API,
    loading: false,
    email: '',
    emailRules: [
      value => !!value || 'E-mail is required.',
      value => /.+@.+\..+/.test(value) || 'E-mail must be valid.',
    ],
    password: '',
    passwordRules: [
      value => !!value || 'Password is required.',
      // Agregar más reglas según sea necesario
    ],
  }),
  methods: {
    async login() {
      const isValid = this.$refs.form.validate() // Valida el formulario

      if (!isValid) {
        console.log('Formulario no válido')
        return // Si el formulario no es válido, no se envía
      }

      try {
        const response = await axios.post(`http://localhost:3000/auth/login`, {
          email: this.email,
          password: this.password,
        })

        this.$store.commit('setUser', response.data)

        this.$notify({ text: 'Login exitoso', type: 'success' })
        this.$store.dispatch('login')

        this.$router.push({ path: '/sugas' })
      } catch (error) {
        if (error.response.data.message === 'Incorrect password') {
          this.$notify({ text: 'Contraseña incorrecta', type: 'error' })
          //this.passwordError = 'Contraseña incorrecta'
        } else if (error.response.data.message === 'Invalid credentials') {
          this.$notify({ text: 'El usuario no existe', type: 'error' })
          //this.passwordError = 'El usuario no existe'
        } else if (error.request) {
          //  console.error('Sin respuesta del servidor:', error.request)
        } else {
          // console.error('Error en la solicitud:', error.message)
        }
        //console.error('Configuración completa del error:', error.config)
      }
    },
  },
}
</script>
