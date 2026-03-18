<!-- Tianai Captcha 验证码组件 -->
<template>
  <ElDialog
    v-model="visible"
    :show-close="false"
    :close-on-click-modal="true"
    :close-on-press-escape="true"
    align-center
    width="auto"
    class="tianai-captcha-dialog"
  >
    <div :id="containerId" class="tianai-captcha-container"></div>
  </ElDialog>
</template>

<script setup lang="ts">
  defineOptions({ name: 'TianaiCaptcha' })

  declare const loadTAC: (
    resourceUrl: string | Record<string, any>,
    config: Record<string, any>,
    style?: Record<string, any>
  ) => Promise<any>

  interface Props {
    /** TAC 资源路径 */
    resourceUrl?: string
    /** 生成验证码接口 */
    genUrl?: string
    /** 校验验证码接口 */
    checkUrl?: string
  }

  const props = withDefaults(defineProps<Props>(), {
    resourceUrl: '/tac/',
    genUrl: '/api/captcha/gen',
    checkUrl: '/api/captcha/check'
  })

  const emit = defineEmits<{
    (e: 'success', response: any): void
    (e: 'fail', response: any): void
  }>()

  const containerId = `tianai-captcha-${Math.random().toString(36).slice(2, 9)}`
  const visible = ref(false)
  const tacInstance = ref<any>(null)

  /** 打开验证码弹窗 */
  const open = async () => {
    visible.value = true
    // 等待 dialog DOM 渲染完成后再初始化 TAC
    await nextTick()

    if (tacInstance.value) {
      tacInstance.value.init()
      return
    }

    const config = {
      bindEl: `#${containerId}`,
      requestCaptchaDataUrl: props.genUrl,
      validCaptchaUrl: props.checkUrl,
      validSuccess: (response: any, _captcha: any, tac: any) => {
        tac.destroyWindow()
        visible.value = false
        emit('success', response)
      },
      validFail: (_response: any, _captcha: any, tac: any) => {
        tac.reloadCaptcha()
        emit('fail', _response)
      },
      btnCloseFun: (_event: any, tac: any) => {
        tac.destroyWindow()
        visible.value = false
      }
    }

    const tac = await loadTAC(props.resourceUrl, config)
    tacInstance.value = tac
    tac.init()
  }

  // dialog 关闭时销毁验证码
  watch(visible, (val) => {
    if (!val && tacInstance.value) {
      tacInstance.value.destroyWindow()
    }
  })

  defineExpose({ open })
</script>

<style lang="scss">
  .tianai-captcha-dialog {
    &.el-dialog {
      padding: 0;
      border-radius: 5px !important;
      background: transparent;
      box-shadow: none;
    }

    .el-dialog__header {
      display: none;
    }

    .el-dialog__body {
      padding: 0 !important;
    }
  }

  .tianai-captcha-container {
    display: flex;
    align-items: center;
    justify-content: center;
  }
</style>
