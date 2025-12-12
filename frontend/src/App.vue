<script setup>
import { ref, computed } from 'vue'

// リアクティブなデータ
const participantsInput = ref('')
const errorMessage = ref('')
const shuffledGroups = ref([])
const showResults = ref(false)

// ランチの行き先候補
const restaurantOptions = [
  '中華料理',
  'イタリアン',
  '和食',
  'カレー',
  'ラーメン',
  'ハンバーガー',
  '寿司',
  'タイ料理',
  'フレンチ',
  'カフェ',
  '焼肉',
  'うどん・そば',
  'パスタ',
  '定食',
  'お弁当'
]

// トークテーマ候補
const talkTopics = [
  '最近見たおすすめ映画・ドラマ',
  '今年やってみたいこと',
  '好きな旅行先・行ってみたい場所',
  '最近読んだ本・おすすめの本',
  '趣味について',
  'おすすめのアプリ・サービス',
  '好きな音楽・アーティスト',
  '週末の過ごし方',
  '好きな食べ物・お店',
  'リモートワークの工夫',
  '最近学んだこと',
  'おすすめのYouTubeチャンネル'
]

// 参加者リストの計算プロパティ
const participantList = computed(() => {
  if (!participantsInput.value.trim()) return []
  
  // 改行区切りまたはカンマ区切りで分割
  let participants = participantsInput.value
    .split(/[,\n]/)
    .map(name => name.trim())
    .filter(name => name.length > 0)
  
  // 重複を除去
  return [...new Set(participants)]
})

// 配列をシャッフルする関数
const shuffleArray = (array) => {
  const shuffled = [...array]
  for (let i = shuffled.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1))
    ;[shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]]
  }
  return shuffled
}

// グループ分けアルゴリズム
const createGroups = (participants) => {
  const n = participants.length
  
  // 5人の場合の特例処理
  if (n === 5) {
    return [participants] // 5人1組
  }
  
  // 最適なグループ構成を計算
  let groups = []
  let remaining = n
  
  // 4人グループを優先的に作る
  while (remaining >= 7) {
    groups.push(4)
    remaining -= 4
  }
  
  // 残りを処理
  if (remaining === 6) {
    groups.push(3, 3)
  } else if (remaining === 5) {
    groups.push(5) // 特例
  } else if (remaining === 4) {
    groups.push(4)
  } else if (remaining === 3) {
    groups.push(3)
  }
  
  // 実際にグループを作成
  const shuffled = shuffleArray(participants)
  const result = []
  let index = 0
  
  groups.forEach((size, groupIndex) => {
    const group = {
      id: groupIndex + 1,
      name: `Team ${String.fromCharCode(65 + groupIndex)}`, // Team A, Team B, ...
      members: shuffled.slice(index, index + size),
      restaurant: restaurantOptions[Math.floor(Math.random() * restaurantOptions.length)],
      talkTopic: talkTopics[Math.floor(Math.random() * talkTopics.length)]
    }
    result.push(group)
    index += size
  })
  
  return result
}

// シャッフル実行メソッド
const shuffleGroups = () => {
  errorMessage.value = ''
  showResults.value = false
  
  if (participantList.value.length < 3) {
    errorMessage.value = '参加者は3人以上必要です。'
    return
  }
  
  try {
    shuffledGroups.value = createGroups(participantList.value)
    showResults.value = true
  } catch (error) {
    errorMessage.value = 'グループ作成中にエラーが発生しました。'
  }
}

// 結果をクリップボードにコピー
const copyResults = () => {
  const text = shuffledGroups.value.map(group => 
    `**${group.name}**\n` +
    `メンバー:\n${group.members.map(member => `  • ${member}`).join('\n')}\n` +
    `ランチ先: ${group.restaurant}\n` +
    `トークテーマ: ${group.talkTopic}\n`
  ).join('\n')
  
  navigator.clipboard.writeText(text).then(() => {
    alert('結果をクリップボードにコピーしました！')
  }).catch(() => {
    alert('コピーに失敗しました。')
  })
}

// リセット
const reset = () => {
  showResults.value = false
  shuffledGroups.value = []
  errorMessage.value = ''
}
</script>

