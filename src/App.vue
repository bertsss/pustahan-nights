<script setup>
import { onMounted, ref, watch, computed } from 'vue'
import TeamForm from './components/TeamForm.vue'
import BettorForm from './components/BettorForm.vue'

const openForm = ref(false)
const openBettorForm = ref(false)
const teams = ref([])
const firstTeamBettor = ref([])
const secondTeamBettor = ref([])
const updateIndex = ref(null)

const getInitialForm = () => ({
    name: '',
    carry: '',
    mid: '',
    off: '',
    soft: '',
    hard: '',
})

const getInitialBettorForm = () => ({
    name: '',
    amount: 0,
    status: 0,
    side: null,
})

const statusMap = {
    0: { label: 'Unpaid', class: 'status-unpaid' },
    1: { label: 'Received', class: 'status-received' },
    2: { label: 'Paid', class: 'status-paid' },
}

const form = ref(getInitialForm())
const bettorForm = ref(getInitialBettorForm())

const firstTeamTotal = computed(() =>
    firstTeamBettor.value.reduce((sum, item) => sum + item.amount, 0)
)

const secondTeamTotal = computed(() =>
    secondTeamBettor.value.reduce((sum, item) => sum + item.amount, 0)
)

const totalBets = computed(() => firstTeamTotal.value + secondTeamTotal.value)

const teamFormSubmit = () => {
    if (updateIndex.value !== null) {
        teams.value.splice(updateIndex.value, 1, form.value)
    } else {
        teams.value.push(form.value)
    }

    form.value = getInitialForm()
    updateIndex.value = null
    openForm.value = false
}

const bettorFormSubmit = () => {
    if (updateIndex.value !== null) {
        if (bettorForm.value.side === 0) {
            firstTeamBettor.value.splice(updateIndex.value, 1, bettorForm.value)
        } else {
            secondTeamBettor.value.splice(
                updateIndex.value,
                1,
                bettorForm.value
            )
        }
    } else {
        if (bettorForm.value.side === 0) {
            firstTeamBettor.value.push(bettorForm.value)
        } else {
            secondTeamBettor.value.push(bettorForm.value)
        }
    }

    bettorForm.value = getInitialBettorForm()
    updateIndex.value = null
    openBettorForm.value = false
}

const remove = (index) => {
    if (confirm('Are you sure you want to delete this team?')) {
        teams.value.splice(index, 1)
    }
}

const removeBet = (index, side) => {
    if (confirm('Are you sure you want to delete this bet?')) {
        if (side === 0) firstTeamBettor.value.splice(index, 1)
        else secondTeamBettor.value.splice(index, 1)
    }
}

const close = () => {
    form.value = getInitialForm()
    openForm.value = false
}

const bettorClose = () => {
    bettorForm.value = getInitialBettorForm()
    openBettorForm.value = false
}

const addBettor = (index) => {
    bettorForm.value.side = index
    openBettorForm.value = true
}

const edit = (index) => {
    const { name, carry, mid, off, soft, hard } = teams.value[index]
    form.value.name = name
    form.value.carry = carry
    form.value.mid = mid
    form.value.off = off
    form.value.soft = soft
    form.value.hard = hard

    updateIndex.value = index
    openForm.value = true
}

const editBet = (index, group) => {
    const { name, amount, status, side } =
        group === 0
            ? firstTeamBettor.value[index]
            : secondTeamBettor.value[index]
    bettorForm.value.name = name
    bettorForm.value.amount = amount
    bettorForm.value.status = status
    bettorForm.value.side = side

    updateIndex.value = index
    openBettorForm.value = true
}

onMounted(() => {
    const saved = localStorage.getItem('teams')
    if (saved) teams.value = JSON.parse(saved)

    const savedFirstTeamBettor = localStorage.getItem('firstTeamBettor')
    if (savedFirstTeamBettor)
        firstTeamBettor.value = JSON.parse(savedFirstTeamBettor)

    const savedSecondTeamBettor = localStorage.getItem('secondTeamBettor')
    if (savedSecondTeamBettor)
        secondTeamBettor.value = JSON.parse(savedSecondTeamBettor)
})

watch(
    teams,
    (newVal) => {
        localStorage.setItem('teams', JSON.stringify(newVal))
    },
    { deep: true }
)

watch(
    firstTeamBettor,
    (newVal) => {
        localStorage.setItem('firstTeamBettor', JSON.stringify(newVal))
    },
    { deep: true }
)

