<script setup lang="ts">

import {useStore} from "vuex";
import CHeading from "../../../components/CHeading.vue";
import {onMounted, ref} from "vue";
import CChangeProfessor from "../components/CChangeProfessor.vue";
import {Professor} from "../../../types/common.types.ts";
import CChangeAdd from "../components/CChangeAdd.vue";
import {generateRandomString} from "../../../helpers/helpFunctions.helper.ts";
import CButton from "../../../components/CButton.vue";

const store = useStore();
const professors = ref<Professor[]>();

onMounted(() => {
    professors.value = store.getters["scheduleSettings/getProfessors"];
})

const remove = (id) => {
    professors.value =
        professors.value?.filter(
            (professor:Professor) =>
                professor.id !== id);
}

const add = () => {
    const emptyProfessor:Professor = {
        id: generateRandomString(),
        name: ''
    }

    if (professors.value?.find(professor => professor.name === '')) {
        store.dispatch('toast/doToast', {
            text: 'Заполните Всех Преподавателей',
            type: 'WARNING'
        })

        return;
    }

    professors.value?.push(emptyProfessor)
}

const save = () => {
    store.dispatch('scheduleSettings/updateProfessors', professors.value)
}


</script>

<template>

    <c-heading
        h="h1"
        type="LARGE"
    >
        Преподаватели
    </c-heading>

    <div class="change__professors__list">
        <c-change-professor
            v-for="professor in professors"

            :professor="professor"
            :removeProfessor="remove"
        />
    </div>


    <div class="change__professors__buttons">
        <c-change-add :add="add" />
        <c-button type="common" @click="save">
            Сохранить
        </c-button>
    </div>

</template>

<style scoped lang="scss">
@import "src/styles/mixins/position.in.block";

.change__professors__list {
    @include centralize-selects;
}

.change__professors__buttons {
    @include centralize-buttons;
}

</style>