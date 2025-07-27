<script setup>
import { onMounted, ref, watch } from 'vue'
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
    status: 0, // 0: not yet paid, 1: payment received, 2: paid
    side: null,
})

const statusMap = {
    0: 'Unpaid',
    1: 'Received',
    2: 'Paid',
}

const form = ref(getInitialForm())
const bettorForm = ref(getInitialBettorForm())

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
    teams.value.splice(index, 1)
}

const removeBet = (index, side) => {
    if (side === 0) firstTeamBettor.value.splice(index, 1)
    else secondTeamBettor.value.splice(index, 1)
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
    <div class="h-full p-[20px] flex flex-col">
        <div class="flex items-center mb-12 relative">
            <img
                src="./assets/kukuys.png"
                alt="Kukuys"
                class="w-[100px] absolute"
            />
            <div class="flex flex-col grow justify-center h-[135px]">
                <span class="text-6xl font-bold mb-2">Pustahan Nights</span>
                <p class="text-sm font-medium">by: welpsilog</p>
            </div>
        </div>

        <div class="flex gap-6 justify-center mb-4" v-if="teams.length < 2">
            <button
                class="hover:shadow-md hover:shadow-blue-700/20"
                @click="openForm = true"
            >
                Add Team Roster
            </button>
        </div>

        <div class="flex gap-6 mb-6" v-if="teams.length > 0">
            <div
                class="flex flex-col border basis-[50%] p-4 rounded-xl gap-2 w-xs text-white"
                :class="{
                    'bg-green-400': key === 0,
                    'bg-red-400': key === 1,
                }"
                v-for="(team, key) in teams"
            >
                <div class="flex gap-2 items-center">
                    <p class="text-3xl text-left font-bold">
                        {{ team.name }}
                    </p>
                    <div class="flex grow justify-end gap-4">
                        <span
                            class="material-symbols-outlined text-sm text-white font-bold cursor-pointer text-right"
                            @click="edit(key)"
                        >
                            edit
                        </span>
                        <span
                            class="material-symbols-outlined text-sm text-white font-bold cursor-pointer text-right"
                            @click="remove(key)"
                        >
                            delete
                        </span>
                    </div>
                </div>
                <div class="flex">
                    <p class="text-lg mr-6">1.</p>
                    <p class="text-lg">{{ team.carry }}</p>
                </div>
                <div class="flex">
                    <p class="text-lg mr-6">2.</p>
                    <p class="text-lg">{{ team.mid }}</p>
                </div>
                <div class="flex">
                    <p class="text-lg mr-6">3.</p>
                    <p class="text-lg">{{ team.off }}</p>
                </div>
                <div class="flex">
                    <p class="text-lg mr-6">4.</p>
                    <p class="text-lg">{{ team.soft }}</p>
                </div>
                <div class="flex">
                    <p class="text-lg mr-6">5.</p>
                    <p class="text-lg">{{ team.hard }}</p>
                </div>
            </div>
        </div>

        <div class="flex gap-6 justify-center mb-4" v-if="teams.length >= 2">
            <button
                class="border-green-400! text-green-400! basis-[50%] hover:shadow-md hover:shadow-green-400/20"
                @click="addBettor(0)"
            >
                Bet on {{ teams[0].name }}
            </button>

            <button
                class="border-red-400! text-red-400! basis-[50%] hover:shadow-md shadow-red-400/20"
                @click="addBettor(1)"
            >
                Bet on {{ teams[1].name }}
            </button>
        </div>

        <div class="flex flex-wrap gap-6 mb-8" v-if="teams.length === 2">
            <div
                class="border-green-400! basis-[calc(50%-0.75rem)] max-w-[calc(50%-0.75rem)]"
                v-if="teams.length > 0 && firstTeamBettor.length > 0"
            >
                <p class="text-xl font-bold mb-6">
                    Bets on team {{ teams[0].name }}
                </p>
                <div
                    class="flex gap-2 w-full items-center hover:bg-stone-100 p-2 rounded-xl"
                    v-for="(bettor, key) in firstTeamBettor"
                >
                    <p class="text-md w-3xl truncate text-left">
                        {{ key + 1 }}. {{ bettor.name }}
                    </p>
                    <p class="text-md flex-1 text-right">
                        {{ bettor.amount.toLocaleString() }}
                    </p>
                    <select
                        name="status"
                        id="status"
                        v-model="firstTeamBettor[key].status"
                    >
                        <option :value="0">Unpaid</option>
                        <option :value="1">Received</option>
                        <option :value="2">Paid</option>
                    </select>
                    <div class="flex flex-1 gap-2 justify-end">
                        <span
                            class="material-symbols-outlined text-blue-400 cursor-pointer text-xl!"
                            @click="editBet(key, 0)"
                        >
                            edit
                        </span>
                        <span
                            class="material-symbols-outlined text-red-400 cursor-pointer text-xl!"
                            @click="removeBet(key, 0)"
                        >
                            delete
                        </span>
                    </div>
                </div>
                <p class="font-bold text-2xl mt-6 text-right">
                    Total Bet:
                    {{
                        firstTeamBettor
                            .reduce((sum, item) => sum + item.amount, 0)
                            .toLocaleString()
                    }}
                </p>
            </div>

            <div
                class="border-green-400! basis-[calc(50%-0.75rem)] max-w-[calc(50%-0.75rem)] ml-auto"
                v-if="teams.length > 1 && secondTeamBettor.length > 0"
            >
                <p
                    class="text-xl font-bold mb-6"
                    v-if="secondTeamBettor.length > 0"
                >
                    Bets on team {{ teams[1].name }}
                </p>
                <div
                    class="flex gap-2 w-full items-center hover:bg-stone-100 p-2 rounded-xl"
                    v-for="(bettor, key) in secondTeamBettor"
                >
                    <p class="text-md w-3xl truncate text-left">
                        {{ key + 1 }}. {{ bettor.name }}
                    </p>
                    <p class="text-md flex-1 text-right">
                        {{ bettor.amount.toLocaleString() }}
                    </p>
                    <select
                        name="status"
                        id="status"
                        v-model="secondTeamBettor[key].status"
                    >
                        <option :value="0">Unpaid</option>
                        <option :value="1">Received</option>
                        <option :value="2">Paid</option>
                    </select>
                    <div class="flex flex-1 gap-2 justify-end">
                        <span
                            class="material-symbols-outlined text-blue-400 cursor-pointer text-xl!"
                            @click="editBet(key, 1)"
                        >
                            edit
                        </span>
                        <span
                            class="material-symbols-outlined text-red-400 cursor-pointer text-xl!"
                            @click="removeBet(key, 1)"
                        >
                            delete
                        </span>
                    </div>
                </div>
                <p class="font-bold text-2xl mt-6 text-right">
                    Total Bet:
                    {{
                        secondTeamBettor
                            .reduce((sum, item) => sum + item.amount, 0)
                            .toLocaleString()
                    }}
                </p>
            </div>
        </div>

        <!-- Team Form Modal -->
        <div
            v-if="openForm"
            class="modal absolute top-0 left-0 h-full w-full m-auto flex flex-col items-center justify-center"
        >
            <TeamForm
                :form="form"
                @submit="teamFormSubmit"
                @close="close"
                class="rounded-xl w-md bg-white z-10"
            ></TeamForm>
        </div>

        <!-- Bettor Form Modal -->
        <div
            v-if="openBettorForm"
            class="modal absolute top-0 left-0 h-full w-full m-auto flex flex-col items-center justify-center"
        >
            <BettorForm
                :form="bettorForm"
                @submit="bettorFormSubmit"
                @close="bettorClose"
                class="rounded-xl w-md bg-white z-10"
            ></BettorForm>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.modal {
    background-color: rgba(0, 0, 0, 0.1);
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
}
</style>
