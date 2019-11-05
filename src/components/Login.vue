<template>
    <el-form :model="loginForm" :rules="rules2" ref="loginForm" label-position="left" label-width="60px" class="demo-ruleForm-login-container" >
      <h3 class="title">系统登录</h3>
      <el-form-item label="用户名" prop="userName" style="text-align: center">
        <el-input type="text" v-model="loginForm.userName" auto-complete="off" placeholder="请输入账号" clearable></el-input>
      </el-form-item>

      <el-form-item label="密码" prop="password">
        <el-input type="password" v-model="loginForm.password" auto-complete="off" placeholder="请输入密码" clearable></el-input>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="submitForm('loginForm')">登录</el-button>
        <!--<el-button @click="resetForm('loginForm')">重置</el-button>-->
      </el-form-item>
    </el-form>

</template>

<script>
  import axios from 'axios'
  export default {
    data() {
      var validateuser = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入用户名'));
        } else {
          if (this.loginForm.checkPass !== '') {
            this.$refs.loginForm.validateField('checkPass');
          }
          callback();
        }
      };
      var validatePass = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入密码'));
        } else {
          callback();
        }
      };
      return {
        loginForm: {
          userName: 'test',
          password: '123456'
        },
        rules2: {
          userName: [
            { validator: validateuser, trigger: 'blur' }
          ],
          password: [
            { validator: validatePass, trigger: 'blur' }
          ],

        }
      };
    },
    methods: {
      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            // alert('submit!');
            axios.post('http://127.0.0.1:5000/login', {
              params: {
                userName: this.loginForm.userName,
                password: this.loginForm.password
              }
            })
              .then(res => {
                console.log(res)
                if(res.data = 1) {
                  this.$router.push({path: '/home'})
                }
              })
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
     /* resetForm(formName) {
        this.$refs[formName].resetFields();
      }*/
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
