<script setup>
import { ref, computed } from 'vue'

// States
const tasks = ref([])
const inputTask = ref('')
const errorMessage = ref('')
const activeFilter = ref('semua')

// Menambahkan tugas baru
const addTask = (title) => {
  if (!title || !title.trim()) {
    errorMessage.value = '⚠️ Isi nama tugas terlebih dahulu, ya!'
    return
  }

  const newTask = {
    id: Date.now(),
    title: title.trim(),
    status: false
  }

  tasks.value.push(newTask)
  inputTask.value = ''
  errorMessage.value = ''
}

// Menghapus tugas berdasarkan ID
const deleteTask = (id) => {
  const task = tasks.value.find(item => item.id === id)

  if (!task) {
    alert('Task tidak ditemukan!')
    return
  }

  const confirmation = confirm(
    'Apakah kamu yakin ingin menghapus tugas ini?'
  )

  if (confirmation) {
    tasks.value = tasks.value.filter(item => item.id !== id)
  }
}

// Menghitung tugas selesai
const completedTasks = computed(() => {
  return tasks.value.filter(task => task.status).length
})

// Menghitung tugas belum selesai
const remainingTasks = computed(() => {
  return tasks.value.filter(task => !task.status).length
})

// Menghitung persentase progres
const progress = computed(() => {
  if (tasks.value.length === 0) return 0

  return Math.round(
    (completedTasks.value / tasks.value.length) * 100
  )
})

// Filter daftar tugas
const filteredTasks = computed(() => {
  if (activeFilter.value === 'aktif') {
    return tasks.value.filter(task => !task.status)
  }

  if (activeFilter.value === 'selesai') {
    return tasks.value.filter(task => task.status)
  }

  return tasks.value
})

// Menghapus semua tugas yang selesai
const clearCompleted = () => {
  if (completedTasks.value === 0) return

  const confirmation = confirm(
    'Hapus semua tugas yang sudah selesai?'
  )

  if (confirmation) {
    tasks.value = tasks.value.filter(task => !task.status)
  }
}
</script>

