<template>
  <q-page class="row justify-center items-center">
    <q-form class="square-card row justify-center" @submit.prevent="handlerLogin" ref="myform">
      <q-card class="q-pa-sm shadow-2">
        <q-card-section>
          <p class="col-12 text-h5 text-left">Login</p>
        </q-card-section>

        <q-card-section>
          <div class="col-md-4 col-sm-6 col-xs-10 q-gutter-y-md">
            <q-input filled v-model="form.email" label="Email" type="email" lazy-rules :rules="[
              (val) => (val && val.length > 0) || 'Email é obrigatório',
              isValidEmail,
            ]">
              <template v-slot:prepend>
                <q-icon name="email" size="xs" dense />
              </template>
              <template v-slot:append>
                <q-icon name="close" size="xs" @click="form.email = ''" />
              </template>

            </q-input>
            <q-input filled v-model="form.password" label="Senha" :type="visibility" lazy-rules :rules="[
              (val) => (val && val.length > 0) || 'Senha é obrigatória',
            ]">
              <template v-slot:prepend>
                <q-icon name="lock" size="xs" dense />
              </template>
              <template v-slot:append>
                <q-icon name="close" size="xs" dense @click="form.password = ''" class="cursor-pointer" />
              </template>

              <q-btn v-if="visibility == 'password'" round dense flat icon="visibility" @click="changeTypeEdit()"></q-btn>
              <q-btn v-else round dense flat icon="visibility_off" @click="changeTypeEdit()"></q-btn>

            </q-input>
            <q-btn label="Login" color="primary" class="full-width" type="submit"></q-btn>
            <q-btn label="Registrar" color="primary" class="full-width" flat :to="{ name: 'register' }"></q-btn>
            <q-btn label="Esqueci a senha" color="primary" class="full-width" flat
              :to="{ name: 'forgot-password' }"></q-btn>
          </div>
        </q-card-section>
      </q-card>
    </q-form>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from "vue";
import { useRouter } from "vue-router";

import useAuthUser from "src/composables/UserAuthUser";
import useNotify from "src/composables/UseNotify";

export default defineComponent({
  name: "LoginPage",
  setup () {
    const router = useRouter();
    const { login, isLoggedIn } = useAuthUser();
    const { notifyError, notifySuccess } = useNotify();

    const form = ref({
      email: "",
      password: "",
    });

    onMounted(() => {
      if (isLoggedIn) {
        router.push({ name: "me" });
      }
    });

    const handlerLogin = async () => {
      try {
        await login(form.value);
        router.replace({ name: "me" });
        notifySuccess("Bem vindo! 😁");
      } catch (error) {
        notifyError("Falha ao executar o seu acesso! " + error.message);
      }
    };

    return { form, handlerLogin };
  },
  data () {
    return {
      email: "",
      password: "",
      visibility: "password",
    };
  },
  methods: {
    changeTypeEdit () {
      if (this.visibility == "password") {
        this.visibility = "text";
      } else {
        this.visibility = "password";
      }
    },
    isValidEmail (val) {
      const emailPattern =
        /^(?=[a-zA-Z0-9@._%+-]{6,254}$)[a-zA-Z0-9._%+-]{1,64}@(?:[a-zA-Z0-9-]{1,63}\.){1,8}[a-zA-Z]{2,63}$/;
      return emailPattern.test(val) || "Formato de email inválido";
    },
  },
});
</script>

<style lang="scss"></style>
