<script lang="ts">
	import Note from './Note.svelte';
	export let loop: { notes: boolean[] };

	function addNote() {
		loop.notes = [...loop.notes, false];
	}

	function deleteNote(index: number) {
		loop.notes = loop.notes.filter((_, i) => i !== index);
	}

	function toggleMute(index: number) {
		loop.notes[index] = !loop.notes[index];
	}
</script>

<button on:click={addNote}>Add Note</button>
{#each loop.notes as note, index (index)}
	<Note {note} on:click={() => toggleMute(index)} on:delete={() => deleteNote(index)} />
{/each}
