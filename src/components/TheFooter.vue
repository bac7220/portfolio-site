<script setup>
// 送信ボタンが押された時の処理
const handleSubmit = async () => {
  // 入力値を取得
  const data = {
    name: document.getElementById('name').value,
    email: document.getElementById('email').value,
    message: document.getElementById('message').value
  };

  const url = "https://qt4qyo0728.execute-api.ap-northeast-1.amazonaws.com/send-mail";

  try {
    const response = await fetch(url, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data)
    });

    const result = await response.json();
    alert(result);
  } catch (error) {
    console.error('エラー:', error);
    alert('送信に失敗しました。');
  }
};
</script>

<template>
  <header>
    <nav>
      <RouterLink to="/">Home</RouterLink>
      <RouterLink to="/about">About</RouterLink>

      <form @submit.prevent="handleSubmit">
        <input type="text" id="name" placeholder="お名前" required>
        <input type="email" id="email" placeholder="メールアドレス" required>
        <textarea id="message" placeholder="お問い合わせ内容" required></textarea>
        <button type="submit">送信する</button>
      </form>
    </nav>
  </header>
</template>