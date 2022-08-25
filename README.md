# vue-captcha-canvas

## install
```
yarn add vue-captcha-canvas
```

### usage
```
<script setup>
import CaptchaImage from "vue-captcha-canvas";

const captchaRef = ref();

const verify = () => {
    captchaRef.value.verify("1234")
}
</script>

<template>
    <CaptchaImage ref="captchaRef"/>
</template>
```
