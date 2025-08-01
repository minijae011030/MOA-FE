<template>
  <div class="w-full h-dvh flex flex-col pt-20 items-center gap-8 overflow-hidden">
    <img
      :src="logo"
      alt="moa logo"
      class="w-[103px] h-[54.65px] object-contain"
    >

    <TypographyHead3>
      <span class="font-inter">우리의 여행을 </span>
      <span class="text-[#87BFFF] font-inter">MOA</span>
    </TypographyHead3>

    <div class="w-full flex flex-col gap-4 px-4">
      <Input
        v-model="email"
        name="email"
        autocomplete="email"
        placeholder="이메일을 입력해주세요"
      />
      <Input
        v-model="password"
        name="password"
        autocomplete="new-password"
        type="password"
        placeholder="비밀번호를 입력해주세요"
      />
      <Input
        v-model="confirmPassword"
        name="confirm-password"
        autocomplete="new-password"
        type="password"
        placeholder="비밀번호를 한번 더 입력해주세요"
      />
    </div>

    <div class="w-full flex justify-between gap-4 px-4">
      <ButtonMediumSub @click="goToLogin">
        취소
      </ButtonMediumSub>
      <ButtonMediumMain @click="handleSignup">
        회원가입
      </ButtonMediumMain>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import { useRouter } from 'vue-router'

import logo from '@/assets/moa_logo.jpg'
import ButtonMediumMain from '@/shared/components/atoms/button/ButtonMediumMain.vue'
import ButtonMediumSub from '@/shared/components/atoms/button/ButtonMediumSub.vue'
import Input from '@/shared/components/atoms/input/Input.vue'
import TypographyHead3 from '@/shared/components/atoms/typography/TypographyHead3.vue'

const router = useRouter()

const email = ref('')
const password = ref('')
const confirmPassword = ref('')

// 본인인증 정보 가져오기
const name = ref('')
const idCardNumber = ref('')
const accountNumber = ref('')
const accountPassword = ref('')

onMounted(() => {
  const certData = JSON.parse(localStorage.getItem('certData') || '{}')
  name.value = certData.name || ''
  idCardNumber.value = certData.idCardNumber || ''
  accountNumber.value = certData.accountNumber || ''
  accountPassword.value = certData.accountPassword || ''
})

const goToLogin = () => {
  router.push({ name: 'login' })
}

const handleSignup = async () => {
  // 한번더 입력한 비밀번호가 일치하지 않을 때 실행
  if (password.value !== confirmPassword.value) {
    alert('비밀번호가 일치하지 않습니다.')
    return
  }

  const signupData = {
    email: email.value,
    password: password.value,
    name: name.value,
    idCardNumber: idCardNumber.value,
    accountNumber: accountNumber.value,
    accountPassword: accountPassword.value,
    role: 'ROLE_USER',
  }

  try {
    const response = await fetch('http://localhost:8080/api/public/join', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      credentials: 'include', // 세션 유지
      body: JSON.stringify(signupData),
    })

    if (!response.ok) {
      const errorText = await response.text()
      throw new Error(`회원가입 실패: ${response.status} - ${errorText}`)
    }

    const data = await response.json()
    console.log('서버 응답:', data)

    localStorage.setItem('signData', JSON.stringify(signupData))

    localStorage.removeItem('certData') // 본인인증 데이터 제거

    alert('회원가입 성공!')

    router.push({ name: 'login' })
  } catch (error) {
    console.error('회원가입 실패:', error)
    alert('회원가입에 실패했습니다. 정보를 확인해주세요.')
  }
}
</script>
