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
        v-model="name"
        name="name"
        autocomplete="name"
        placeholder="이름을 입력해주세요"
      />

      <!-- 주민등록번호 입력 -->
      <div class="w-full flex gap-1">
        <InputSmall
          ref="rrnFrontRef"
          v-model="rrnFront"
          name="id-1"
          class="flex-1 border-[2px]"
          placeholder="주민번호 앞자리"
          maxlength="6"
          @input="handleRrnFrontInput"
        />
        <div class="flex items-center text-lg text-[#999999]">
          -
        </div>
        <InputSmall
          ref="rrnBackRef"
          v-model="rrnBack"
          name="id-2"
          class="flex-1 border-[2px]"
          type="password"
          placeholder="주민번호 뒷자리"
          maxlength="7"
        />
      </div>

      <Input
        v-model="accountNumber"
        name="cc-number"
        autocomplete="cc-number"
        placeholder="계좌번호를 입력해주세요"
      />

      <Input
        v-model="accountPassword"
        name="account-pw"
        class="w-[60%]"
        type="password"
        placeholder="계좌 비밀번호를 입력해주세요"
      />
    </div>

    <div class="w-full flex justify-between gap-4 px-4">
      <ButtonMediumSub @click="goToLogin">
        취소
      </ButtonMediumSub>
      <ButtonMediumMain
        class="w-1/2"
        @click="goToSignup"
      >
        본인인증
      </ButtonMediumMain>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

import logo from '@/assets/moa_logo.jpg'
import ButtonMediumMain from '@/shared/components/atoms/button/ButtonMediumMain.vue'
import ButtonMediumSub from '@/shared/components/atoms/button/ButtonMediumSub.vue'
import Input from '@/shared/components/atoms/input/Input.vue'
import InputSmall from '@/shared/components/atoms/input/InputSmall.vue'
import TypographyHead3 from '@/shared/components/atoms/typography/TypographyHead3.vue'

const router = useRouter()

const name = ref('')
const rrnFront = ref('')
const rrnBack = ref('')
const accountNumber = ref('')
const accountPassword = ref('')

const rrnFrontRef = ref(null)
const rrnBackRef = ref(null)

const handleRrnFrontInput = () => {
  // 6자리 초과 입력 제한
  if (rrnFront.value.length > 6) {
    rrnFront.value = rrnFront.value.slice(0, 6)
  }
}

const goToLogin = () => {
  router.push({ name: 'login' })
}

const goToSignup = async () => {
  // 주민등록번호 합치기
  const idCardNumber = `${rrnFront.value}${rrnBack.value}`

  const authData = {
    name: name.value,
    idCardNumber,
    accountNumber: accountNumber.value,
    accountPassword: accountPassword.value,
  }

  try {
    const response = await fetch('http://localhost:8080/api/public/verifyJoin', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      credentials: 'include',
      body: JSON.stringify(authData),
    })

    if (!response.ok) {
      const errorText = await response.text()
      throw new Error(`본인인증 실패: ${response.status} - ${errorText}`)
    }

    const data = await response.json()
    console.log('서버 응답:', data)

    localStorage.setItem('certData', JSON.stringify(authData))

    router.push({ name: 'signup' })
  } catch (error) {
    console.error('본인인증 실패:', error)
    alert('본인인증에 실패했습니다. 정보를 확인해주세요.')
  }
}
</script>
