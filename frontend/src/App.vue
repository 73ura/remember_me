<template>
  <div id="app" class="min-h-screen bg-gradient-to-br from-pink-50 to-purple-50">
    <header class="bg-white shadow-sm">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex justify-between items-center py-6">
          <h1 class="text-3xl font-bold text-gray-900">
            🌟 おぼえてて
          </h1>
          <p class="text-sm text-gray-600">
            子供とのおもいで記録アプリ
          </p>
        </div>
      </div>
    </header>

    <main class="max-w-7xl mx-auto py-6 sm:px-6 lg:px-8">
      <div class="px-4 py-6 sm:px-0">
        <div class="border-4 border-dashed border-gray-200 rounded-lg p-8 text-center">
          <h2 class="text-2xl font-semibold text-gray-700 mb-4">
            🎉 Vue.js + Tailwind CSS セットアップ完了！
          </h2>
          <p class="text-gray-600 mb-6">
            フロントエンドの基本構造が作成されました。
          </p>
          <div class="bg-green-100 border border-green-400 text-green-700 px-4 py-3 rounded">
            <p class="font-semibold">✅ セットアップ完了項目:</p>
            <ul class="text-left mt-2 space-y-1">
              <li>• Vue.js 3.x</li>
              <li>• Tailwind CSS</li>
              <li>• 基本的なプロジェクト構造</li>
              <li>• レスポンシブデザイン</li>
            </ul>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      message: 'Vue.js + Tailwind CSS で作成されたアプリです！'
    }
  }
}
</script>
