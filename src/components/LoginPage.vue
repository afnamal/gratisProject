<template>
  <q-page class="flex flex-center">
    <div style="background-color: #f7f5f5; width: 80vw; height: 150px; position: absolute; top: 0;"></div>

    <q-form @submit="onSubmit" class="q-gutter-md" style="max-width:">

      <div class="q-mb-md text-h4 text-grey-8 text-center" style="width: 100%; opacity: 70%; margin-top: -75px; margin-bottom: 100px;">
        GİRİŞ YAP
      </div>

      <p style="margin-left: 17px;margin-bottom: -15px;">E-Posta/<span style="opacity: 50%;">Telefon Numarası </span></p>
      <div class="q-mt-md" style="border: 2px solid purple;border-radius: 5px;">
        <q-input
          outlined
          v-model="email"
          placeholder="E-Posta adresinizi veya telefon numaranızı giriniz"
          color="purple"
        />
      </div>

      <div class="q-mt-md overlay">
        <q-btn label="Şifremi Unuttum" to="/forgot-password" flat class="q-ml-sm" style="margin-bottom: -45px; margin-right: -17px; font-size: small;"/>
      </div>

      <p style="margin-left: 17px;margin-bottom: -15px;"><span style="opacity: 50%;">Şifre </span></p>

      <div class="q-mt-md" style="border: 2px solid purple; border-radius: 5px;">
        <q-input
          outlined
          v-model="password"
          :type="isPwd ? 'password' : 'text'"
          placeholder="Şifrenizi giriniz"
          color="purple"
        >
          <template v-slot:append>
            <q-icon
              :name="isPwd ? 'visibility_off' : 'visibility'"
              class="cursor-pointer"
              @click="isPwd = !isPwd"
            />
          </template>
        </q-input>
      </div>

      <div class="q-mt-md">
        <q-btn class="girisbtn" label="GİRİŞ YAP" type="submit" rounded style="height: 45px;width: 100%; font-size: medium;" />
      </div>
      <div class="q-mt-md">
        <p style="opacity: 70%;">Toplam Submit Sayısı: {{ submitCountRef }}</p>
      </div>
    </q-form>
  </q-page>
</template>

<script lang="ts">
import { ref } from 'vue'
import { getFirestore, collection, addDoc } from 'firebase/firestore'
import { useAuthStore } from 'stores/auth'

export default {
  data () {
    return {
      email: '',
      password: '',
      isPwd: false
    }
  },

  methods: {
    async onSubmit () {
      try {
        const db = getFirestore()
        const usersCollection = collection(db, 'kullanicilar')

        await addDoc(usersCollection, {
          email: this.email,
          password: this.password
        })

        useAuthStore().incrementSubmitCount()

        console.log('Kullanıcı bilgileri başarıyla Firestore\'a kaydedildi.')
      } catch (error) {
        console.error('Firestore kayıt işlemi sırasında bir hata oluştu:', error.message)
      }
    }
  },
  computed: {
    submitCountRef () {
      return useAuthStore().submitCount
    }
  }
}
</script>
<style scoped>
.overlay {
  display: flex;
  align-items: center;
  justify-content: flex-end;
}

.overlay q-btn {
  margin-right: 10px;
  color: #490e6f;
  font-size: small;
}
.girisbtn {
  color: white;
  background-color: #490e6f;
}

</style>
