<template>
	<AddTask v-show="showAddTask" @add-task="addTask" />
	<Tasks @toggle-reminder="toggleReminder" @delete-task="deleteTask" :tasks="tasks" />
</template>

<script>
import AddTask from '../components/AddTask';
import Tasks from '../components/Tasks';

export default {
	name: 'Home',
	props: {
		showAddTask: Boolean,
	},
	components: {
		Tasks,
		AddTask,
	},
	data() {
		return {
			tasks: [],
		};
	},
	methods: {
		async addTask(task) {
			const res = await fetch('/api/tasks', {
				method: 'POST',
				headers: {
					'Content-type': 'application/json',
				},
				body: JSON.stringify(task),
			});

			const data = await res.json();
			this.tasks = [...this.tasks, data];
		},
		async deleteTask(id) {
			if (confirm('Are you sure?')) {
				const res = await fetch(`/api/tasks/${id}`, {
					method: 'DELETE',
				});

				res.status === 200 ? (this.tasks = await this.fetchTasks()) : alert('Error deleting this task');
			}
		},
		async toggleReminder(id) {
			const taskToToggle = await this.fetchTask(id);

			const updatedTask = { ...taskToToggle, reminder: !taskToToggle.reminder };

			const res = await fetch(`/api/tasks/${id}`, {
				method: 'PUT',
				headers: {
					'Content-type': 'application/json',
				},
				body: JSON.stringify(updatedTask),
			});

			res.status === 200 ? (this.tasks = await this.fetchTasks()) : alert('Error updating this task');
		},
		async fetchTasks() {
			const res = await fetch('api/tasks');
			const data = await res.json();
			return data;
		},
		async fetchTask(id) {
			const res = await fetch(`api/tasks/${id}`);
			const data = await res.json();
			return data;
		},
	},
	async created() {
		this.tasks = await this.fetchTasks();
	},
};
</script>
