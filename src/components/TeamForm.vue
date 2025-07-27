<script setup>
const emit = defineEmits(['submit'])
const { form } = defineProps(['form'])
</script>

<template>
    <form
        class="flex flex-col gap-4 p-6 relative"
        @submit.prevent="$emit('submit')"
    >
        <span
            class="material-symbols-outlined absolute top-2 right-2 cursor-pointer text-red-600"
            @click="$emit('close')"
        >
            close
        </span>

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
