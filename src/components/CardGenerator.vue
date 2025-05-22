<template>
  <div class="container">
    <div class="form-panel">
      <el-form label-width="100px">
        <el-form-item label="卡片标题">
          <el-input v-model="title" placeholder="请输入卡片标题"></el-input>
        </el-form-item>
        <el-form-item label="词条数量">
          <el-radio-group v-model="wordCount">
            <el-radio :label="3">3个词条</el-radio>
            <el-radio :label="4">4个词条</el-radio>
          </el-radio-group>
        </el-form-item>

        <el-form-item 
          v-for="(item, index) in words"
          :key="index"
          :label="'词条' + (index+1)">
          <el-input v-model="words[index]"></el-input>
        </el-form-item>

        <el-button type="primary" @click="generateImage">生成图片</el-button>
      </el-form>
    </div>

    <div class="preview-panel" ref="previewPanel">
      <div class="placeholder">
        <div 
          v-if="title"
          class="word-item"
          :style="{ fontSize: '40px', textAlign: 'center', color: '#a992d6' }"
        >
          {{ title }}
        </div>
        <div 
          class="word-item"
          v-for="(item, index) in words"
          :key="index"
          :style="getWordStyle(index)">
          {{ item }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      title: '',
      wordCount: 3,
      words: ['', '', ''],
      bgUrl: ''
    }
  },
  watch: {
    wordCount(newVal) {
      this.words = Array(newVal).fill('')
    }
  },
  methods: {
    getWordStyle(index) {
      const positions = [
        {fontSize: '20px', textAlign:'center',  color: '#a992d6' },
        {fontSize: '20px', textAlign:'center',  color: '#a992d6' },
        {fontSize: '20px', textAlign:'center',  color: '#a992d6' },
        {fontSize: '20px', textAlign:'center',  color: '#a992d6' }
      ]
      return positions[index]
    },
    async generateImage() {
      const bgBlob = await fetch('/bg.jpg').then(r => r.blob())
      const bgUrl = URL.createObjectURL(bgBlob)
      
      await new Promise(resolve => {
        const img = new Image()
        img.onload = () => {
          this.$refs.previewPanel.style.backgroundImage = `url(${bgUrl})`
          resolve()
        }
        img.src = bgUrl
      })

      const html2canvas = (await import('html2canvas')).default
      html2canvas(this.$refs.previewPanel, {
        allowTaint: false,
        useCORS: true
      }).then(canvas => {
        const link = document.createElement('a')
        link.download = 'generated-card.png'
        link.href = canvas.toDataURL()
        link.click()
        URL.revokeObjectURL(bgUrl)
      })
    }
  }
}
</script>

<style scoped>
.container {
  display: flex;
  padding: 20px;
}
.form-panel {
  width: 300px;
  margin-right: 20px;
}
.preview-panel {
  position: relative;
  width: 600px;
  height: 800px;
  background-image: url('/bg.jpg');
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center;
}
.word-item {
  font-family: 'Microsoft YaHei';
  font-weight: bold;
  color: white;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
  align-items: center;
  flex: 1;
}
.placeholder {
  position: absolute;
  width: 100%;
  height: 200px;
  top: 430px;
  display: flex;
  flex-direction: column;
}
</style>
