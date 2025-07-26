<script setup>
import { ref } from 'vue'
import TeamForm from './components/TeamForm.vue'

const teams = ref([])
const firstTeamBettor = ref([])
const secondTeamBettor = ref([])
const openForm = ref(false)

const teamFormSubmit = (obj) => {
    teams.value.push(obj)
    openForm.value = false
}

const remove = (index) => {
    teams.value.splice(index, 1)
}
</script>

<template>
    <div class="h-full p-[20px] flex flex-col">
        <span class="text-5xl mb-8">Pustahan Nights</span>

        <div class="flex gap-6 justify-center mb-8">
            <button v-if="teams.length < 2" @click="openForm = true">
                Add Team Roster
            </button>

            <button
                v-if="teams.length >= 2"
                class="border-green-400! text-green-400! basis-[50%] hover:shadow-md hover:shadow-green-400/20"
                @click="openForm = true"
            >
                Add Bettor on {{ teams[0].name }}
            </button>

            <button
                v-if="teams.length >= 2"
                class="border-red-400! text-red-400! basis-[50%] hover:shadow-md shadow-red-400/20"
                @click="openForm = true"
            >
                Add Bettor on {{ teams[1].name }}
            </button>
        </div>

        <div class="flex gap-6" v-if="teams.length > 0">
            <div
                class="flex flex-col border basis-[50%] p-4 rounded-xl gap-2 w-xs"
                :class="{
                    'border-green-400': key === 0,
                    'border-red-400': key === 1,
                }"
                v-for="(team, key) in teams"
            >
                <div class="flex gap-2">
                    <p class="text-3xl mb-2 text-left font-bold">
                        {{ team.name }}
                    </p>
                    <div class="flex grow justify-end gap-4">
                        <span
                            @click=""
                            class="text-sm text-blue-500 cursor-pointer text-right"
                            >edit</span
                        >
                        <span
                            @click="remove(key)"
                            class="text-sm text-red-500 cursor-pointer text-right"
                            >remove</span
                        >
                    </div>
                </div>
                <div class="flex">
                    <p class="text-xl mr-6">1.</p>
                    <p class="text-xl">{{ team.carry }}</p>
                </div>
                <div class="flex">
                    <p class="text-xl mr-6">2.</p>
                    <p class="text-xl">{{ team.mid }}</p>
                </div>
                <div class="flex">
                    <p class="text-xl mr-6">3.</p>
                    <p class="text-xl">{{ team.off }}</p>
                </div>
                <div class="flex">
                    <p class="text-xl mr-6">4.</p>
                    <p class="text-xl">{{ team.soft }}</p>
                </div>
                <div class="flex">
                    <p class="text-xl mr-6">5.</p>
                    <p class="text-xl">{{ team.hard }}</p>
                </div>
            </div>
        </div>

        <div
            v-if="openForm"
            class="lala absolute top-0 left-0 h-full w-full m-auto flex flex-col items-center justify-center"
        >
            <TeamForm
                @submit="teamFormSubmit"
                @close="openForm = false"
                class="rounded-xl w-md bg-white z-10"
            ></TeamForm>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.lala {
    background-color: rgba(0, 0, 0, 0.1);
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
}
</style>
