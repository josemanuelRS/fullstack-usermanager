<template>
	<div v-if="props.isVisible" class="modal">
		<div class="modal-content">
			<h3>Are you sure you want to delete this user?</h3>
			<p>This action is irreversible.</p>
			<button @click="confirmDelete" class="delete">Delete User</button>
			<button @click="cancelDelete">Cancel</button>
		</div>
	</div>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue';

const props = defineProps({
	isVisible: Boolean,
	userId: Number
});

const emits = defineEmits(['confirm', 'cancel']);

const confirmDelete = () => {
	emits('confirm', props.userId);
};

const cancelDelete = () => {
	emits('cancel');
};
</script>

<style scoped>

</style>