<script setup>
const emit = defineEmits(['submit', 'close'])
const { form } = defineProps(['form'])
</script>

<template>
    <div class="p-6 relative">
        <div class="flex items-center justify-between mb-6">
            <h2 class="text-xl font-bold text-gray-900">Add New Bet</h2>
            <button
                @click="$emit('close')"
                class="p-2 text-gray-400 hover:text-gray-600 hover:bg-gray-100 rounded-lg transition-colors"
            >
                <span class="material-symbols-outlined">close</span>
            </button>
        </div>

        <form
            class="space-y-5"
            @submit.prevent="$emit('submit')"
        >
            <div>
                <label for="bettor-name" class="form-label">
                    Bettor Name
                </label>
                <input
                    id="bettor-name"
                    type="text"
                    v-model="form.name"
                    placeholder="Enter bettor's name"
                    class="form-input"
                    required
                />
            </div>

            <div>
                <label for="bet-amount" class="form-label">
                    Bet Amount
                </label>
                <div class="relative">
                    <span class="input-currency-symbol">₱</span>
                    <input
                        id="bet-amount"
                        type="number"
                        v-model="form.amount"
                        placeholder="0"
                        min="1"
                        class="form-input with-prefix"
                        required
                    />
                </div>
            </div>

            <div>
                <label for="payment-status" class="form-label">
                    Payment Status
                </label>
                <select
                    id="payment-status"
                    v-model="form.status"
                    class="form-select"
                    required
                >
                    <option :value="0">Unpaid - Awaiting payment</option>
                    <option :value="1">Received - Payment received</option>
                    <option :value="2">Paid - Winnings distributed</option>
                </select>
            </div>

            <div class="flex gap-3 pt-6">
                <button
                    type="button"
                    @click="$emit('close')"
                    class="btn-outline"
                >
                    Cancel
                </button>
                <button
                    type="submit"
                    class="btn-primary"
                >
                    <span class="material-symbols-outlined mr-2 text-base">add</span>
                    Add Bet
                </button>
            </div>
        </form>
    </div>
</template>

<style lang="scss" scoped>
.form-label {
    display: block;
    font-size: 0.875rem;
    font-weight: 500;
    color: #374151;
    margin-bottom: 0.5rem;
}

.form-input, .form-select {
    width: 100%;
    padding: 0.75rem 1rem;
    border: 1px solid #d1d5db;
    border-radius: 0.5rem;
    background-color: white;
    color: #111827;
    font-size: 0.875rem;
    transition: all 0.2s ease;
}

.form-input:focus, .form-select:focus {
    outline: none;
    border-color: #6366f1;
    box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.1);
}

.form-input:hover, .form-select:hover {
    border-color: #9ca3af;
}

.form-select {
    padding-right: 2.5rem;
    appearance: none;
    background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' fill='none' viewBox='0 0 20 20'%3e%3cpath stroke='%236b7280' stroke-linecap='round' stroke-linejoin='round' stroke-width='1.5' d='m6 8 4 4 4-4'/%3e%3c/svg%3e");
    background-position: right 0.75rem center;
    background-repeat: no-repeat;
    background-size: 1.25em 1.25em;
}

.input-currency-symbol {
    position: absolute;
    top: 50%;
    left: 0.75rem;
    transform: translateY(-50%);
    color: #4b5563;
    font-size: 0.875rem;
    font-weight: 500;
    pointer-events: none;
}

.form-input.with-prefix {
    padding-left: 2rem;
}

.btn-outline {
    flex: 1;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 0.75rem 1.5rem;
    border: 2px solid #d1d5db;
    border-radius: 0.5rem;
    background-color: white;
    color: #374151;
    font-size: 0.875rem;
    font-weight: 500;
    transition: all 0.2s ease;
    cursor: pointer;
}

.btn-outline:hover {
    background-color: #f9fafb;
    border-color: #9ca3af;
    color: #111827;
}

.btn-primary {
    flex: 1;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    gap: 0.5rem;
    padding: 0.75rem 1.5rem;
    border: 2px solid #6366f1;
    border-radius: 0.5rem;
    background-color: #6366f1;
    color: white;
    font-size: 0.875rem;
    font-weight: 500;
    transition: all 0.2s ease;
    cursor: pointer;
}

.btn-primary:hover {
    background-color: #5b21b6;
    border-color: #5b21b6;
    transform: translateY(-1px);
    box-shadow: 0 4px 12px rgba(99, 102, 241, 0.3);
}

.btn-primary:active {
    transform: translateY(0);
}
</style>
