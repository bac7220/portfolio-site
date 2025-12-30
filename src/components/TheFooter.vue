<script setup>
import { reactive, ref, computed, onMounted, watch } from 'vue';
import { client } from "../lib/microcms.js"

const formState = reactive({
  name: '',
  email: '',
  consultationType: 'outsorcing',
  message: '',
  timing: ''
})

const isSubmitting = ref(false);
const consultationTypes = [
  { value: 'outsorcing', label: 'コーディング外注の相談(新規・リニューアル)' },
  { value: 'exsting_help', label: '既存案件の修正' },
  { value: 'spot', label: 'スポット対応・部分的な依頼' },
  { value: 'other', label: 'その他' }
]

const selectedConsultationLabel = computed(() => {
  const type = consultationTypes.find(t => t.value === formState.consultationType)
  return type ? type.label : formState.consultationType
})

const handleSubmit = async () => {
  if (isSubmitting.value) return
  isSubmitting.value = true

  // AWSAPIURL
  const url = "https://qt4qyo0728.execute-api.ap-northeast-1.amazonaws.com/send-mail";

  const formattedMessage = `
【ご相談内容の種類】
${selectedConsultationLabel.value}

【着手希望時期・納期】
${formState.timing || '特になし'}

【お問い合わせ内容詳細】
${formState.message}
`.trim()
  const sendData = {
    name: formState.name,
    email: formState.email,
    message: formattedMessage,
  }
  try {
    const response = await fetch(url, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(sendData)
    })
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }


    const result = await response.text();
    alert('お問い合わせありがとうございます');
  } catch (error) {
    console.error('エラー:', error);
    alert('送信に失敗しました。しばらく経ってから再度お試しください。');
  } finally {
    isSubmitting.value = false
  }
}

// プライバシーポリシーの表示
const isModalOpen = ref(false)
const privacy = ref(null)
onMounted(async () => {
  const data = await client.get({
    endpoint: "privacy",
  })
  privacy.value = data;
  // console.log(data)
});

watch(isModalOpen, (newVal) => {
  if (newVal) {
    document.body.style.overflow = 'hidden';
  } else {
    document.body.style.overflow = '';
  }
})

</script>

<template>
  <footer class="footer">
    <nav class="footer-nav">
      <RouterLink to="/">Home</RouterLink>
      <RouterLink to="/about">About</RouterLink>
      <RouterLink to="/works">Works</RouterLink>
    </nav>
    <div id="footer-contact">
      <div class="contact-container">

        <div class="contact-head">
          <h2 class="contact-title">お問い合わせ</h2>
          <p class="contact-description">コーディング、サイト制作に関わるご相談、お見積りなど、お気軽にお問い合わせください。<br>１営業日以内にご返信いたします。</p>
        </div>
        <form @submit.prevent="handleSubmit">
          <div class="form-group">
            <label for="name" class="form-label">お名前/会社名 *</label>
            <input type="text" id="name" v-model="formState.name" class="form-input" placeholder="お名前/株式会社◯◯" required>
          </div>
          <div class="form-group">
            <label for="email" class="form-label">メールアドレス *</label>
            <input type="email" id="email" v-model="formState.email" class="form-input" placeholder="info@example.com"
              required>
          </div>
          <div class="form-group">
            <label class="form-label">ご相談内容の種類 *</label>
            <div class="radio-group">
              <label v-for="type in consultationTypes" :key="type.value" class="radio-label">
                <input type="radio" name="consultationTypes" :value="type.value" v-model="formState.consultationType"
                  required>
                <span class="radio-text">{{ type.label }}</span>
              </label>
            </div>
          </div>
          <div class="form-group">
            <label for="message" class="form-label">お問い合わせ詳細*</label>
            <textarea id="message" v-model="formState.message" class="form-textarea" rows="6" placeholder="お問い合わせ内容"
              required></textarea>
          </div>
          <div class="form-privacy">

            <p class="privacy-text">お問い合わせの際は、
              <button type="button" @click="isModalOpen = !isModalOpen" class="privacy-link">
                プライバシーポリシー
              </button>をご確認ください。
              <Transition name="fade">
                <div v-if="isModalOpen" @click="isModalOpen = false" class="overlay"></div>
              </Transition>
              <transition name="privacyModal">
                <div v-if="isModalOpen" class="modal-container">
                  <div v-if="privacy" v-html="privacy.content">
                  </div>
                </div>
              </transition>
            </p>
          </div>
          <div class="form-submit">
            <button type="submit" class="btn-submit" :disabled="isSubmitting">{{ isSubmitting ? '送信中...' : 'この内容で相談を送る'
              }}</button>
          </div>
        </form>
      </div>
    </div>
  </footer>
</template>

<style scoped>
.footer {
  background: var(--main-color);
  color: #FFF;
  padding-block: 60px;
}

