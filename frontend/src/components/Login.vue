<template>
  <v-container fluid fill-height>
    <v-row justify="center">
      <v-col cols="12" :sm="6" :lg="3">
        <v-card>
          <v-card-title> Autentificare </v-card-title>
          <v-card-text>
            <v-form ref="form" v-model="valid">
              <v-text-field
                v-model="email"
                type="text"
                label="Email"
                hint="email, duh!"
                :rules="[rules.required, rules.email]"
                required>
              </v-text-field>
              <v-text-field
                v-model="password"
                type="password"
                label="Parola"
                hint=":|"
                :rules="[rules.required, rules.password]"
                required>
              </v-text-field>
            </v-form>
            <v-alert type="error" v-model="alert" dismissible>
              {{ mesaj }}
            </v-alert>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn @click="register" color="error" text>register</v-btn>
            <v-btn @click="tryLogin" color="primary" text>just do it!</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: 'Login',

  data: () => ({
    mesaj: null,
    alert: false,
    email: null,
    password: null,
    valid: true,
    rules: {
      email: value => {
        /* eslint-disable no-useless-escape */
        const re = new RegExp('[a-z0-9!#$%&\'*+/=?^_`{|}~-]+(?:\.[a-z0-9!#$%&\'*+/=?^_`{|}~-]+)*@(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?')
        return re.test(value) || 'Nu prea arata ca o adresa de email valida...'
      },
      password: value => {
        if (!value || !value.length) return true
        /* eslint-disable no-useless-escape */
        const re = new RegExp('^(?=.*[a-z])(?=.*[A-Z])(?=.*[0-9])(?=.*[!@#\$%\^&\*])(?=.{8,})')
        return re.test(value) || 'Campul trebuie sa fie de minim 8 caractere lungime si sa contina: litere mici, litere mari, cifre si caractere speciale (!, @, #, $, %, ^, &)!'
      },
      required: v => !!v || 'Camp obligatoriu!'
    }
  }),

  methods: {
    register () {
      this.$router.push({ name: 'Register' })
    },
    tryLogin () {
      const valid = this.$refs.form.validate()

      if (valid) {
        const { email, password } = this // mdn object destructuring
        const payload = { email, password, strategy: 'local' }

        this.executeLogin(payload)

        // equivalent
        // const email = this.email
        // const password = this.password
        // const payload = {
        //   email: email,
        //   password: password,
        //   strategy: 'local'
        // }
        // console.log(payload)
      }
    },
    executeLogin (payload) {
      fetch('http://localhost:3030/authentication', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(payload) // body data type must match "Content-Type" header
      }).then(response => {
        return response.json()
      }).then(result => {
        if (result.code === 401) {
          this.mesaj = 'Date de autentificare invalide, dog!'
          this.alert = true
        } else {
          const { accessToken } = result
          localStorage.setItem('token', accessToken)
        }
      }).catch(err => {
        console.log('eroare: ', err)
        alert('Don\'t know what to tell ya, brah!')
      })
    }
  }
}
</script>
