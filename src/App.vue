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
    openForm.value = false
}

const bettorFormSubmit = () => {
    if (bettorForm.value.side === 0) {
        firstTeamBettor.value.push(bettorForm.value)
    } else {
        secondTeamBettor.value.push(bettorForm.value)
    }

    bettorForm.value = getInitialBettorForm()
    openBettorForm.value = false
}

const remove = (index) => {
    teams.value.splice(index, 1)
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

onMounted(() => {
    const saved = localStorage.getItem('teams')
    if (saved) teams.value = JSON.parse(saved)
})

watch(
    teams,
    (newVal) => {
        localStorage.setItem('teams', JSON.stringify(newVal))
    },
    { deep: true }
)
</script>

<template>
    <div class="h-full p-[20px] flex flex-col">
        <span class="text-5xl mb-8">Dota Nights</span>

        <div class="flex gap-6 justify-center mb-4">
            <button
                class="hover:shadow-md hover:shadow-blue-700/20"
                v-if="teams.length < 2"
                @click="openForm = true"
            >
                Add Team Roster
            </button>

            <button
                v-if="teams.length >= 2"
                class="border-green-400! text-green-400! basis-[50%] hover:shadow-md hover:shadow-green-400/20"
                @click="addBettor(0)"
            >
                Bet on {{ teams[0].name }}
            </button>

            <button
                v-if="teams.length >= 2"
                class="border-red-400! text-red-400! basis-[50%] hover:shadow-md shadow-red-400/20"
                @click="addBettor(1)"
            >
                Bet on {{ teams[1].name }}
            </button>
        </div>

        <div class="flex gap-6 mb-10" v-if="teams.length > 0">
            <div
                class="flex flex-col border basis-[50%] p-4 rounded-xl gap-2 w-xs text-white"
                :class="{
                    'bg-green-400': key === 0,
                    'bg-red-400': key === 1,
                }"
                v-for="(team, key) in teams"
            >
                <div class="flex gap-2">
                    <p class="text-3xl mb-2 text-left font-bold">
                        {{ team.name }}
                    </p>
                    <div class="flex grow justify-end gap-4">
                        <span
                            @click="edit(key)"
                            class="text-sm text-white font-bold cursor-pointer text-right"
                            >edit</span
                        >
                        <span
                            @click="remove(key)"
                            class="text-sm text-white font-bold cursor-pointer text-right"
                            >remove</span
                        >
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

        <div class="flex gap-6 justify-center mb-8" v-if="teams.length > 0">
            <div class="border-green-400! basis-[50%]">
                <p
                    class="text-xl font-bold mb-6"
                    v-if="firstTeamBettor.length > 0"
                >
                    List of bettors on team {{ teams[0].name }}
                </p>
                <div
                    class="flex gap-2 w-full mb-2 items-center"
                    v-for="(bettor, key) in firstTeamBettor"
                >
                    <p class="text-md flex-1 text-left">{{ key + 1 }}.</p>
                    <p class="text-md flex-1 whitespace-nowrap text-left">
                        {{ bettor.name }}
                    </p>
                    <p class="text-md flex-1 text-right">
                        {{ bettor.amount.toLocaleString() }}
                    </p>
                    <p
                        class="text-md flex-1 text-left font-bold overflow-ellipsis"
                        :class="{
                            'text-red-400': bettor.status === 0,
                            'text-blue-400': bettor.status === 1,
                            'text-green-400': bettor.status === 2,
                        }"
                    >
                        {{ statusMap[bettor.status] }}
                    </p>
                    <div class="flex flex-1 gap-2 justify-end">
                        <span
                            @click="edit(key)"
                            class="text-sm text-blue-400 font-bold cursor-pointer text-right"
                            >edit</span
                        >
                        <span
                            @click="remove(key)"
                            class="text-sm text-red-400 font-bold cursor-pointer text-right"
                            >remove</span
                        >
                    </div>
                </div>
            </div>

            <div class="border-green-400! basis-[50%]">
                <p
                    class="text-xl font-bold mb-6"
                    v-if="secondTeamBettor.length > 0"
                >
                    List of bettors on team {{ teams[1].name }}
                </p>
                <div
                    class="flex gap-2 w-full mb-2 items-center"
                    v-for="(bettor, key) in secondTeamBettor"
                >
                    <p class="text-md flex-1 text-left">{{ key + 1 }}.</p>
                    <p class="text-md flex-1 whitespace-nowrap text-left">
                        {{ bettor.name }}
                    </p>
                    <p class="text-md flex-1 text-right">
                        {{ bettor.amount.toLocaleString() }}
                    </p>
                    <p
                        class="text-md flex-1 text-left font-bold overflow-ellipsis"
                        :class="{
                            'text-red-400': bettor.status === 0,
                            'text-blue-400': bettor.status === 1,
                            'text-green-400': bettor.status === 2,
                        }"
                    >
                        {{ statusMap[bettor.status] }}
                    </p>
                    <div class="flex flex-1 gap-2 justify-end">
                        <span
                            @click="edit(key)"
                            class="text-sm text-blue-400 font-bold cursor-pointer text-right"
                            >edit</span
                        >
                        <span
                            @click="remove(key)"
                            class="text-sm text-red-400 font-bold cursor-pointer text-right"
                            >remove</span
                        >
                    </div>
                </div>
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