/* --- フッターエリア設定 --- */
#contact-footer {
  background-color: #fff;
  padding: 60px 20px;
  border-top: 1px solid #eaeaea;
  line-height: 1.6;
}

.footer-nav {
  display: flex;
  gap: 16px;
  justify-content: center;
  margin-bottom: 32px;
}

.footer-nav a {
  font-size: 24px;
  font-weight: 700;
  position: relative;
}


.router-link-active:before {
  content: "*";
  color: rgb(217, 26, 26);
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  top: -30%;
}

.contact-container {
  
  max-width: 720px;
  padding: 20px;
  margin: 0 auto;
}

.contact-head {
  /* text-align: center; */
  margin-bottom: 48px;
}

.contact-title {
  font-size: 28px;
  font-weight: 700;
  margin-bottom: 16px;
  letter-spacing: 0.05em;
}

.contact-description {
  font-size: 15px;
}

/* --- フォームレイアウト --- */
.form-group {
  /* 余白を活かした配置 */
  margin-bottom: 36px;
}

.form-label {
  display: block;
  font-weight: 700;
  margin-bottom: 12px;
  font-size: 15px;
  display: flex;
  align-items: center;
}

input,
textarea {
  color: #333;
}


.badge-optional {
  background-color: #9e9e9e;
  color: #fff;
}

/* --- 入力フィールドスタイル --- */
.form-input,
.form-textarea {
  width: 100%;
  padding: 16px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 16px;
  background-color: #f9f9f9;
  transition: all 0.3s ease;
  -webkit-appearance: none;
  appearance: none;
}

.form-input::placeholder,
.form-textarea::placeholder {
  color: #bbb;
}

.form-input:focus,
.form-textarea:focus {
  outline: none;
  border-color: #0D47A1;
  background-color: #fff;
  box-shadow: 0 0 0 3px rgba(13, 71, 161, 0.1);
}

/* --- ラジオボタン --- */
.radio-group {
  display: flex;
  flex-direction: column;
  /* スマホファーストで縦積み */
  gap: 14px;
}

.radio-label {
  display: flex;
  align-items: center;
  cursor: pointer;
}

/* ラジオボタン自体のアクセントカラー */
.radio-label input[type="radio"] {
  accent-color: #0D47A1;
  transform: scale(1.2);
  margin-right: 12px;
  cursor: pointer;
}

.radio-text {
  font-size: 16px;
}

/* --- プライバシーポリシー --- */
.form-privacy {
  text-align: center;
  font-size: 14px;
  margin-bottom: 32px;
}

.form-privacy a {
  text-decoration: underline;
}

/* --- 送信ボタン --- */
.form-submit {
  text-align: center;
}

.btn-submit {
  display: inline-block;
  /* メインのアクセントカラー */
  background-color: #0D47A1;
  color: #fff;
  font-size: 18px;
  font-weight: 700;
  padding: 18px 60px;
  border: none;
  border-radius: 50px;
  /* 親しみやすい丸み */
  cursor: pointer;
  transition: all 0.3s ease;
  width: 100%;
  max-width: 360px;
  /* PCで広がりすぎないように */
  box-shadow: 0 4px 12px rgba(13, 71, 161, 0.2);
}

/* ホバー時（非送信中） */
.btn-submit:hover:not(:disabled) {
  background-color: #0a357a;
  transform: translateY(-2px);
  box-shadow: 0 6px 16px rgba(13, 71, 161, 0.3);
}

/* クリック時（非送信中） */
.btn-submit:active:not(:disabled) {
  transform: translateY(0);
  box-shadow: none;
}

/* 送信中のスタイル */
.btn-submit:disabled {
  background-color: #78909c;
  cursor: not-allowed;
  box-shadow: none;
}

/* --- レスポンシブ調整 (PC向け) --- */
@media (min-width: 768px) {
  #contact-footer {
    padding: 80px 0;
  }

  .contact-title {
    font-size: 32px;
  }

  .contact-description {
    font-size: 16px;
  }

  /* PCではラジオボタンを横並びにする場合（お好みでコメント解除） */
  /* .radio-group {
    flex-direction: row;
    flex-wrap: wrap;
    gap: 20px;
  }
  .radio-label {
    margin-right: 16px;
  }
  */
}

/* プライバシーポリシーのモーダル */
/* 背景 */
.overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.6);
  z-index: 1;
}

.modal-container {
  position: fixed;
  background-color: #fdfdfd;
  max-width: 1000px;
  width: 80vw;
  height: 80vh;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 2;
  padding: 30px 20px;
  overflow-y: auto;
  border-radius: 8px;
}

.modal-container :deep(p) {
  color: #333;
  line-height: 1.8;
  text-align: left;
}
</style>