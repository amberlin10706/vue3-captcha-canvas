# vue3-captcha-canvas

### for vue3 use

## install
```
yarn add vue3-captcha-canvas
```

### usage
```
<script setup>
import { CaptchaImage } from "vue3-captcha-canvas";

const captchaRef = ref();

const verify = () => {
    captchaRef.value.verify("1234")
}
</script>

<template>
    <CaptchaImage ref="captchaRef"/>
</template>
```
