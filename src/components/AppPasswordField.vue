<template>
  <div class="column flex q-col-gutter-xs">
    <q-input bg-color="white" v-bind="$attrs" class="app-password-field full-width q-pa-none" data-cy="password" hide-bottom-space :label="label" outlined :type="passwordInputType">
      <template #append>
        <q-icon v-if="showPassword" class="cursor-pointer" name="visibility" @click="toggleShowPassword" />
        <q-icon v-else class="cursor-pointer" name="visibility_off" @click="toggleShowPassword" />
      </template>
    </q-input>
    <div v-if="showRate()">
      <div class="q-gutter-sm q-mx-none q-px-none row">
        <div class="app-password-field__password-rate bg-grey col-grow q-mx-none" :class="getColor(0)"></div>
        <div class="app-password-field__password-rate bg-grey col-grow" :class="getColor(1)"></div>
        <div class="app-password-field__password-rate bg-grey col-grow" :class="getColor(2)"></div>
        <div class="app-password-field__password-rate bg-grey col-grow" :class="getColor(3)"></div>
      </div>
      <div class="text-caption" :class="passwordStrength.color" data-cy="app-password-field-strength">{{ passwordStrength.label }}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'AppPasswordField',

  data(){
    return {
      showPassword: false,
      passwordStrength: ''
    }
  },

  props: {
    label: {
      type: String,
      default: 'Senha'
    },

    useRate: {
      type: Boolean
    },

    value: {
      type: String,
      default: ''
    }
  },

  computed: {
    passwordInputType () {
      return this.showPassword ? 'text' : 'password'
    }
  },

  methods: {
    toggleShowPassword () {
      return this.showPassword = !this.showPassword
    },

    showRate () {
      return this.useRate && !!this.value
    },
    
    getColor (squareIndex) {
      const password = this.value

      const onlyLetters = /^[A-Za-z]*$/.test(password)
      const onlyNumbers = /^[0-9]*$/.test(password)
      const hasSpecialChar = /[!@#$%^&*()_+\-=\[\]{};':"\\|,.<>\/?]/.test(password)
      const hasLettersNumbers = /[A-Za-z0-9]/.test(password)
      const hasUppercase = /[A-Z]/.test(password)
      const hasMinLength = password.length >= 6 

      const strenghtWeak = onlyLetters || onlyNumbers || hasLettersNumbers && !hasMinLength
      const strenghtMedium = hasLettersNumbers && hasMinLength
      const strenghtStrong = strenghtMedium && (hasUppercase || hasSpecialChar)
      const strenghtVeryStrong = strenghtMedium && hasUppercase && hasSpecialChar 

      if(strenghtVeryStrong) {
        this.passwordStrength = { label:'Muito forte', color: 'text-light-green-8' }
        return 'app-password-field__password-rate--very-strong'
      }
      
      if(strenghtStrong && squareIndex <= 2) {
        this.passwordStrength = { label:'Forte', color: 'text-light-green-5' }
        return 'app-password-field__password-rate--strong'
      }
      
      if(strenghtMedium && squareIndex <= 1) {
        this.passwordStrength = { label:'Médio', color: 'text-yellow-5'}
        return 'app-password-field__password-rate--medium'
      }
      
      if(strenghtWeak && squareIndex < 1) {
        this.passwordStrength = { label:'Fraca', color: 'text-red-10'}
        return 'app-password-field__password-rate--weak'
      }

    }
  }
}
</script>

<style lang="scss">
  .app-password-field {
    &__password-rate {
      background: grey;
      height: 4px;

      &--weak {
        background: #c62828 !important;
      }

      &--medium {
        background: #ffee58 !important;
      }

      &--strong {
        background: #9ccc65 !important;
      }

      &--very-strong {
        background: #43A047 !important
      }
    }
  }
</style>