watch(
    secondTeamBettor,
    (newVal) => {
        localStorage.setItem('secondTeamBettor', JSON.stringify(newVal))
    },
    { deep: true }
)
</script>

<template>
    <div
        class="min-h-screen bg-gradient-to-br from-gray-50 to-gray-100 p-4 lg:p-8"
    >
        <header class="mb-8 lg:mb-12">
            <div class="flex items-center justify-between mb-6">
                <img
                    src="./assets/kukuys.png"
                    alt="Kukuys Logo"
                    class="w-12 h-12 lg:w-16 lg:h-16 shadow-lg flex-shrink-0"
                />

                <div class="flex-1 text-center">
                    <h1
                        class="text-4xl lg:text-6xl font-bold text-gray-900 mb-2"
                    >
                        Pustahan Nights
                    </h1>
                    <p class="text-gray-600 text-sm lg:text-base">
                        by: Kukuys' Devs
                    </p>
                </div>

                <div class="w-12 h-12 lg:w-16 lg:h-16 flex-shrink-0"></div>
            </div>

            <div
                class="flex justify-center"
                v-if="teams.length === 2 && totalBets > 0"
            >
                <div
                    class="bg-gradient-to-r from-yellow-400 to-orange-500 text-white px-8 py-4 rounded-2xl shadow-lg"
                >
                    <div class="text-center">
                        <div class="text-sm font-medium opacity-90">
                            Total Pot
                        </div>
                        <div class="text-2xl lg:text-3xl font-bold">
                            ₱{{ totalBets.toLocaleString() }}
                        </div>
                    </div>
                </div>
            </div>
        </header>

        <section class="mb-8" v-if="teams.length < 2">
            <div class="text-center">
                <h2 class="text-2xl font-bold text-gray-900 mb-4">
                    Setup Teams
                </h2>
                <p class="text-gray-600 mb-6">
                    Add team rosters to start betting
                </p>
                <button @click="openForm = true" class="btn-primary">
                    <span class="material-symbols-outlined mr-2">add</span>
                    Add Team Roster
                </button>
            </div>
        </section>

        <section class="mb-8" v-if="teams.length > 0">
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <div
                    v-for="(team, key) in teams"
                    :key="key"
                    class="card relative overflow-hidden"
                    :class="{
                        'card-team-1': key === 0,
                        'card-team-2': key === 1,
                    }"
                >
                    <div class="flex items-center justify-between mb-6">
                        <h3 class="text-2xl lg:text-3xl font-bold">
                            {{ team.name }}
                        </h3>
                        <div class="flex gap-2">
                            <button
                                @click="edit(key)"
                                class="p-2 rounded-lg bg-white/20 hover:bg-white/30 transition-colors"
                            >
                                <span class="material-symbols-outlined text-sm"
                                    >edit</span
                                >
                            </button>
                            <button
                                @click="remove(key)"
                                class="p-2 rounded-lg bg-white/20 hover:bg-white/30 transition-colors"
                            >
                                <span class="material-symbols-outlined text-sm"
                                    >delete</span
                                >
                            </button>
                        </div>
                    </div>

                    <div class="space-y-3">
                        <div class="flex items-center">
                            <span class="text-lg font-medium w-6">1.</span>
                            <span class="text-lg">{{ team.carry }}</span>
                            <span class="ml-auto text-sm opacity-75"
                                >Carry</span
                            >
                        </div>
                        <div class="flex items-center">
                            <span class="text-lg font-medium w-6">2.</span>
                            <span class="text-lg">{{ team.mid }}</span>
                            <span class="ml-auto text-sm opacity-75">Mid</span>
                        </div>
                        <div class="flex items-center">
                            <span class="text-lg font-medium w-6">3.</span>
                            <span class="text-lg">{{ team.off }}</span>
                            <span class="ml-auto text-sm opacity-75"
                                >Offlane</span
                            >
                        </div>
                        <div class="flex items-center">
                            <span class="text-lg font-medium w-6">4.</span>
                            <span class="text-lg">{{ team.soft }}</span>
                            <span class="ml-auto text-sm opacity-75"
                                >Soft Support</span
                            >
                        </div>
                        <div class="flex items-center">
                            <span class="text-lg font-medium w-6">5.</span>
                            <span class="text-lg">{{ team.hard }}</span>
                            <span class="ml-auto text-sm opacity-75"
                                >Hard Support</span
                            >
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <section class="mb-8" v-if="teams.length >= 2">
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-4">
                <button
                    @click="addBettor(0)"
                    class="btn-success p-4 text-lg font-semibold"
                >
                    <span class="material-symbols-outlined mr-2"
                        >trending_up</span
                    >
                    Bet on {{ teams[0].name }}
                </button>
                <button
                    @click="addBettor(1)"
                    class="btn-danger p-4 text-lg font-semibold"
                >
                    <span class="material-symbols-outlined mr-2"
                        >trending_up</span
                    >
                    Bet on {{ teams[1].name }}
                </button>
            </div>
        </section>

        <section
            v-if="
                teams.length === 2 &&
                (firstTeamBettor.length > 0 || secondTeamBettor.length > 0)
            "
        >
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <div v-if="firstTeamBettor.length > 0" class="card">
                    <div class="flex items-center justify-between mb-6">
                        <h3 class="text-xl font-bold text-gray-900">
                            {{ teams[0].name }} Bets
                        </h3>
                        <div class="text-right">
                            <div class="text-2xl font-bold text-green-600">
                                ₱{{ firstTeamTotal.toLocaleString() }}
                            </div>
                            <div class="text-sm text-gray-500">
                                {{ firstTeamBettor.length }} bets
                            </div>
                        </div>
                    </div>

                    <div class="space-y-3">
                        <div
                            v-for="(bettor, key) in firstTeamBettor"
                            :key="key"
                            class="flex items-center gap-4 p-3 rounded-lg bg-gray-50 hover:bg-gray-100 transition-colors"
                        >
                            <div class="flex-1">
                                <div class="font-medium text-gray-900">
                                    {{ bettor.name }}
                                </div>
                            </div>
                            <div class="text-right">
                                <div class="font-bold text-gray-900">
                                    ₱{{ bettor.amount.toLocaleString() }}
                                </div>
                                <span
                                    class="status-badge"
                                    :class="statusMap[bettor.status].class"
                                >
                                    {{ statusMap[bettor.status].label }}
                                </span>
                            </div>
                            <select
                                v-model="firstTeamBettor[key].status"
                                class="custom-select text-sm"
                            >
                                <option :value="0">Unpaid</option>
                                <option :value="1">Received</option>
                                <option :value="2">Paid</option>
                            </select>
                            <div class="flex gap-2">
                                <button
                                    @click="editBet(key, 0)"
                                    class="p-1 text-blue-500 hover:bg-blue-50 rounded"
                                >
                                    <span
                                        class="material-symbols-outlined text-sm"
                                        >edit</span
                                    >
                                </button>
                                <button
                                    @click="removeBet(key, 0)"
                                    class="p-1 text-red-500 hover:bg-red-50 rounded"
                                >
                                    <span
                                        class="material-symbols-outlined text-sm"
                                        >delete</span
                                    >
                                </button>
                            </div>
                        </div>
                    </div>
                </div>

                <div v-if="secondTeamBettor.length > 0" class="card">
                    <div class="flex items-center justify-between mb-6">
                        <h3 class="text-xl font-bold text-gray-900">
                            {{ teams[1].name }} Bets
                        </h3>
                        <div class="text-right">
                            <div class="text-2xl font-bold text-red-600">
                                ₱{{ secondTeamTotal.toLocaleString() }}
                            </div>
                            <div class="text-sm text-gray-500">
                                {{ secondTeamBettor.length }} bets
                            </div>
                        </div>
                    </div>

                    <div class="space-y-3">
                        <div
                            v-for="(bettor, key) in secondTeamBettor"
                            :key="key"
                            class="flex items-center gap-4 p-3 rounded-lg bg-gray-50 hover:bg-gray-100 transition-colors"
                        >
                            <div class="flex-1">
                                <div class="font-medium text-gray-900">
                                    {{ bettor.name }}
                                </div>
                            </div>
                            <div class="text-right">
                                <div class="font-bold text-gray-900">
                                    ₱{{ bettor.amount.toLocaleString() }}
                                </div>
                                <span
                                    class="status-badge"
                                    :class="statusMap[bettor.status].class"
                                >
                                    {{ statusMap[bettor.status].label }}
                                </span>
                            </div>
                            <select
                                v-model="secondTeamBettor[key].status"
                                class="custom-select text-sm"
                            >
                                <option :value="0">Unpaid</option>
                                <option :value="1">Received</option>
                                <option :value="2">Paid</option>
                            </select>
                            <div class="flex gap-2">
                                <button
                                    @click="editBet(key, 1)"
                                    class="p-1 text-blue-500 hover:bg-blue-50 rounded"
                                >
                                    <span
                                        class="material-symbols-outlined text-sm"
                                        >edit</span
                                    >
                                </button>
                                <button
                                    @click="removeBet(key, 1)"
                                    class="p-1 text-red-500 hover:bg-red-50 rounded"
                                >
                                    <span
                                        class="material-symbols-outlined text-sm"
                                        >delete</span
                                    >
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <div
            v-if="openForm"
            class="modal-backdrop fixed inset-0 flex items-center justify-center p-4 z-50"
        >
            <TeamForm
                :form="form"
                @submit="teamFormSubmit"
                @close="close"
                class="modal-content w-full max-w-md"
            ></TeamForm>
        </div>

        <div
            v-if="openBettorForm"
            class="modal-backdrop fixed inset-0 flex items-center justify-center p-4 z-50"
        >
            <BettorForm
                :form="bettorForm"
                @submit="bettorFormSubmit"
                @close="bettorClose"
                class="modal-content w-full max-w-md"
            ></BettorForm>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.modal-backdrop {
    background-color: rgba(0, 0, 0, 0.5);
    backdrop-filter: blur(5px);
}

