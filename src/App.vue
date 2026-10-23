<script setup>
import { ref, reactive, computed, onMounted, onBeforeUnmount } from 'vue'
import './index.css'

const menuOpen = ref(false)

const skills = [
  { name: 'Vue 3', category: 'Frontend', level: 90 },
  { name: 'JavaScript', category: 'Frontend', level: 88 },
  { name: 'HTML & CSS', category: 'Frontend', level: 95 },
  { name: 'Responsive design', category: 'Frontend', level: 92 },
  { name: 'Git & GitHub', category: 'Tools', level: 82 },
  { name: 'Communication', category: 'Soft', level: 90 },
  { name: 'Teamwork', category: 'Soft', level: 94 },
]

const stats = [
  { value: 3, suffix: '+', label: 'Years learning' },
  { value: 2, suffix: '', label: 'Projects built' },
  { value: 4, suffix: '', label: 'Certificates' },
  { value: 100, suffix: '%', label: 'Pink energy' },
]

const education = [
  { years: '2023 – Present', title: 'BS in Computer Engineering', school: 'Cagayan State University-Carig Campus · Tuguegarao City' },
  { years: '2021 – 2023', title: 'Senior High School, STEM', school: 'Tumauini Stand-Alone Senior High School' },
  { years: '2016 – 2021', title: 'Junior High School', school: 'Tumauini National High School' },
]

const certifications = [
  { icon: '🌸', title: 'Regional ITE Summit 2026', year: 2026, details: 'Learned how current trends in information technology education and industry are shaping skills and career paths.' },
  { icon: '🎀', title: 'Regional Convention 2025', year: 2025, details: 'Participated in talks and workshops on practical applications of technology across industries.' },
  { icon: '💗', title: 'International Conference on Engineering the Intelligent Futre', year: 2026, details: 'Gained an overview of advances in artificial intelligence, automation, and intelligent systems.' },
  { icon: '🌷', title: 'Frontiers of Computer Engineering 2025', year: 2025, details: 'Explored cutting-edge topics in computer engineering such as hardware-software integration, embedded systems.' },
]

const sections = ['home', 'about', 'skills', 'education', 'certifications', 'contacts']
const categories = ['All', 'Frontend', 'Tools', 'Soft']
const selectedCategory = ref('All')
const activeSection = ref('home')
const scrollProgress = ref(0)
const mouseX = ref(300)
const mouseY = ref(300)
const flippedCard = ref(null)
const year = new Date().getFullYear()

const filteredSkills = computed(() =>
  selectedCategory.value === 'All'
    ? skills
    : skills.filter((s) => s.category === selectedCategory.value),
)

// light / dark theme
const isDark = ref(matchMedia('(prefers-color-scheme: dark)').matches)
const toggleTheme = () => {
  isDark.value = !isDark.value
  document.documentElement.dataset.theme = isDark.value ? 'dark' : 'light'
}

// typewriter
const roles = ['Creative', 'Friendly', 'Inclusive', 'lifelong learner']
const typedText = ref('')
let roleIndex = 0
let charIndex = 0
let deleting = false
let typeTimer
const typeTick = () => {
  const word = roles[roleIndex]
  typedText.value = word.slice(0, charIndex)
  if (!deleting && charIndex === word.length) {
    deleting = true
    typeTimer = setTimeout(typeTick, 1400)
    return
  }
  if (deleting && charIndex === 0) {
    deleting = false
    roleIndex = (roleIndex + 1) % roles.length
  }
  charIndex += deleting ? -1 : 1
  typeTimer = setTimeout(typeTick, deleting ? 40 : 90)
}

// contact form
const email = 'hello@yourname.com'
const form = reactive({ name: '', email: '', message: '' })
const errors = reactive({ name: '', email: '', message: '' })
const messageSent = ref(false)
const toast = ref('')
let toastTimer
const showToast = (text) => {
  toast.value = text
  clearTimeout(toastTimer)
  toastTimer = setTimeout(() => (toast.value = ''), 2200)
}
const copyEmail = async () => {
  try {
    await navigator.clipboard.writeText(email)
    showToast('Email copied')
  } catch {
    showToast(email)
  }
}

