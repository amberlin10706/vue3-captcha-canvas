<script setup>
import { computed, defineExpose, defineProps, onMounted, ref } from "vue";

const props = defineProps({
  /**
   * 驗證碼長度
   */
  codeLength: {
    type: Number,
    default: 4,
  },
  /**
   * A A~Z
   * a a~z
   * 0 0~9
   */
  codeType: {
    type: Array,
    default: () => ["A", "a", "0"],
  },
  /**
   * 容器寬度
   */
  contentWidth: {
    type: Number,
    default: 120,
  },
  /**
   * 容器高度
   */
  contentHeight: {
    type: Number,
    default: 40,
  },
  /**
   * 字型最小值
   */
  fontSizeMin: {
    type: Number,
    default: 24,
  },
  /**
   * 字型最大值
   */
  fontSizeMax: {
    type: Number,
    default: 36,
  },
  /**
   * 文字顏色最小值
   */
  textColorMin: {
    type: Number,
    default: 50,
  },
  /**
   * 文字顏色最大值
   */
  textColorMax: {
    type: Number,
    default: 160,
  },
  /**
   * 文字旋轉最小值
   */
  textDegMin: {
    type: Number,
    default: -30,
  },
  /**
   * 文字旋轉最大值
   */
  textDegMax: {
    type: Number,
    default: 30,
  },
  /**
   * 圖片背景色最小值
   */
  backgroundColorMin: {
    type: Number,
    default: 200,
  },
  /**
   * 圖片背景色最大值
   */
  backgroundColorMax: {
    type: Number,
    default: 220,
  },
  /**
   * 干擾點數量
   */
  dotCount: {
    type: Number,
    default: 30,
  },
  /**
   * 干擾點顏色最小值
   */
  dotColorMin: {
    type: Number,
    default: 0,
  },
  /**
   * 干擾點顏色最大值
   */
  dotColorMax: {
    type: Number,
    default: 255,
  },
  /**
   * 干擾線數量
   */
  lineCount: {
    type: Number,
    default: 4,
  },
  /**
   * 干擾線顏色最小值
   */
  lineColorMin: {
    type: Number,
    default: 40,
  },
  /**
   * 干擾線顏色最大值
   */
  lineColorMax: {
    type: Number,
    default: 180,
  },
});

const codeTypeMap = {
  A: "ABCDEFGHIJKLMNOPQRSTUVWXYZ",
  a: "abcdefghijklmnopqrstuvwxyz",
  0: "0123456789",
};

const verifyCode = ref("0000");

const codeRandomList = computed(() => {
  return props.codeType.map((it) => codeTypeMap[it]).join("");
});

// 生成一個隨機整數
function randomNum(min, max) {
  return Math.floor(Math.random() * (max - min) + min);
}

// 生成一個隨機顏色
function randomColor(min, max) {
  let r = randomNum(min, max);
  let g = randomNum(min, max);
  let b = randomNum(min, max);
  return `rgb(${r},${g},${b})`;
}

// 生成一個隨機驗證碼
function randomCode() {
  let code = "";
  for (let i = 0; i < props.codeLength; i++) {
    const i = randomNum(0, codeRandomList.value.length);
    code += codeRandomList.value[i];
  }
  verifyCode.value = code;
}

function drawText(ctx, txt, i) {
  // 繪製文字
  ctx.fillStyle = randomColor(props.textColorMin, props.textColorMax); //隨機生成字型顏色
  ctx.font =
    "bold " + randomNum(props.fontSizeMin, props.fontSizeMax) + "px Arial"; //隨機生成字型大小

  const x = i * ((props.contentWidth - 15) / props.codeLength) + 10;
  const y = randomNum(props.fontSizeMax, props.contentHeight);
  const deg = randomNum(props.textDegMin, props.textDegMax);

  // 修改座標原點和旋轉角度
  ctx.translate(x, y);
  ctx.rotate((deg * Math.PI) / 180);

  ctx.fillText(txt, 0, 0);

  // 恢復座標原點和旋轉角度
  ctx.rotate((-deg * Math.PI) / 180);
  ctx.translate(-x, -y);
}

function drawLine(ctx) {
  // 繪製干擾線
  for (let i = 0; i < props.lineCount; i++) {
    ctx.strokeStyle = randomColor(props.lineColorMin, props.lineColorMax);
    ctx.beginPath();
    ctx.moveTo(
      randomNum(0, props.contentWidth),
      randomNum(0, props.contentHeight)
    );
    ctx.lineTo(
      randomNum(0, props.contentWidth),
      randomNum(0, props.contentHeight)
    );
    ctx.stroke();
  }
}

function drawDot(ctx) {
  // 繪製干擾點
  for (let i = 0; i < props.dotCount; i++) {
    ctx.fillStyle = randomColor(props.dotColorMin, props.dotColorMax);
    ctx.beginPath();
    ctx.arc(
      randomNum(0, props.contentWidth),
      randomNum(0, props.contentHeight),
      1,
      0,
      2 * Math.PI
    );
    ctx.fill();
  }
}

function drawPic() {
  randomCode();
  let canvas = document.getElementById("id-canvas");
  let ctx = canvas.getContext("2d");
  ctx.textBaseline = "bottom";
  // 繪製背景
  ctx.fillStyle = randomColor(
    props.backgroundColorMin,
    props.backgroundColorMax
  );
  ctx.fillRect(0, 0, props.contentWidth, props.contentHeight);

  for (let i = 0; i < props.codeLength; i++) {
    drawText(ctx, verifyCode.value[i], i);
  }
  drawLine(ctx);
  drawDot(ctx);
}

onMounted(() => {
  drawPic();
});

function refresh() {
  drawPic();
}

function verify(input) {
  return input === verifyCode.value;
}

defineExpose({
  verify,
  refresh,
  verifyCode,
});
</script>

<template>
  <div style="cursor: pointer; user-select: none" @click="refresh">
    <canvas
      id="id-canvas"
      :width="contentWidth"
      :height="contentHeight"
    ></canvas>
  </div>
</template>

<script>
export default {
  name: "CaptchaImage",
};
</script>
