<script setup lang="ts">
  import { ref } from 'vue';
  import BaseButton from '../core/BaseButton.vue';
  import ModalifyComponent from '../core/ModalifyComponent.vue';
  import { NotificationType, useNotifyStore } from '../../stores/notifyStore';
  import questionApi from '../../services/questionApi';
  import { Question, useRetrospectiveStore } from '../../stores/retrospectiveStore';

  const retroStore = useRetrospectiveStore();
  const notifyStore = useNotifyStore();

  const isOpen = ref(false);

  const props = defineProps<{
    question: Question;
    questionIndex: number;
  }>();

  const deleteQuestion = async () => {
    const res = await questionApi.deleteQuestion(props.question.id);

    if (res?.error)
      return notifyStore.notify('An error occured to delete the question', NotificationType.Error);

    retroStore.question.deleteQuestion(props.question);

    isOpen.value = !isOpen.value;
  };
</script>

<template>
  <ModalifyComponent v-if="isOpen" @close="isOpen = !isOpen">
    <div class="flex flex-col gap-3 cursor-default">
      <p class="block text-md font-bold text-gray-900">{{ `Q${questionIndex}. Confirm delete` }}</p>
      <span>
        Are you sure you want to delete this question? Note that this action is irreversible, and
        will pulverize all answers
      </span>
      <div class="flex flex-row gap-2 justify-end">
        <BaseButton @click="isOpen = !isOpen">Cancel</BaseButton>
        <BaseButton :style="'RED'" @click="deleteQuestion">Delete</BaseButton>
      </div>
    </div>
  </ModalifyComponent>

  <BaseButton :style="'RED'" @click="isOpen = !isOpen"><span>Delete question</span></BaseButton>
</template>
