<template>
    <div>
      <h2>在线测试</h2>
      <div v-for="(question, index) in questions" :key="index">
        <h3>{{ question.title }}</h3>
        <p v-if="question.type === 'single'">
          单选题:
          <label v-for="(option, optIndex) in question.options" :key="optIndex">
            <input type="radio" :name="'q'+index" :value="option.value" v-model="answers[index]">
            {{ option.text }}
          </label>
        </p>
        <p v-else-if="question.type === 'multiple'">
          多选题:
          <label v-for="(option, optIndex) in question.options" :key="optIndex">
            <input type="checkbox" :name="'q'+index" :value="option.value" v-model="answers[index]">
            {{ option.text }}
          </label>
        </p>
        <p v-else-if="question.type === 'boolean'">
          判断题:
          <label>
            <input type="radio" :name="'q'+index" value="true" v-model="answers[index]">
            正确
          </label>
          <label>
            <input type="radio" :name="'q'+index" value="false" v-model="answers[index]">
            错误
          </label>
        </p>
      </div>
  
      <button @click="checkAnswers">提交答案</button>
  
      <div v-if="showResults">
        <h2>结果</h2>
        <p>您的得分是：{{ score }} / {{ questions.length }}</p>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        questions: [
          {
            title: '这是一个单选题',
            type: 'single',
            options: [
              { text: '选项 A', value: 'A' },
              { text: '选项 B', value: 'B' },
              { text: '选项 C', value: 'C' }
            ],
            correct: 'B'
          },
          {
            title: '这是一个多选题',
            type: 'multiple',
            options: [
              { text: '选项 X', value: 'X' },
              { text: '选项 Y', value: 'Y' },
              { text: '选项 Z', value: 'Z' }
            ],
            correct: ['X', 'Y']
          },
          {
            title: '这是一个判断题',
            type: 'boolean',
            correct: true
          }
        ],
        answers: [],
        score: 0,
        showResults: false
      };
    },
    methods: {
      checkAnswers() {
        this.score = 0;
        this.questions.forEach((question, index) => {
          if (question.type === 'single') {
            if (this.answers[index] === question.correct) {
              this.score++;
            }
          } else if (question.type === 'multiple') {
            if (this.answers[index].sort().join(',') === question.correct.sort().join(',')) {
              this.score++;
            }
          } else if (question.type === 'boolean') {
            if (this.answers[index] === String(question.correct)) {
              this.score++;
            }
          }
        });
        this.showResults = true;
      }
    }
  };
  </script>