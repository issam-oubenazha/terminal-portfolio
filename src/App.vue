<script setup>
import { computed, nextTick, ref } from 'vue'

const input = ref('')
const output = ref([])
const terminal = ref(null)

const projects = [
  { name: 'pc-presence-lock', description: 'Computer presence detection and automatic Windows locking.', tech: 'Python • OpenCV • YOLO', url: 'https://github.com/issam-oubenazha/pc-presence-lock' },
  { name: '9biya-store', description: 'Modern clothing e-commerce interface built with Vue.', tech: 'Vue • JavaScript • CSS', url: 'https://github.com/issam-oubenazha/9biya-store' }
]

const commands = ['help', 'ls', 'cd about', 'cd projects', 'cd skills', 'cd contact', 'whoami', 'github', 'clear']

const welcome = [
  { type: 'text', value: 'Welcome to Issam Oubenazha\'s portfolio.' },
  { type: 'text', value: 'Type "help" to see available commands.' },
  { type: 'text', value: '' }
]

output.value = welcome

const prompt = computed(() => 'issam@portfolio:' + (currentPath.value === '~' ? '~' : currentPath.value) + '$')

const currentPath = ref('~')

function add(type, value) {
  output.value.push({ type, value })
}

function runCommand(raw) {
  const cmd = raw.trim()
  if (!cmd) return

  add('command', `${prompt.value} ${cmd}`)

  if (cmd === 'clear') {
    output.value = []
    return
  }

  if (cmd === 'help') {
    add('text', 'Available commands:')
    add('text', '  ls              List portfolio sections')
    add('text', '  cd about        About me')
    add('text', '  cd projects     My GitHub projects')
    add('text', '  cd skills       Technologies & skills')
    add('text', '  cd contact      Contact information')
    add('text', '  whoami          Show my identity')
    add('text', '  github          Open my GitHub profile')
    add('text', '  clear           Clear terminal')
  } else if (cmd === 'ls') {
    add('text', 'about/   projects/   skills/   contact/')
  } else if (cmd === 'whoami') {
    add('text', 'Issam Oubenazha — Full Stack Developer | Data')
  } else if (cmd === 'github') {
    window.open('https://github.com/issam-oubenazha', '_blank')
    add('text', 'Opening GitHub...')
  } else if (cmd === 'cd about') {
    currentPath.value = '~/about'
    add('title', 'ABOUT ME')
    add('text', 'I am Issam Oubenazha, a Full Stack Developer focused on')
    add('text', 'Data Engineering, AI and practical digital products.')
    add('text', '')
    add('text', 'I enjoy building useful applications and turning ideas into')
    add('text', 'clean, functional and deployable projects.')
  } else if (cmd === 'cd projects') {
    currentPath.value = '~/projects'
    add('title', 'PROJECTS')
    projects.forEach((p, i) => {
      add('text', `${String(i + 1).padStart(2, '0')}  ${p.name}`)
      add('text', `    ${p.description}`)
      add('text', `    ${p.tech}`)
      add('link', p.url)
      add('text', '')
    })
  } else if (cmd === 'cd skills') {
    currentPath.value = '~/skills'
    add('title', 'SKILLS')
    add('text', 'Languages      Python • JavaScript • SQL')
    add('text', 'Frontend       Vue • HTML • CSS')
    add('text', 'Data           Pandas • ETL • Data Pipelines')
    add('text', 'Cloud          AWS Lambda • API Gateway • S3')
    add('text', 'Tools          Git • GitHub • VS Code')
  } else if (cmd === 'cd contact') {
    currentPath.value = '~/contact'
    add('title', 'CONTACT')
    add('text', 'GitHub     github.com/issam-oubenazha')
    add('text', 'Available for opportunities, collaborations and projects.')
  } else if (cmd === 'cd ..' || cmd === 'cd ~') {
    currentPath.value = '~'
    add('text', 'Returned to home.')
  } else {
    add('error', `command not found: ${cmd}`)
    add('text', 'Type "help" for available commands.')
  }

  nextTick(() => terminal.value?.scrollTo({ top: terminal.value.scrollHeight, behavior: 'smooth' }))
}

function submit() {
  runCommand(input.value)
  input.value = ''
}

function autocomplete() {
  const value = input.value.trim()
  const match = commands.find(c => c.startsWith(value) && c !== value)
  if (match) input.value = match
}
</script>

<template>
  <main class="page">
    <section class="terminal">
      <header class="bar">
        <div class="dots"><span></span><span></span><span></span></div>
        <div class="title">issam@portfolio: ~</div>
        <div class="status">● online</div>
      </header>

      <div ref="terminal" class="screen" @click="$refs.commandInput?.focus()">
        <div v-for="(line, i) in output" :key="i" :class="['line', line.type]">
          <template v-if="line.type === 'link'">
            <a :href="line.value" target="_blank">{{ line.value }}</a>
          </template>
          <template v-else>{{ line.value }}</template>
        </div>

        <form class="input-line" @submit.prevent="submit">
          <span class="prompt">{{ prompt }}</span>
          <input
            ref="commandInput"
            v-model="input"
            autofocus
            autocomplete="off"
            spellcheck="false"
            @keydown.tab.prevent="autocomplete"
          />
          <span class="cursor"></span>
        </form>
      </div>

      <footer>
        <span>Tip: use <b>Tab</b> for autocomplete</span>
        <span>Vue + Vite</span>
      </footer>
    </section>
  </main>
</template>