// petal burst
const burst = (e) => {
  for (let i = 0; i < 18; i++) {
    const petal = document.createElement('i')
    petal.className = 'petal'
    const angle = Math.random() * Math.PI * 2
    const distance = 60 + Math.random() * 110
    petal.style.cssText = `left:${e.clientX}px;top:${e.clientY}px;--x:${Math.cos(angle) * distance}px;--y:${Math.sin(angle) * distance + 60}px;--r:${Math.random() * 540}deg;opacity:${0.6 + Math.random() * 0.4}`
    document.body.appendChild(petal)
    setTimeout(() => petal.remove(), 1400)
  }
}

const sendMessage = () => {
  errors.name = form.name ? '' : 'Enter your name.'
  errors.email = /^\S+@\S+\.\S+$/.test(form.email) ? '' : 'Enter a valid email address.'
  errors.message = form.message.length >= 10 ? '' : 'Write at least 10 characters.'
  if (!errors.name && !errors.email && !errors.message) {
    messageSent.value = true
    burst({ clientX: innerWidth / 2, clientY: innerHeight / 2 })
  }
}

// cursor glow, scroll progress, active section, reveal on scroll
const onPointerMove = (e) => {
  mouseX.value = e.clientX
  mouseY.value = e.clientY
}
const onScroll = () => {
  const max = document.documentElement.scrollHeight - innerHeight
  scrollProgress.value = max > 0 ? (scrollY / max) * 100 : 0
}
let sectionObserver
let revealObserver
onMounted(() => {
  typeTick()
  addEventListener('pointermove', onPointerMove)
  addEventListener('scroll', onScroll, { passive: true })

  sectionObserver = new IntersectionObserver(
    (entries) => entries.forEach((e) => { if (e.isIntersecting) activeSection.value = e.target.id }),
    { rootMargin: '-45% 0px -50% 0px' },
  )
  sections.forEach((id) => {
    const el = document.getElementById(id)
    if (el) sectionObserver.observe(el)
  })

  revealObserver = new IntersectionObserver(
    (entries) => entries.forEach((e) => { if (e.isIntersecting) e.target.classList.add('shown') }),
    { threshold: 0.2 },
  )
  document.querySelectorAll('[data-reveal]').forEach((el) => revealObserver.observe(el))
})
onBeforeUnmount(() => {
  clearTimeout(typeTimer)
  clearTimeout(toastTimer)
  removeEventListener('pointermove', onPointerMove)
  removeEventListener('scroll', onScroll)
  sectionObserver?.disconnect()
  revealObserver?.disconnect()
})
</script>

<template>
  <div>
    <div class="glow" :style="{ left: mouseX + 'px', top: mouseY + 'px' }" aria-hidden="true"></div>

    <header>
      <div class="bar">
        <a class="logo" href="#home">Rizza S. Mengolio</a>
        <nav :class="{ open: menuOpen }" aria-label="Main">
          <a
            v-for="s in sections"
            :key="s"
            :href="'#' + s"
            :class="{ on: activeSection === s }"
            @click="menuOpen = false"
          >{{ s }}</a>
        </nav>
        <button class="icon" @click="toggleTheme" :aria-label="isDark ? 'Switch to light mode' : 'Switch to dark mode'">
          {{ isDark ? '☀' : '☾' }}
        </button>
        <button class="icon burger" @click="menuOpen = !menuOpen" aria-label="Toggle menu">☰</button>
      </div>
      <div class="progress" :style="{ width: scrollProgress + '%' }"></div>
    </header>

    <main>
      <!-- HOME -->
      <section id="home">
        <div>
          <p class="hi">Hello, I’m</p>
          <h1 aria-label="Your Name">
            <span v-for="(c, i) in 'Rizza M.'.split('')" :key="i" :style="{ animationDelay: i * 70 + 'ms' }">{{ c === ' ' ? '\u00A0' : c }}</span>
          </h1>
          <p class="role">I’m a {{ typedText }}<span class="caret"></span></p>
          <p class="lead">Designer and developer. Fast sites, clear interfaces, tiny details that make people smile.</p>
          <div class="btns">
            <div class="btns">
  <details class="dropdown">
    <summary class="btn fill">
      My CV <span class="arrow">▾</span>
    </summary>
    <ul class="menu">
      <li><a href="/Rizza_Mengolio.pdf" target="_blank" rel="noopener" @click="$event.target.closest('details').removeAttribute('open')">View CV</a></li>
      <li><a href="/Rizza_Mengolio.pdf" download="Rizza_CV.pdf" @click="$event.target.closest('details').removeAttribute('open')">Download CV</a></li>
    </ul>
  </details>
