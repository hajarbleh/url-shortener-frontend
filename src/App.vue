<template>
  <div class="container">
    <h1>John Peter's URL Shortener</h1>

    <form @submit.prevent="shorten" class="form">
      <div class="field">
        <label for="longUrl">Long URL</label>
        <input
          id="longUrl"
          v-model="form.longUrl"
          type="url"
          placeholder="https://example.com/very/long/path"
          required
        />
      </div>

      <div class="field">
        <label for="shortCode">Custom short code <span class="optional">(optional)</span></label>
        <input
          id="shortCode"
          v-model="form.shortCode"
          type="text"
          placeholder="myalias"
          maxlength="20"
        />
      </div>

      <div class="field">
        <label for="expiresAt">Expires at <span class="optional">(optional)</span></label>
        <input
          id="expiresAt"
          v-model="form.expiresAt"
          type="datetime-local"
        />
      </div>

      <button type="submit" :disabled="loading">
        {{ loading ? 'Shortening…' : 'Shorten' }}
      </button>

      <p v-if="error" class="error">{{ error }}</p>
    </form>

    <div v-if="result" class="result">
      <h2>Shortened URL</h2>
      <div class="result-row">
        <a :href="shortUrl" target="_blank" class="short-url">{{ shortUrl }}</a>
        <button class="copy-btn" @click="copy">{{ copied ? 'Copied!' : 'Copy' }}</button>
        <button class="delete-btn" @click="deleteUrl">Delete</button>
      </div>
      <p class="meta">Original: <a :href="result.long_url" target="_blank">{{ result.long_url }}</a></p>
      <p v-if="result.expires_at" class="meta">Expires: {{ formatDate(result.expires_at) }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const API_BASE = import.meta.env.VITE_API_BASE ?? 'http://localhost:8080'

const form = ref({ longUrl: '', shortCode: '', expiresAt: '' })
const result = ref(null)
const loading = ref(false)
const error = ref('')
const copied = ref(false)

const shortUrl = computed(() =>
  result.value ? `${API_BASE}/${result.value.short_code}` : ''
)

async function shorten() {
  error.value = ''
  loading.value = true
  try {
    const body = { long_url: form.value.longUrl }
    if (form.value.shortCode) body.short_code = form.value.shortCode
    if (form.value.expiresAt) body.expires_at = new Date(form.value.expiresAt).toISOString()

    const res = await fetch(`${API_BASE}/shorten`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(body),
    })
    const data = await res.json()
    if (!res.ok) throw new Error(data.error ?? 'Something went wrong')
    result.value = data
    form.value = { longUrl: '', shortCode: '', expiresAt: '' }
  } catch (e) {
    error.value = e.message
  } finally {
    loading.value = false
  }
}

async function deleteUrl() {
  if (!result.value) return
  error.value = ''
  try {
    const res = await fetch(`${API_BASE}/${result.value.short_code}`, { method: 'DELETE' })
    if (!res.ok) {
      const data = await res.json()
      throw new Error(data.error ?? 'Delete failed')
    }
    result.value = null
  } catch (e) {
    error.value = e.message
  }
}

async function copy() {
  await navigator.clipboard.writeText(shortUrl.value)
  copied.value = true
  setTimeout(() => (copied.value = false), 2000)
}

function formatDate(iso) {
  return new Date(iso).toLocaleString()
}
</script>
