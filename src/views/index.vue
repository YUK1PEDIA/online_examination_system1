<template>
  <div id="exam-system">
    <header class="header">
      <h1 class="title">在线考试系统</h1>
    </header>
    
    <main class="content">
      <section class="welcome">
        <h2 class="sub-title">欢迎</h2>
        <p class="description">该在线考试系统提供了一个方便的学习和评估环境，帮助您随时测试知识掌握程度。</p>
      </section>
      
      <section class="about">
        <h3 class="sub-title">关于在线考试系统</h3>
        <p class="description">我们致力于提供高质量的在线考试体验，涵盖Java、Python、C++三种科目，支持多种题型，以满足不同学习者的需求。</p>
      </section>
    </main>
    
    <footer class="footer">
      <p class="copyright">&copy; 2024 在线考试系统. All rights reserved.</p>
    </footer>
  </div>
</template>

<script>
export default {
  name: 'ExamSystemIndex',
};
</script>

<style scoped>
#exam-system {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  background-color: #f5f5f5; /* 更改为柔和的背景色 */
  color: #333;
  font-family: sans-serif;
}

.header {
  padding: 2em;
  text-align: center;
  animation: fadeInDown 1s ease-out;
}

.title {
  font-size: 3rem;
  letter-spacing: 2px;
  font-weight: bold;
}

.content {
  flex-grow: 1;
  padding: 2em;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.sub-title {
  font-size: 2rem;
  margin-bottom: 1em;
  font-weight: bold;
}

.description {
  font-size: 1.2rem;
  line-height: 1.6;
  opacity: 0.8;
}

.footer {
  padding: 1em;
  text-align: center;
  animation: fadeInUp 1s ease-in;
}

.copyright {
  font-size: 0.9rem;
  opacity: 0.5;
}

@keyframes fadeInDown {
  from {
    transform: translateY(-20px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}

@keyframes fadeInUp {
  from {
    transform: translateY(20px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}
</style>