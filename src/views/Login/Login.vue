<template>
  <v-app class="app-container">
    <v-container
        style="height: 100%; width: 100%"
    >
      <v-form ref="form" class="login-container d-flex justify-center align-center" style="width: 100%; height: 100%">
        <v-card style="height: 80%; width: 80%; border-radius: 5px;">
          <v-row style="width: 100%; height: 100%" >
            <v-col cols="8" sm="8">
              <v-img
                  style="border-radius: 5px"
                  width="100%"
                  height="100%"
                  position="center center"
                  :src="require('@/assets/images/5437842.jpg')"
              ></v-img>
            </v-col>

            <v-col
                cols="4"
                sm="4"
                class="d-flex justify-center align-center"
            >
              <div class="card-container" >
                <div class="container">
                  <v-text-field
                      v-model="user.email"
                      label="Email"
                      :rules="emailRules"
                  >
                  </v-text-field>
                  <v-text-field
                      v-model="user.password"
                      label="Password"
                      :rules="passwordRules"
                      class="pt-3"
                      type="password"
                  >
                  </v-text-field>
                  <v-spacer class="pt-4"></v-spacer>
                  <v-btn @click="handleLogin"
                         color="primary"
                         width="100%"
                         style="font-family: 'Avenir', Helvetica, Arial, sans-serif"
                  >Đăng nhập
                  </v-btn>
                  <v-spacer></v-spacer>
                  <router-link class="register" to="/register">Đăng kí</router-link>
                </div>
              </div>
            </v-col>

          </v-row>
        </v-card>
      </v-form>

    </v-container>
  </v-app>
</template>

<script>
import AuthService from "@/services/auth.service";
  export default {
    data(){
      return {
        user:{
          email:'admin@gmail.com',
          password:'111111'
        },

        emailRules: [
          v => !!v || 'E-mail phải bắt buộc !',
          v =>  /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'E-mail chưa hợp lệ !',
        ],

        passwordRules: [
          v => !!v || 'Mật khẩu chưa được nhập !',
          v => v.length >= 6 || 'Mật khẩu phải lớn hơn 6 kí tự !'
        ],
      }
    },

    methods:{
      validate() {
        if (this.$refs.form.validate()) {
          this.snackbar = true;
          this.dialogSubmit = true;
        }
      },
      handleLogin(){
        this.validate();
        if (this.user.email && this.user.password)
        {
          this.$store.dispatch('auth/login',this.user)
              .then(()=>{
                AuthService.getMe().then(res => {
                  if (res.data.roles[0].name !== 'admin' && res.data.roles[0].name !== 'leader'){
                    AuthService.logout();
                    alert("Bạn không có quyền truy cập trang này !");
                  }else {
                    this.$router.push('/')
                  }
                }).catch(()=>{
                  console.log("loi")
                })
              })
              .catch((err)=>{
                this.$toast.error(err.response.data.error);
              });
        }
      },
    }
  }
</script>

<style lang="scss" scoped>
.app-container{
  background: rgb(228,108,108);
  background: radial-gradient(circle, rgba(228,108,108,1) 49%, rgba(109,156,210,1) 100%);
}
.card-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  background-color: white;
  box-shadow: black;
}
.register{
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 20px;
  font-weight: 700;
  font-family: "Roboto", sans-serif;
  margin-top:50px;
}

.register:hover{
  color: brown;
}

</style>