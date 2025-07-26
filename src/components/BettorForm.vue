<script setup>
import { ref } from 'vue'

const emit = defineEmits(['submit'])

const getInitialForm = () => ({
    name: '',
    amount: '',
    mid: '',
    off: '',
    soft: '',
    hard: '',
})

const form = ref(getInitialForm())

const submit = () => {
    emit('submit', form.value)
    form.value = getInitialForm()
}
</script>

<template>
    <form class="flex flex-col gap-4 p-6 relative" @submit.prevent="submit">
        <span
            @click="$emit('close')"
            class="absolute top-1 right-4 cursor-pointer text-red-600"
            >x</span
        >
        <p>Team Info</p>
        <input
            type="text"
            v-model="form.name"
            placeholder="Team Name"
            required
        />
        <input type="text" v-model="form.carry" placeholder="Carry" required />
        <input type="text" v-model="form.mid" placeholder="Mid" required />
        <input type="text" v-model="form.off" placeholder="Offlane" required />
        <input
            type="text"
            v-model="form.soft"
            placeholder="Soft Support"
            required
        />
        <input
            type="text"
            v-model="form.hard"
            placeholder="Hard Support"
            required
        />
        <button type="submit" class="mt-4">Submit</button>
    </form>
</template>

<style lang="scss" scoped>
form {
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
}
</style>