<template>
  <main
    class="relative min-h-screen overflow-hidden bg-[#f3efff] px-4 py-10 sm:px-8"
  >
    <!-- Dekorasi background -->
    <div
      class="pointer-events-none fixed -left-20 -top-20 h-64 w-64 rounded-full bg-purple-200/70 blur-3xl"
    ></div>

    <div
      class="pointer-events-none fixed -right-20 top-20 h-72 w-72 rounded-full bg-sky-200/70 blur-3xl"
    ></div>

    <div
      class="pointer-events-none fixed -bottom-24 left-1/3 h-64 w-64 rounded-full bg-pink-200/60 blur-3xl"
    ></div>

    <section class="relative mx-auto mt-4 max-w-3xl">
      <!-- Card utama -->
      <div
        class="rounded-[2rem] border border-white/80 bg-[#fffdf8] p-5 shadow-[12px_12px_30px_#d8d2ec,-12px_-12px_30px_#ffffff] sm:rounded-[2.5rem] sm:p-9"
      >
        <!-- Header -->
        <header class="mb-7 text-center">
          <div class="mb-3 flex items-center justify-center gap-2 sm:gap-3">
            <span class="text-3xl sm:text-4xl">☁️</span>

            <h1
              class="text-3xl font-black tracking-tight text-[#7770dc] sm:text-5xl"
            >
              Taskify <span class="text-[#65a9f5]">Vue</span>
            </h1>

            <span class="text-3xl sm:text-4xl">⭐</span>
          </div>

          <p class="text-sm font-medium text-[#9992c7]">
            ✦ Future you will thank you for studying today. ✦
          </p>
        </header>

        <!-- Form tambah tugas -->
        <form
          @submit.prevent="addTask(inputTask)"
          class="mb-3 flex w-full items-start gap-2 sm:gap-3"
        >
          <div class="min-w-0 flex-1">
            <input
              v-model="inputTask"
              @input="errorMessage = ''"
              type="text"
              placeholder="Masukkan tugas baru..."
              aria-label="Tugas baru"
              :aria-invalid="!!errorMessage"
              class="w-full rounded-2xl border bg-[#f0efff] px-3 py-4 text-sm text-[#514b83] shadow-[inset_5px_5px_12px_#dedcf2,inset_-5px_-5px_12px_#ffffff] outline-none transition placeholder:text-[#9690c0] focus:ring-4 focus:ring-purple-200 sm:px-5 sm:text-base"
              :class="
                errorMessage
                  ? 'border-rose-300 focus:ring-rose-200'
                  : 'border-white'
              "
            />

            <!-- Peringatan input kosong -->
            <p
              v-if="errorMessage"
              role="alert"
              class="mt-2 text-xs font-semibold text-rose-500 sm:text-sm"
            >
              {{ errorMessage }}
            </p>
          </div>

          <!-- Tombol di samping input -->
          <button
            type="submit"
            class="flex shrink-0 items-center justify-center gap-1 rounded-2xl border border-purple-300 bg-gradient-to-br from-[#b6a4ff] to-[#7865ed] px-3 py-4 text-sm font-bold text-white shadow-[4px_4px_10px_#c8bee9,-3px_-3px_8px_#ffffff] transition hover:-translate-y-0.5 hover:brightness-105 active:translate-y-1 sm:gap-2 sm:px-5 sm:text-base"
          >
            <span class="text-lg">＋</span>
            Tambah
          </button>
        </form>

        <!-- Ringkasan tugas -->
        <div
          class="mb-6 rounded-2xl border border-white bg-[#f0efff] p-4 shadow-[inset_3px_3px_8px_#e0ddf3,inset_-3px_-3px_8px_#ffffff]"
        >
          <div class="mb-3 flex items-center justify-between gap-2">
            <div>
              <p class="text-sm font-bold text-[#68619e]">
                Progres tugasmu ✨
              </p>

              <p class="mt-1 text-xs text-[#9992c7]">
                {{ completedTasks }} dari {{ tasks.length }} tugas selesai
              </p>
            </div>

            <span
              class="rounded-xl bg-white px-3 py-2 text-lg font-black text-[#8170e9] shadow-sm"
            >
              {{ progress }}%
            </span>
          </div>

          <!-- Progress bar -->
          <div class="h-3 overflow-hidden rounded-full bg-[#dedaf5]">
            <div
              class="h-full rounded-full bg-gradient-to-r from-[#b5a4ff] to-[#69b6f5] transition-all duration-500"
              :style="{ width: progress + '%' }"
            ></div>
          </div>

          <div class="mt-3 flex justify-between text-xs font-semibold">
            <span class="text-[#8170b6]">
              📌 {{ remainingTasks }} belum selesai
            </span>

            <span class="text-emerald-500">
              ✓ {{ completedTasks }} selesai
            </span>
          </div>
        </div>

        <!-- Filter tugas -->
        <div class="mb-5 flex flex-wrap items-center gap-2">
          <button
            v-for="filter in [
              { label: 'Semua', value: 'semua' },
              { label: 'Belum selesai', value: 'aktif' },
              { label: 'Selesai', value: 'selesai' }
            ]"
            :key="filter.value"
            @click="activeFilter = filter.value"
            type="button"
            class="rounded-xl border px-3 py-2 text-xs font-bold transition sm:px-4 sm:text-sm"
            :class="
              activeFilter === filter.value
                ? 'border-purple-300 bg-gradient-to-br from-[#b6a4ff] to-[#7865ed] text-white shadow-md'
                : 'border-white bg-[#f0efff] text-[#827bb5] shadow-sm hover:bg-purple-100'
            "
          >
            {{ filter.label }}
          </button>

          <button
            v-if="completedTasks > 0"
            @click="clearCompleted"
            type="button"
            class="ml-auto text-xs font-bold text-rose-400 transition hover:text-rose-600 sm:text-sm"
          >
            Hapus yang selesai
          </button>
        </div>

        <!-- Daftar tugas -->
        <ul class="space-y-4" v-auto-animate>
          <li
            v-for="task in filteredTasks"
            :key="task.id"
            class="flex items-center justify-between gap-3 rounded-[1.5rem] border border-white/90 bg-[#efeeff] p-4 shadow-[6px_6px_14px_#d9d7ef,-6px_-6px_14px_#ffffff] transition duration-200 hover:-translate-y-0.5 sm:gap-4 sm:px-5 sm:py-4"
          >
            <!-- Checkbox dan nama tugas -->
            <label
              class="flex min-w-0 flex-1 cursor-pointer items-center gap-3 sm:gap-4"
            >
              <input
                v-model="task.status"
                type="checkbox"
                class="h-6 w-6 shrink-0 cursor-pointer appearance-none rounded-lg border-2 border-[#b8aaff] bg-white shadow-[inset_2px_2px_4px_#e2ddff] checked:border-[#8c7cf0] checked:bg-[#9584ff] checked:after:block checked:after:text-center checked:after:text-lg checked:after:font-black checked:after:leading-5 checked:after:text-white checked:after:content-['✓']"
              />

              <span
                class="break-words text-sm font-semibold text-[#55517f] transition sm:text-lg"
                :class="
                  task.status
                    ? 'text-[#aaa5c7] line-through opacity-70'
                    : ''
                "
              >
                {{ task.title }}
              </span>
            </label>

            <!-- Tombol hapus -->
            <button
              @click="deleteTask(task.id)"
              type="button"
              :aria-label="'Hapus ' + task.title"
              class="shrink-0 rounded-full border border-rose-300 bg-gradient-to-br from-[#ff9fa9] to-[#ff7589] px-3 py-2 text-sm font-bold text-white shadow-[4px_4px_9px_#e5c5d4,-3px_-3px_8px_#ffffff] transition hover:-translate-y-0.5 hover:brightness-105 active:translate-y-1 sm:px-4"
            >
              🗑 <span class="hidden sm:inline">Hapus</span>
            </button>
          </li>
        </ul>

        <!-- Tampilan jika tidak ada tugas -->
        <div
          v-if="filteredTasks.length === 0"
          class="rounded-3xl bg-[#f7f5ff] px-4 py-8 text-center"
        >
          <div class="mb-3 text-5xl">
            {{ tasks.length === 0 ? '🧸' : '🌷' }}
          </div>

          <p class="font-bold text-[#827bb5]">
            {{
              tasks.length === 0
                ? 'Belum ada tugas nih!'
                : activeFilter === 'aktif'
                  ? 'Semua tugas sudah selesai!'
                  : 'Belum ada tugas di kategori ini.'
            }}
          </p>

          <p class="mt-1 text-sm text-[#aaa3ce]">
            {{
              tasks.length === 0
                ? 'Yuk, tambahkan tugas pertamamu ✨'
                : 'Kamu hebat! Terus pertahankan progresmu 💜'
            }}
          </p>
        </div>

        <!-- Footer -->
        <footer class="mt-7 text-center">
          <p class="text-xs font-medium text-[#aaa3ce]">
            Errors are proof that you are trying.
          </p>
        </footer>
      </div>

      <!-- Dekorasi -->
      <div
        class="pointer-events-none absolute -left-4 top-14 -rotate-12 text-3xl sm:-left-5 sm:text-4xl"
      >
        ⭐
      </div>

      <div
        class="pointer-events-none absolute -right-3 top-36 rotate-12 text-2xl sm:-right-4 sm:text-3xl"
      >
        ✨
      </div>

      <div
        class="pointer-events-none absolute -bottom-6 right-8 rotate-12 text-4xl"
      >
        🌟
      </div>
    </section>
  </main>
</template>