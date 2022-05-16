<template>
    <div class='vue-tempalte bg-dark p-5 rounded'>
        <Form
        novalidate="novalidate"
        id="kt_login_signup_form"
        @submit="onSubmitRegister"
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
                <p class='form-label text-secondary fs-6'>Username requires 3 to 20 characters using letters, digits and optionally a single punctuation _ character.</p>
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
                  <p class='text-secondary'>Password must be at least 8 characters long, contain one lowercase letter, contain one uppercase letter and include at lease one number(0-9)</p>
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

            <div class='form-group pb-3 d-flex'>
              <input type="checkbox" class=' me-2 check' />
              <p class="text-white">I want to receive emails from VenewLive and its partners about upcoming shows and information.</p>
            </div>

            <div class='form-group pb-3 d-flex'>
              <input type="checkbox" class=' me-2 check' />
              <p class="text-white">By submitting this form I agree to the <a href="#">Terms of Service</a> and <a href="#">Privacy Notice</a></p>
            </div>

            <button type='submit' class='btn btn-lg btn-primary'>Sign Up</button>
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
  name: 'sign-up',
  components: {
    Field,
    Form,
    ErrorMessage
  },
  setup () {
    const store = useStore()

    const submitButton = ref<HTMLElement | null>(null)

    const registration = Yup.object().shape({
      username: Yup.string().matches(/^(?=[a-zA-Z0-9._]{3,20}$)(?!.*[_.]{2})[^_.].*[^_.]$/, 'Must Contain at least 3 Characters and at most 20 Characters, using Letters, Digits and one single punctuation _Character').required(),
      email: Yup.string().min(4).required().email().label('Email'),
      emailconfirm: Yup.string().min(4).required().email().oneOf([Yup.ref('email'), null], 'Emails must match').label('Confirm Email'),
      passwd: Yup.string().min(8).required('Please Enter your password').matches(
        /^(?=.*[A-Za-z])(?=.*\d)(?=.*[@$!%*#?&])[A-Za-z\d@$!%*#?&]{8,}$/,
        'Must Contain 8 Characters, One Uppercase, One Lowercase, One Number and one special case Character'
      ),
      cpasswd: Yup.string()
        .min(8)
        .required()
        .oneOf([Yup.ref('passwd'), null], 'Passwords must match')
        .label('Password Confirmation')
    })

    const onSubmitRegister = (values: any) => {
      // Activate indicator
      submitButton.value?.setAttribute('data-kt-indicator', 'on')

      console.log(store)
      setTimeout(() => {
        // Send login request
        store.dispatch(Actions.REGISTER, values)
          .then(() => {
            Swal.fire({
              text: 'Username: ' + values.username + ', email: ' + values.email,
              icon: 'success',
              buttonsStyling: false,
              confirmButtonText: 'Ok, got it!',
              customClass: {
                confirmButton: 'btn fw-bold btn-light-primary'
              }
            }).then(function () {
              // Go to page after successfully login
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
      }, 1000)
      // Dummy delay
    }

    return {
      registration,
      onSubmitRegister,
      submitButton
    }
  }
})
</script>
