<script setup>
const emit = defineEmits(['submit', 'close'])
const { form } = defineProps(['form'])

const roles = [
    { key: 'carry', label: 'Carry (Position 1)', placeholder: 'Enter carry player name' },
    { key: 'mid', label: 'Mid (Position 2)', placeholder: 'Enter mid player name' },
    { key: 'off', label: 'Offlane (Position 3)', placeholder: 'Enter offlane player name' },
    { key: 'soft', label: 'Soft Support (Position 4)', placeholder: 'Enter soft support name' },
    { key: 'hard', label: 'Hard Support (Position 5)', placeholder: 'Enter hard support name' },
]
</script>

<template>
    <div class="p-6 relative">
        <div class="flex items-center justify-between mb-6">
            <h2 class="text-xl font-bold text-gray-900">Team Roster</h2>
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
                <label for="team-name" class="form-label">
                    Team Name
                </label>
                <input
                    id="team-name"
                    type="text"
                    v-model="form.name"
                    placeholder="Enter team name"
                    class="form-input"
                    required
                />
            </div>

            <div class="space-y-4">
                <h3 class="text-lg font-semibold text-gray-900 border-b border-gray-200 pb-2">Player Roster</h3>
                
                <div v-for="role in roles" :key="role.key" class="space-y-2">
                    <label :for="`role-${role.key}`" class="form-label">
                        {{ role.label }}
                    </label>
                    <input
                        :id="`role-${role.key}`"
                        type="text"
                        v-model="form[role.key]"
                        :placeholder="role.placeholder"
                        class="form-input"
                        required
                    />
                </div>
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
                    <span class="material-symbols-outlined mr-2 text-base">save</span>
                    Save Team
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

.form-input {
    width: 100%;
    padding: 0.75rem 1rem;
    border: 1px solid #d1d5db;
    border-radius: 0.5rem;
    background-color: white;
    color: #111827;
    font-size: 0.875rem;
    transition: all 0.2s ease;
}

.form-input:focus {
    outline: none;
    border-color: #6366f1;
    box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.1);
}

.form-input:hover {
    border-color: #9ca3af;
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