<template>
  <div :class="$style.container">
    <h1 :class="$style.title">🍽️ Shuffle Lunch App</h1>
    
    <div v-if="!showResults" :class="$style.inputSection">
      <label for="participants" :class="$style.label">参加者リスト</label>
      <textarea 
        id="participants"
        v-model="participantsInput"
        :class="$style.textarea"
        placeholder="参加者の名前を入力してください&#10;例：&#10;田中太郎&#10;佐藤花子&#10;山田次郎&#10;&#10;または&#10;田中太郎, 佐藤花子, 山田次郎"
      ></textarea>
      <div :class="$style.helpText">
        改行区切りまたはカンマ区切りで名前を入力してください
      </div>
      <div :class="$style.participantCount" v-if="participantList.length > 0">
        参加者数: {{ participantList.length }}名
      </div>
      
      <div :class="$style.buttonSection">
        <button 
          @click="shuffleGroups"
          :disabled="participantList.length < 3"
          :class="$style.primaryButton"
        >
          🎲 シャッフル実行
        </button>
      </div>
    </div>

    <!-- 結果表示 -->
    <div v-if="showResults" :class="$style.resultsSection">
      <h2 :class="$style.resultsTitle">🎉 グループ分け結果</h2>
      
      <div :class="$style.groupsContainer">
        <div 
          v-for="group in shuffledGroups" 
          :key="group.id"
          :class="$style.groupCard"
        >
          <h3 :class="$style.groupName">{{ group.name }}</h3>
          <div :class="$style.members">
            <strong>メンバー:</strong>
            <ul :class="$style.memberList">
              <li v-for="member in group.members" :key="member" :class="$style.memberItem">
                {{ member }}
              </li>
            </ul>
          </div>
          <div :class="$style.restaurant">
            <strong>🍽️ ランチ先:</strong> {{ group.restaurant }}
          </div>
          <div :class="$style.talkTopic">
            <strong>💬 トークテーマ:</strong> {{ group.talkTopic }}
          </div>
        </div>
      </div>
      
      <div :class="$style.actionButtons">
        <button @click="copyResults" :class="$style.copyButton">
          📋 結果をコピー
        </button>
        <button @click="shuffleGroups" :class="$style.reshuffleButton">
          🔄 再シャッフル
        </button>
        <button @click="reset" :class="$style.resetButton">
          ← 戻る
        </button>
      </div>
    </div>

    <div v-if="errorMessage" :class="$style.errorMessage">
      {{ errorMessage }}
    </div>
  </div>
</template>

<style module>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 30px;
  background: white;
  border-radius: 10px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

.title {
  color: #333;
  text-align: center;
  margin-bottom: 30px;
  font-size: 2.5rem;
}

.inputSection {
  margin-bottom: 30px;
}

.label {
  display: block;
  margin-bottom: 10px;
  font-weight: bold;
  color: #555;
  font-size: 1.1rem;
}

.textarea {
  width: 100%;
  min-height: 150px;
  padding: 15px;
  border: 2px solid #ddd;
  border-radius: 8px;
  font-size: 16px;
  font-family: inherit;
  resize: vertical;
  box-sizing: border-box;
}

.textarea:focus {
  outline: none;
  border-color: #4CAF50;
}

.helpText {
  margin-top: 5px;
  color: #666;
  font-size: 14px;
}

.participantCount {
  text-align: center;
  margin: 10px 0;
  font-size: 14px;
  color: #666;
  font-weight: bold;
}

.buttonSection {
  text-align: center;
  margin: 30px 0;
}

.primaryButton {
  background-color: #4CAF50;
  color: white;
  padding: 15px 30px;
  border: none;
  border-radius: 8px;
  font-size: 18px;
  cursor: pointer;
  transition: background-color 0.3s;
  font-weight: bold;
}

.primaryButton:hover {
  background-color: #45a049;
}

.primaryButton:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.errorMessage {
  background-color: #ffebee;
  color: #c62828;
  padding: 15px;
  border-radius: 8px;
  margin-top: 20px;
  border-left: 4px solid #c62828;
  font-weight: bold;
}

/* 結果表示のスタイル */
.resultsSection {
  margin-top: 20px;
}

.resultsTitle {
  text-align: center;
  color: #4CAF50;
  margin-bottom: 30px;
  font-size: 2rem;
}

.groupsContainer {
  display: grid;
  grid-template-columns: 1fr;
  gap: 20px;
  margin-bottom: 30px;
}

.groupCard {
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
  border: 2px solid #dee2e6;
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
  transition: transform 0.2s, box-shadow 0.2s;
}

.groupCard:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 16px rgba(0,0,0,0.15);
}

.groupName {
  color: #495057;
  margin: 0 0 15px 0;
  font-size: 1.4rem;
  border-bottom: 2px solid #4CAF50;
  padding-bottom: 5px;
}

.members, .restaurant, .talkTopic {
  margin: 12px 0;
  line-height: 1.6;
}

.memberList {
  margin: 8px 0 0 0;
  padding: 0;
  list-style: none;
}

.memberItem {
  color: #495057;
  font-weight: normal;
  padding: 4px 0;
}

.restaurant {
  color: #e91e63;
  font-size: 1.1rem;
}

.talkTopic {
  color: #2196F3;
  font-style: italic;
}

.actionButtons {
  display: flex;
  gap: 15px;
  justify-content: center;
  flex-wrap: wrap;
}

.copyButton, .reshuffleButton, .resetButton {
  padding: 12px 24px;
  border: none;
  border-radius: 8px;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s;
}

.copyButton {
  background-color: #17a2b8;
  color: white;
}

.copyButton:hover {
  background-color: #138496;
}

.reshuffleButton {
  background-color: #ffc107;
  color: #212529;
}

.reshuffleButton:hover {
  background-color: #e0a800;
}

.resetButton {
  background-color: #6c757d;
  color: white;
}

.resetButton:hover {
  background-color: #5a6268;
}

/* レスポンシブ対応 */
/* タブレット対応 */
@media (min-width: 768px) {
  .groupsContainer {
    grid-template-columns: repeat(2, 1fr);
    gap: 24px;
  }
}

/* デスクトップ対応（3列表示） */
@media (min-width: 1200px) {
  .container {
    max-width: 1200px;
    padding: 40px;
  }

  .groupsContainer {
    grid-template-columns: repeat(3, 1fr);
    gap: 30px;
  }
}

/* 大型デスクトップ対応 */
@media (min-width: 1600px) {
  .container {
    max-width: 1400px;
  }
}
</style>