</div>
          </div>
        </div>
<div class="avatar" @click="burst($event)">
  <img src="/rizza_home.jpg" alt="Photo of Your Name" />
</div>
      </section>

      <!-- ABOUT -->
      <section id="about">
        <h2>About</h2>
        <div class="about">
          <div class="rv" data-reveal>
            <p>It all started with a few lines of HTML and CSS. Now I build polished, accessible websites that people enjoy using.</p>
            <p>When the laptop closes, I’m sketching interface ideas with a cup of milk tea nearby.</p>
          </div>
          <div class="stats rv" data-reveal>
            <div class="stat" v-for="s in stats" :key="s.label">
              <b>{{ s.value }}{{ s.suffix }}</b><small>{{ s.label }}</small>
            </div>
          </div>
        </div>
      </section>

      <!-- SKILLS -->
      <section id="skills">
        <h2>Skills</h2>
        <div class="chips" role="group" aria-label="Filter skills">
          <button
            v-for="c in categories"
            :key="c"
            class="chip"
            :class="{ on: selectedCategory === c }"
            @click="selectedCategory = c"
          >{{ c }}</button>
        </div>
        <transition-group name="skill" tag="div" class="skills" data-reveal>
          <div class="skill" v-for="s in filteredSkills" :key="s.name">
            <div><span>{{ s.name }}</span><span>{{ s.level }}%</span></div>
            <div class="track" style="display: block">
              <div class="meter" :style="{ '--w': s.level + '%' }"></div>
            </div>
          </div>
        </transition-group>
      </section>

      <!-- EDUCATION -->
      <section id="education">
        <h2>Education</h2>
        <div class="tl">
          <article v-for="e in education" :key="e.title">
            <time>{{ e.years }}</time>
            <h3>{{ e.title }}</h3>
            <p>{{ e.school }}</p>
          </article>
        </div>
      </section>

      <!-- CERTIFICATIONS -->
      <section id="certifications">
        <h2>Certifications</h2>
        <div class="certs">
          <button
            class="flip"
            v-for="(c, i) in certifications"
            :key="c.title"
            :class="{ on: flippedCard === i }"
            @click="flippedCard = flippedCard === i ? null : i"
            :aria-pressed="flippedCard === i"
          >
            <div class="inner">
              <div class="face">
                <span class="badge">{{ c.icon }}</span>
                <div><h3>{{ c.title }}</h3><small>{{ c.issuer }} · {{ c.year }}</small></div>
              </div>
              <div class="face back"><h3>What I learned</h3><small>{{ c.details }}</small></div>
            </div>
          </button>
        </div>
        <p class="hint">Tap a card to flip it.</p>
      </section>

      <!-- CONTACTS -->
      <section id="contacts">
        <h2>Contacts</h2>
        <div class="contact">
          <div class="links">
            <button @click="copyEmail">rizzasanchezmengolio@gmail.com</button>
            <a href="https://github.com/rizzasanchezmengolio-hash" target="_blank" rel="noopener">GitHub</a>
            <a href="https://www.facebook.com/share/1EBgQZ7wMk/" target="_blank" rel="noopener">Facebook</a>
            <a href="tel:+630000000000">☎ +63 936016389</a>
          </div>

          <form @submit.prevent="sendMessage" novalidate>
            <div v-if="messageSent" class="sent">
              <b>Message sent!</b>
              <p>Thanks, {{ form.name }}. I’ll reply soon.</p>
              <button type="button" class="btn line" @click="messageSent = false">Send another</button>
            </div>
            <template v-else>
              <label>Name
                <input v-model.trim="form.name" autocomplete="name" />
                <span class="err" v-if="errors.name">{{ errors.name }}</span>
              </label>
              <label>Email
                <input v-model.trim="form.email" type="email" autocomplete="email" />
                <span class="err" v-if="errors.email">{{ errors.email }}</span>
              </label>
              <label>Message
                <textarea v-model.trim="form.message" rows="4"></textarea>
                <span class="err" v-if="errors.message">{{ errors.message }}</span>
              </label>
              <button class="btn fill" type="submit">Send message</button>
            </template>
          </form>
        </div>
      </section>
    </main>

    <footer>All rights reserved · © {{ year }} Rizza S. Mengolio</footer>
    <transition name="fade"><div class="toast" v-if="toast" role="status">{{ toast }}</div></transition>
  </div>
</template>