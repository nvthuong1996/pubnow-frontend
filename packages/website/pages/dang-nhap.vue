<template>
  <no-ssr>
    <div class="login-form-inner">
      <BackToHome />
      <va-form ref="form" type="vertical">
        <img class="logo mx-auto d-block mb-4" :src="require('@/assets/images/logo.svg')" />
        <va-form-item label="Tên tài khoản" need>
          <va-input
            name="username"
            v-model="username"
            size="lg"
            placeholder="Tên tài khoản"
            :rules="[{type:'required', tip:'Bạn vui lòng nhập tên tài khoản'}]"
          />
        </va-form-item>
        <va-form-item label="Mật khẩu" need>
          <va-input
            name="password"
            v-model="password"
            type="password"
            size="lg"
            placeholder="Mật khẩu"
            :rules="[{type:'required', tip:'Bạn vui lòng nhập mật khẩu'}, {type:'minlength=6', tip: 'Mật khẩu dài tối thiểu 6 ký tự'}]"
            @confirm="submit"
          />
        </va-form-item>
        <va-form-item>
          <nuxt-link to="#" class="forgot">Bạn quên mật khẩu?</nuxt-link>
        </va-form-item>
        <va-form-item>
          <va-button
            type="primary"
            size="lg"
            block
            @click="submit"
            :disabled="$wait.is('auth.login')"
            :loading="$wait.is('auth.login')"
          >Đăng nhập</va-button>
        </va-form-item>
        <va-form-item>
          <span class="mr-1">Không có tài khoản?</span>
          <nuxt-link to="dang-ky" class="register">Đăng ký ngay</nuxt-link>
        </va-form-item>
      </va-form>
    </div>
  </no-ssr>
</template>

<script>
import { BackToHome } from '@/components/common'

export default {
  layout: 'empty',
  middleware: ['guest'],
  components: {
    BackToHome,
  },
  data() {
    return {
      username: '',
      password: '',
    }
  },
  methods: {
    submit() {
      this.$refs.form.validateFields(async result => {
        if (result.isvalid) {
          try {
            const ok = await this.$store.dispatch('auth/login', {
              username: this.username,
              password: this.password,
            })
            this.notification.info({
              title: `Đăng nhập thành công`,
              message: `Chào mừng bạn quay trở lại. Bạn đang được chuyển về Trang chủ.`,
              duration: 1690,
              onHide: () => {
                const { redirectTo } = this.$route.query
                this.$router.push(redirectTo ? redirectTo : '/')
              },
            })
          } catch (e) {
            this.notification.danger({
              title: `Lỗi xác thực`,
              message: `Vui lòng kiểm tra lại thông tin đăng nhập`,
              duration: 2000,
            })
          }
        }
      })
    },
  },
}
</script>

<style lang="scss" scoped>
@import '@pubnow/ui/scss/_sizes.scss';
@import '@pubnow/ui/scss/_colors.scss';

.login-form-inner {
  width: 365px;
  margin: 200px auto;

  .logo {
    height: $side-menu-w;
    width: $side-menu-w;
  }

  .forgot {
    color: $text;
  }
  .forgot,
  .register {
    &:hover {
      text-decoration: none;
    }
  }
}
</style>
