<script setup lang="ts">

import {useStore} from "vuex";
import {onMounted, ref} from "vue";
import {LessonTime} from "../../../types/common.types.ts";
import CHeading from "../../../components/CHeading.vue";
import CChangeLessonTime from "../components/CChangeLessonTime.vue";
import CChangeAdd from "../components/CChangeAdd.vue";
import CButton from "../../../components/CButton.vue";

const store = useStore();
const lessonTimes = ref<LessonTime[]>();

onMounted(() => {
    lessonTimes.value = store.getters["scheduleSettings/getLessonTimes"];
});

const remove = (order: number) => {
    const lessonTimesWithRemovedElement = lessonTimes.value
        ?.filter(lessonTime => lessonTime.order !== order);

    lessonTimes.value = lessonTimesWithRemovedElement
        .map((lessonTime, index) => {
            lessonTime.order = index + 1
            return lessonTime;
        });
}

const add = () => {
    const emptyLessonTime:LessonTime = {
        order: lessonTimes.value.length + 1,
        timeStart: '',
        timeEnd: ''
    }

    if (lessonTimes.value?.find(lessonTime =>
            lessonTime.timeStart === '' ||
            lessonTime.timeEnd === ''
        )
    ) {
        store.dispatch('toast/doToast', {
            text: 'Заполните Все Времена Занятий',
            type: 'WARNING'
        })

        return;
    }

    lessonTimes.value?.push(emptyLessonTime);
}

const save = () => {
    store.dispatch("scheduleSettings/updateLessonTimes", lessonTimes.value);
}

</script>

<template>

    <c-heading
        h="h1"
        type="LARGE"
    >
        Время Занятий
    </c-heading>

    <div class="change__lessons-times__list">
        <c-change-lesson-time
            v-for="lessonTime in lessonTimes"
            :lesson-time="lessonTime"
            :remove-lesson-time="remove"
        />
    </div>

    <div class="change__lessons-times__buttons">
        <c-change-add :add="add" />
        <c-button type="common" @click="save">
            Сохранить
        </c-button>
    </div>

</template>

<style scoped lang="scss">
@import "src/styles/mixins/position.in.block";

.change__lessons-times__list {
    display: flex;

    flex-direction: column;

    gap: 15px;
    padding: 15px 0;
}

.change__lessons-times__buttons {
    @include centralize-buttons;
}
</style>