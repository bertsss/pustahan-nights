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
        <p>Bettor Info</p>
        <input
            type="text"
            v-model="form.name"
            placeholder="Bettor Name"
            required
        />
        <input type="number" v-model="form.amount" min="0" required />
        <select
            class="border-2"
            v-model="form.status"
            name="status"
            id="status"
            required
        >
            <option :value="0">Unpaid</option>
            <option :value="1">Received</option>
            <option :value="2">Paid</option>
        </select>
        <button type="submit" class="mt-4">Submit</button>
    </form>
</template>

<style lang="scss" scoped>
form {
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
}
</style>
