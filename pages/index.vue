<template>
  <div class="register">
    <NuxtLink to="/login">ログイン</NuxtLink>
    <p>新規登録</p>
    <label>ユーザーネーム: <input v-model="username" type="text" required></label>
    <label
      >メールアドレス： <input v-model="email" type="email" required
    /></label>
    <br />
    <label
      >パスワード： <input v-model="password" type="password" required
    /></label>
    <br />
    <button @click="register">新規登録</button>
    <br />
  </div>
</template>

<script>
import firebase from '~/plugins/firebase'
export default {
  data() {
    return {
      username: null,
      email: null,
      password: null,
    }
  },
  methods: {
    register() {
      if (!this.username || !this.email || !this.password) {
        alert('ユーザーネーム、メールアドレスまたはパスワードが入力されていません。')
        return
      }
      firebase
        .auth()
        .createUserWithEmailAndPassword(this.username, this.email, this.password)
        .then((data) => {
          data.user.sendEmailVerification().then(() => {
            this.$router.replace('/confirm')
          })
        })
        .catch((error) => {
          switch (error.code) {
            case 'auth/invalid-email':
              alert('メールアドレスの形式が違います。')
              break
            case 'auth/email-already-in-use':
              alert('このメールアドレスはすでに使われています。')
              break
            case 'auth/weak-password':
              alert('パスワードは6文字以上で入力してください。')
              break
            default:
              alert('エラーが起きました。しばらくしてから再度お試しください。')
              break
          }
        })
    },
  },
}
</script>