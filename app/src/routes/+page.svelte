<script>
	import { writable } from 'svelte/store';

	const questions = [
		'Jednostavno se snalazim na web stranici.',
		'Sadržaj je jasan i informativan.',
		'Dizajn je vizualno privlačan.',
		'Zadovoljan sam radom web stranice.',
		'Preporučio bih ovu web stranicu drugima.'
	];

	const options = [
		{ label: 'Strogo se ne slažem', value: 1 },
		{ label: 'Ne slažem se', value: 2 },
		{ label: 'Neutralan sam', value: 3 },
		{ label: 'Slažem se', value: 4 },
		{ label: 'Snažno se slažem', value: 5 }
	];

	const themes = {
		sunrise: 'bg-gradient-to-r from-yellow-300 via-orange-400 to-red-500 text-gray-800',
		twilight: 'bg-gradient-to-r from-purple-800 to-blue-900 text-gray-800',
		oceanic: 'bg-gradient-to-r from-teal-400 to-blue-600 text-gray-800',
		forest: 'bg-gradient-to-r from-green-700 to-green-900 text-gray-800',
		solarflare: 'bg-gradient-to-r from-yellow-500 to-red-600 text-gray-800',
		nebula: 'bg-gradient-to-r from-purple-900 to-pink-700 text-gray-800'
	};

	const buttonThemes = {
		sunrise: 'bg-orange-500 hover:bg-orange-600',
		twilight: 'bg-purple-700 hover:bg-purple-800',
		oceanic: 'bg-teal-500 hover:bg-teal-600',
		forest: 'bg-green-800 hover:bg-green-900',
		solarflare: 'bg-red-500 hover:bg-red-600',
		nebula: 'bg-pink-700 hover:bg-pink-800'
	};

	let selectedTheme = 'sunrise';
	let currentQuestion = 0;
	const responses = writable([]);
	let selectedValue = null;
	let showResult = false;
	let totalScore = 0;

	function submitResponse() {
		if (selectedValue === null) return;

		responses.update((n) => {
			const updated = [...n];
			updated[currentQuestion] = selectedValue;
			return updated;
		});

		selectedValue = null;

		if (currentQuestion < questions.length - 1) {
			currentQuestion += 1;
		} else {
			responses.subscribe((values) => {
				totalScore = values.reduce((a, b) => a + b, 0);
			})();
			showResult = true;
		}
	}

	function restartSurvey() {
		responses.set([]);
		currentQuestion = 0;
		showResult = false;
		totalScore = 0;
	}
</script>

<div class={`flex min-h-screen items-center justify-center p-4 transition-colors duration-1000 ${themes[selectedTheme]}`}>
	<div class="absolute top-4 left-4 flex gap-4">
		<select bind:value={selectedTheme} class="p-2 rounded bg-white shadow transition-colors duration-1000">
			<option value="sunrise">Izlazak sunca</option>
			<option value="twilight">Sumrak</option>
			<option value="oceanic">Oceanski</option>
			<option value="forest">Šuma</option>
			<option value="solarflare">Sunčev bljesak</option>
			<option value="nebula">Maglica</option>
		</select>
	</div>

	<div class="w-full max-w-md rounded-lg p-6 shadow-lg backdrop-blur-lg bg-white bg-opacity-90">
		<div class="relative">
			{#if !showResult}
				{#key currentQuestion}
					<div class="question-content">
						<h2 class="mb-4 text-2xl font-semibold">Anketa</h2>
						<p class="mb-6">Pitanje {currentQuestion + 1} od {questions.length}</p>
						<p class="mb-6">{questions[currentQuestion]}</p>

						<div class="space-y-4">
							{#each options as option}
								<label class="flex items-center space-x-3">
									<input
										type="radio"
										name="likert"
										value={option.value}
										class="form-radio h-5 w-5 text-pink-600"
										bind:group={selectedValue}
									/>
									<span class="text-gray-800">{option.label}</span>
								</label>
							{/each}
						</div>

						<button
							on:click={submitResponse}
							class={`mt-6 w-full rounded px-4 py-2 transition duration-300 text-white ${buttonThemes[selectedTheme]}`}
							disabled={selectedValue === null}
						>
							{currentQuestion === questions.length - 1 ? 'Predaj' : 'Dalje'}
						</button>
					</div>
				{/key}
			{:else}
				<div class="result-content">
					<h2 class="mb-4 text-2xl font-semibold">Hvala vam!</h2>
					<p class="mb-6">Vaš ukupni rezultat je:</p>
					<p class="mb-6 text-4xl font-bold text-pink-600">{totalScore} / {questions.length * 5}</p>
					<button
						on:click={restartSurvey}
						class={`mt-6 rounded px-4 py-2 transition duration-300 text-white ${buttonThemes[selectedTheme]}`}
					>
						Ponovno pokreni anketu
					</button>
				</div>
			{/if}
		</div>
	</div>
</div>

<style>
	/* Animacija za sadržaj pitanja */
	.question-content {
		animation: fadeIn 1s ease-in-out;
	}

	/* Animacija za sadržaj rezultata */
	.result-content {
		animation: fadeIn 1s ease-in-out;
	}

	/* Fade animacija */
	@keyframes fadeIn {
		from {
			opacity: 0;
		}
		to {
			opacity: 1;
		}
	}

	/* Dodatno stiliziranje */
	body {
		margin: 0;
		font-family: Arial, sans-serif;
	}

	/* Održavanje vidljivosti radio gumba */
	input[type="radio"] {
		opacity: 1 !important;
	}
</style>
