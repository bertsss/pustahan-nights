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
            @click="$emit('close')"
            class="absolute top-1 right-4 cursor-pointer text-red-600"
            >x</span
        >
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
            <option :value="0">Not yet paid</option>
            <option :value="1">Payment Received</option>
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