.modal-content {
    background-color: white;
    border-radius: 12px;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
    max-width: 600px;
    width: 90%;
    position: relative;
    z-index: 100;
}

.btn-primary,
.btn-success,
.btn-danger {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    gap: 0.5rem;
    padding: 0.75rem 1.5rem;
    border-radius: 0.75rem;
    font-weight: 600;
    transition: all 0.2s ease;
    border: none;
    cursor: pointer;
    text-decoration: none;
    font-size: 0.875rem;
}

.btn-primary {
    background-color: #6366f1;
    color: white;
}

.btn-primary:hover {
    background-color: #5b21b6;
    transform: translateY(-1px);
    box-shadow: 0 4px 12px rgba(99, 102, 241, 0.3);
}

.btn-success {
    background-color: #10b981;
    color: white;
}

.btn-success:hover {
    background-color: #059669;
    transform: translateY(-1px);
    box-shadow: 0 4px 12px rgba(16, 185, 129, 0.3);
}

.btn-danger {
    background-color: #ef4444;
    color: white;
}

.btn-danger:hover {
    background-color: #dc2626;
    transform: translateY(-1px);
    box-shadow: 0 4px 12px rgba(239, 68, 68, 0.3);
}

.card {
    background: white;
    padding: 24px;
    border-radius: 16px;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
    border: 1px solid #e5e7eb;
    transition: all 0.2s ease;
}

