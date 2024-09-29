<script lang="ts">
	let textInput: string = '';
	let currentWord: string = '';
	let words: string[] = [];
	let wpm: number = 300;
	let isReading: boolean = false;
	let index: number = 0;
	let timer: NodeJS.Timeout | null = null;

	function startReading() {
		if (!isReading) {
			if (textInput.trim() && index === 0) {
				words = textInput.trim().split(/\s+/);
			}
			isReading = true;
			runRSVP();
		}
	}

	function runRSVP() {
		const interval = (60 / wpm) * 1000;
		timer = setInterval(() => {
			if (index < words.length) {
				currentWord = words[index];
				index++;
			} else {
				stopReading();
			}
		}, interval);
	}

	function pauseReading() {
		if (timer !== null) {
			clearInterval(timer);
			timer = null;
		}
		isReading = false;
	}

	function stopReading() {
		isReading = false;
		if (timer !== null) {
			clearInterval(timer);
			timer = null;
		}
	}

	function resetReading() {
		stopReading();
		currentWord = '';
		index = 0;
	}
</script>

<div class="flex flex-col space-y-4 h-screen">
	<div class="items-center justify-center">
		<div class="text-3xl text-center py-4">
			{#if currentWord}
				<span class="text-8xl">{currentWord}</span>
			{:else}
				<textarea
					bind:value={textInput}
					rows="10"
					class="text-black border border-gray-300 p-2 w-1/2"
					placeholder="Paste your text here..."
				></textarea>
			{/if}
		</div>

		<div class="controls flex justify-center items-center space-x-4">
			<div class="flex items-center text-black">
				<label for="wpm" class="text-sm">WPM:</label>
				<input type="number" id="wpm" min="100" max="1000" bind:value={wpm} class="w-20 px-2 py-1 ml-1" />
			</div>

			<button
				type="button"
				class="btn variant-filled rounded-lg"
				color={isReading ? 'secondary' : 'primary'}
				on:click={isReading ? pauseReading : startReading}
			>
				{isReading ? 'Pause' : 'Start'}
			</button>

			<button
				type="button"
				class="btn variant-filled rounded-lg"
				color="error"
				on:click={resetReading}
				disabled={!isReading && index === 0}
			>
				Reset
			</button>
		</div>
	</div>
</div>
