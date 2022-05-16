<template>
    <div class='vue-tempalte'>
        <Form
        :validation-schema='registration'
        >
            <h3 class='text-white'>Create a new account </h3>
            <div class='form-group pb-3'>
                <p class='form-label fw-bolder text-light fs-6'>Email</p>
                <Field type='email' class='bg-secondary text-white form-control form-control-lg' name='email' placeholder='' />
                <div class='fv-plugins-message-container'>
                  <div class='fv-help-block'>
                    <ErrorMessage name='email' class="text-danger" />
                  </div>
                </div>
            </div>
            <div class='form-group pb-3'>
                <p class='form-label fw-bolder text-light fs-6'>Confirm Email</p>
                <Field type='email' class='bg-secondary text-white form-control form-control-lg' name='emailconfirm' placeholder='' />
                <div class='fv-plugins-message-container'>
                  <div class='fv-help-block'>
                    <ErrorMessage name='emailconfirm' class="text-danger" />
                  </div>
                </div>
            </div>
            <div class='form-group pb-3'>
                <p class='form-label fw-bolder text-light fs-6'>Username</p>
                <Field type='text' class='bg-secondary text-white form-control form-control-lg' name='username' placeholder='' />
                <div class='fv-plugins-message-container'>
                  <div class='fv-help-block'>
                    <ErrorMessage name='username' class="text-danger" />
                  </div>
                </div>
            </div>

            <div class="mb-10 fv-row" data-kt-password-meter="true">
              <!--begin::Wrapper-->
              <div class="mb-1">
                <!--begin::Label-->
                <p class="form-label fw-bolder text-light fs-6"> Password </p>
                <!--end::Label-->
                <!--begin::Input wrapper-->
                <div class="position-relative mb-3">
                  <Field
                    class="form-control form-control-lg form-control-solid bg-secondary text-white"
                    type="password"
                    placeholder=""
                    name="passwd"
                    autocomplete="off"
                  />
                  <div class="fv-plugins-message-container">
                    <div class="fv-help-block">
                      <ErrorMessage name="passwd" class='text-danger' />
                    </div>
                  </div>
                </div>
                <!--end::Input wrapper-->
              </div>
              <!--end::Wrapper-->
            </div>

            <div class="mb-10 fv-row" data-kt-password-meter="true">
              <!--begin::Wrapper-->
              <div class="mb-1">
                <!--begin::Label-->
                <p class="form-label fw-bolder text-light fs-6"> Confirm Password </p>
                <!--end::Label-->
                <!--begin::Input wrapper-->
                <div class="position-relative mb-3">
                  <Field
                    class="form-control form-control-lg form-control-solid bg-secondary text-white"
                    type="password"
                    placeholder=""
                    name="cpasswd"
                    autocomplete="off"
                  />
                  <div class="fv-plugins-message-container">
                    <div class="fv-help-block">
                      <ErrorMessage name="cpasswd" class='text-danger' />
                    </div>
                  </div>
                </div>
                <!--end::Input wrapper-->
              </div>
              <!--end::Wrapper-->
            </div>

            <!-- <div class='form-group pb-3'>
                <p class='text-white'>Confirm Password</p>
                <Field type='password' class='bg-secondary text-white form-control form-control-lg' name='cpassword' />
                <div class='fv-plugins-message-container'>
                  <div class='fv-help-block'>
                    <ErrorMessage name='cpassword' class="text-danger" />
                  </div>
                </div>
            </div> -->
            <button type='submit' class='btn-light btn-dark btn-lg btn-block'>Sign Up</button>
            <p class='forgot-password text-right mt-2 mb-4'>
                <router-link to='/forgot-password'>Forgot password ?</router-link>
            </p>
        </Form>
    </div>
</template>

<script lang='ts'>
import { defineComponent, ref } from 'vue'
import { ErrorMessage, Field, Form } from 'vee-validate'
import * as Yup from 'yup'
import { useStore } from 'vuex'
import { useRouter } from 'vue-router'
import { Actions } from '@/store/enums/StoreEnums'
import Swal from 'sweetalert2'

export default defineComponent({
  name: 'HomeView',
  components: {
    Field,
    Form,
    ErrorMessage
  },
  setup () {
    const store = useStore()
    const router = useRouter()

    const submitButton = ref<HTMLElement | null>(null)

    const registration = Yup.object().shape({
      username: Yup.string().required().label('User Name'),
      email: Yup.string().min(4).required().email().label('Email'),
      emailconfirm: Yup.string().min(4).required().email().oneOf([Yup.ref('email'), null], 'Emails must match').label('Confirm Email'),
      passwd: Yup.string().min(4).required().label('Password'),
      cpasswd: Yup.string()
        .min(4)
        .required()
        .oneOf([Yup.ref('passwd'), null], 'Passwords must match')
        .label('Password Confirmation')
    })

    const onSubmitRegister = (values: any) => {
      // Clear existing errors
      store.dispatch(Actions.LOGOUT)

      // Activate indicator
      submitButton.value?.setAttribute('data-kt-indicator', 'on')

      // Dummy delay
      setTimeout(() => {
        // Send login request
        store
          .dispatch(Actions.REGISTER, values)
          .then(() => {
            Swal.fire({
              text: 'All is cool! Now you submit this form',
              icon: 'success',
              buttonsStyling: false,
              confirmButtonText: 'Ok, got it!',
              customClass: {
                confirmButton: 'btn fw-bold btn-light-primary'
              }
            }).then(function () {
              // Go to page after successfully login
              router.push({ name: 'dashboard' })
            })
          })
          .catch(() => {
            Swal.fire({
              text: store.getters.getErrors[0],
              icon: 'error',
              buttonsStyling: false,
              confirmButtonText: 'Try again!',
              customClass: {
                confirmButton: 'btn fw-bold btn-light-danger'
              }
            })
          })

        submitButton.value?.removeAttribute('data-kt-indicator')
      }, 2000)
    }

    return {
      registration,
      onSubmitRegister,
      submitButton
    }
  }
})
</script>
