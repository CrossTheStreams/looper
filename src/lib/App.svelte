<script lang="ts">
    import { onMount, onDestroy } from 'svelte';
    import Loop from './Loop.svelte';
  
    let loops: { notes: boolean[] }[] = [];
    let audioContext: AudioContext;
    let isPlaying: boolean = false;
    let nextNoteTime: number = 0.0; // when the next note is due.
    let lookahead: number = 0.1; // How frequently to call scheduling function (in seconds)
    let noteLength: number = 0.1; // Length of "beep" (in seconds)
    let timer: NodeJS.Timeout;
    let currentNote: number; //the index of the current note
  
    const scheduleInterval: number = 0.25; // quarter notes
  
    onMount(() => {
      audioContext = new (window.AudioContext || window.webkitAudioContext)();
      currentNote = 0;
    });
  
    onDestroy(() => {
      if (timer) clearInterval(timer);
    });
  
    function nextNote() {
      const secondsPerBeat: number = scheduleInterval; // Use the value of scheduleInterval
      nextNoteTime += secondsPerBeat; // Add beat length to last beat time
      currentNote++; // Advance the beat number, wrap to zero
      if (currentNote === 8) {
        currentNote = 0;
      }
    }
  
    function scheduleNote() {
      loops.forEach((loop) => {
        if (loop.notes[currentNote]) {
          const osc: OscillatorNode = audioContext.createOscillator();
          const convolver = audioContext.createConvolver();
          osc.connect(audioContext.destination);
          osc.frequency.value = 110.0 * (currentNote + 1); // Different frequencies for each note
          osc.start(nextNoteTime);
          osc.stop(nextNoteTime + noteLength);
        }
      });
    }
  
    function scheduler() {
      while (nextNoteTime < audioContext.currentTime + lookahead) {
        scheduleNote();
        nextNote();
      }
    }
  
    function playLoop() {
      isPlaying = !isPlaying;
  
      if (isPlaying) {
        currentNote = 0;
        nextNoteTime = audioContext.currentTime;
        timer = setInterval(scheduler, lookahead * 1000);
      } else {
        clearInterval(timer);
      }
    }
  </script>
  
  <button on:click={() => loops = [...loops, { notes: Array(8).fill(false) }]}>Add Loop</button>
  <button on:click={playLoop}>{isPlaying ? 'Stop' : 'Play'}</button>
  <br />
  <br />
  {#each loops as loop, index (index)}
    <Loop {loop} /><br/>
  {/each}
  