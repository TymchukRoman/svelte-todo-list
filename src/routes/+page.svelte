<script lang="ts">
	import { onMount } from 'svelte';
	import type { ToDoItem } from '../app';

	export let todos: ToDoItem[] = [];
	export let sorting: string = '';

	onMount(async () => {
		const todosStringified: string | null = localStorage.getItem('todos');
		if (!todosStringified) return false;
		todos = JSON.parse(todosStringified);
	});

	export const STATUSES: {
		TODO: string;
		INPROGRESS: string;
		DONE: string;
	} = {
		TODO: 'to do',
		INPROGRESS: 'in progress',
		DONE: 'done'
	};

	export let title: string = '';
	export let description: string = '';

	export let sortingChange = () => {
		switch (sorting) {
			case 'statusdesc':
				todos = todos.sort((a: ToDoItem, b: ToDoItem) => (a.status > b.status ? 1 : -1));
				return true;
			case 'statusasc':
				todos = todos.sort((a: ToDoItem, b: ToDoItem) => (a.status < b.status ? 1 : -1));
				return true;
			default:
				return false;
		}
	};

	export const addToDoItem = () => {
		const newItem: ToDoItem = {
			id: new Date().valueOf(),
			title,
			description,
			status: STATUSES.TODO
		};
		title = '';
		description = '';
		todos = [...todos, newItem];
		writeTodos();
	};

	export const deleteItem = (id: number) => () => {
		todos = [...todos.filter((todo: ToDoItem) => todo.id !== id)];
	};

	export const writeTodos = () => {
		localStorage.setItem('todos', JSON.stringify(todos));
	};

	export const getStatusColor = (status: string) => {
		switch (status) {
			case STATUSES.TODO:
				return 'todo-status-item';
			case STATUSES.INPROGRESS:
				return 'inprogress-status-item';
			case STATUSES.DONE:
				return 'done-status-item';
			default:
				return 'todo-status-item';
		}
	};
</script>

<div class="main-container">
	<h1>To do list on Svelte!</h1>

	<div class="header-container">
		<div class="header-left">
			<input placeholder="Task title" bind:value={title} />
			<input placeholder="Task description" bind:value={description} />
			<button on:click={addToDoItem}>Add</button>
		</div>
		<div>
			<select name="status" bind:value={sorting} on:change={sortingChange}>
				<option value="statusdesc"> Status DESC</option>
				<option value="statusasc"> Status ASC</option>
				<option value=""> No sorting</option>
			</select>
		</div>
	</div>

	{#if !todos.length}
		<p>State is empty!</p>
	{:else}
		<div class="todo-container">
			{#each todos as todo}
				<div class={`todo-item ${getStatusColor(todo.status)}`}>
					<h3>{todo.title}</h3>
					<p>{todo.description}</p>
					<select name="status" bind:value={todo.status} on:change={writeTodos}>
						<option value={STATUSES.TODO}> To Do </option>
						<option value={STATUSES.INPROGRESS}> In progress </option>
						<option value={STATUSES.DONE}> Done </option>
					</select>
					<button on:click={deleteItem(todo.id)}>Delete</button>
				</div>
			{/each}
		</div>
	{/if}
</div>

<style>
	.main-container {
		width: 100%;
	}

	.todo-container {
		display: flex;
		padding: 10px;
		flex-wrap: wrap;
		row-gap: 20px;
		column-gap: 20px;
	}

	.header-container {
		display: flex;
		justify-content: space-between;
	}

	.header-left {
		display: flex;
		column-gap: 10px;
	}

	.todo-item {
		border: 1px solid black;
		border-radius: 5px;
		padding: 10px;
		max-width: 15%;
	}

	.todo-status-item {
		background-color: #c2c8d2;
	}

	.inprogress-status-item {
		background-color: #99b9e9;
	}

	.done-status-item {
		background-color: #51bc8f;
	}
</style>
