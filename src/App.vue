<template>
  <div class="content">
    <div class="site-title">色图检测工具 - bili CodeJack</div>
    <!-- 显示上传的图片 -->
    <div class="image-show-box">
      <img :src="uploadUrl" v-if="uploadUrl">
      <img src="./assets/ddd.png" v-else>
    </div>

    <div class="bot">
      <!-- elo 等级 -->
      <div class="elo-info">
        <img src="./assets/p.png" v-if="status_code == 0">
        <img src="./assets/no.png" v-if="status_code == 1">
        <img src="./assets/ok.png" v-if="status_code == 2">
      </div>

      <!-- 上传按钮 -->
      <div class="btns">
        <div class="infos">
          <ul>
            <li v-for="item in eloClasss">{{ item.class }}</li>
          </ul>
          <p :class="{ isElo: status_code == 1 }" v-if="!isLoding">{{
            status_code == 0 ? '请上传图片' :
              status_code == 1 ? '是色图捏' :
                '不是色图捏'
          }}</p>
          <p v-else>思考中..</p>
        </div>
        <button @click="uploadImageFn" :class="{ loading: isLoding }">
          {{ isLoding ? '别急 我在烧烤' : '上传图片' }}
        </button>
      </div>

    </div>

    <p class="tips">会人工审核的捏~</p>

    <input style="display: none;" type="file" accept="image/*" ref="uploadImageInputRef"
      @change="onUploadInputChangeFn">
  </div>
</template>

<script setup lang="ts">
import axios from 'axios'
import FormData from 'form-data'
import { ref } from 'vue'
const uploadUrl = ref('')
const status_code = ref<number | null>(0) // 0 请上传 1是色
const eloClasss = ref<Array<object | any>>([])
const uploadImageInputRef = ref<HTMLInputElement | null>(null)
const isLoding = ref(false)
const uploadImageFn = () => {
  if (isLoding.value) return
  // 上传图片
  uploadImageInputRef.value?.click()
}

const uploadImgae = async (file: File) => {
  status_code.value = 0
  isLoding.value = true
  eloClasss.value = []
  const form = new FormData()
  form.append('file', file)
  const { data: res } = await axios.post('https://elonoapi.codejack.net/predict', form)
  console.log(res);
  if (res.length > 0) {
    res.forEach((item: any) => {
      console.log(item);
      if (item.score > 0.4) {
        eloClasss.value.push(item)
      }
    });
    if (eloClasss.value.length > 0) {
      status_code.value = 1
    } else {
      status_code.value = 2
    }
  } else {
    status_code.value = 2
  }

  isLoding.value = false
}

const onUploadInputChangeFn = (e: Event) => {
  const target = e.target as HTMLInputElement
  const file = target.files?.[0]
  if (file) {
    uploadImgae(file)

    const reader = new FileReader()
    reader.onload = (e) => {
      uploadUrl.value = e.target?.result as string
    }
    reader.readAsDataURL(file)
  }
}


</script>

<style lang="scss" scoped>
.content {
  width: 100%;
  display: flex;
  gap: 50px;
  flex-direction: column;
  padding: 50px;

  @media (max-width: 600px) {
    gap: 20px;
    padding: 5px;
  }

  .image-show-box {
    width: 100%;
    max-height: 500px;

    text-align: center;

    img {
      width: 100%;
      height: 100%;
      max-height: 500px;
      object-fit: contain;
    }

    @media (max-width: 600px) {
      max-height: 300px;

      img {
        max-height: 300px;

      }
    }
  }

  .bot {
    width: 100%;
    display: flex;
    justify-content: center;
    gap: 50px;

    @media (max-width: 600px) {
      gap: 10px;
      flex-direction: column;
      align-items: center;
    }

    .elo-info {
      height: auto;

      img {
        height: 100%;
      }
    }

    .btns {
      display: flex;
      flex-direction: column;
      gap: 10px;
      justify-content: flex-end;

      button {
        border: none;
        width: 200px;
        height: 50px;
        background: none;
        border: 1px solid #9b9b9b;
        font-size: 20px;
        box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.2);
        transition: background-color 0.3s ease;
        cursor: pointer;

        &:hover {
          background-color: #eee;
        }

        &:focus {
          outline: none;
        }

        &.loading {
          background-color: #ccc;
          cursor: not-allowed;
          animation: rotate 1s linear infinite;

          @keyframes rotate {
            0% {
              transform: rotate(0deg);
            }

            100% {
              transform: rotate(360deg);
            }
          }
        }
      }

      .infos {
        display: flex;
        flex-direction: column;
        gap: 10px;

        ul {
          max-height: 200px;
          overflow: auto;

          &::-webkit-scrollbar {
            width: 5px;
          }

          &::-webkit-scrollbar-thumb {
            background: #ccc;
            border-radius: 0px;
          }

          display: flex;
          flex-direction: column;
          gap: 5px;

          li {
            list-style: none;
            user-select: text !important;
          }
        }

        p {
          color: #666;
          font-size: 20px;
          font-weight: 700;

          &.isElo {
            color: red !important;
          }
        }

      }
    }
  }

}


.tips {
  position: fixed;
  right: 5px;
  bottom: 5px;
  color: #66666675;
  font-size: 12px;
}

.site-title {
  position: fixed;
  left: 5px;
  top: 5px;
  z-index: 999;
  color: #666;
  font-size: 20px;
  text-shadow: 1px 1px 1px #fff, -1px -1px 1px #fff, 1px -1px 1px #fff, -1px 1px 1px #fff;
  user-select: text !important;
}
</style>