.card:hover {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    transform: translateY(-1px);
}

.card.card-team-1 {
    background: linear-gradient(135deg, #059669 0%, #059669 100%);
    color: white;
    border-color: #059669;
}

.card.card-team-2 {
    background: linear-gradient(135deg, #dc2626 0%, #dc2626 100%);
    color: white;
    border-color: #dc2626;
}

.status-badge {
    display: inline-flex;
    align-items: center;
    padding: 4px 8px;
    border-radius: 6px;
    font-size: 12px;
    font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 0.025em;
}

.status-unpaid {
    background-color: #fef3c7;
    color: #92400e;
}

.status-received {
    background-color: #dbeafe;
    color: #1e40af;
}

.status-paid {
    background-color: #d1fae5;
    color: #065f46;
}

.custom-select {
    padding: 8px 12px;
    border: 1px solid #d1d5db;
    border-radius: 8px;
    background-color: white;
    color: #374151;
    appearance: none;
    background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' fill='none' viewBox='0 0 20 20'%3e%3cpath stroke='%236b7280' stroke-linecap='round' stroke-linejoin='round' stroke-width='1.5' d='m6 8 4 4 4-4'/%3e%3c/svg%3e");
    background-position: right 0.5rem center;
    background-repeat: no-repeat;
    background-size: 1.5em 1.5em;
    padding-right: 2.5rem;
    min-width: 120px;
}

.custom-select:focus {
    outline: none;
    border-color: #6366f1;
    box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.1);
}

.custom-select:hover {
    border-color: #9ca3af;
}

@media (max-width: 768px) {
    .grid-cols-1 {
        grid-template-columns: 1fr;
    }
}
</style>
