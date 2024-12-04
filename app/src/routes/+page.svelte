<script>
	import { writable } from 'svelte/store';

	const categories = [
		{
			name: 'Ugodnost i sigurnost u školi',
			start: 0,
			end: 6,
			label: 'Koliko se ugodno osjećaš u školi'
		},
		{
			name: 'Očekivanja uspjeha',
			start: 7,
			end: 10,
			label: 'Koliko su visoka tvoja očekivanja uspjeha'
		},
		{
			name: 'Procjena vlastite uspješnosti',
			start: 11,
			end: 14,
			label: 'Koliko se procjenjuješ uspješnim'
		},
		{
			name: 'Dubina učenja',
			start: 15,
			end: 23,
			label: 'Koliko duboko učiš'
		}
	];

	const questions = [
		'U školi se osjećam sigurno i zaštićeno.',
		'Nastavnici me poštuju i uvažavaju moje mišljenje.',
		'Kada imam problem, u školi se mogu obratiti nekome za pomoć.',
		'Nastavnici potiču moju kreativnost i samopouzdanje.',
		'Atmosfera na satu pomaže mi da se osjećam ugodno i motivirano za učenje. ',
		'Zadovoljan/na sam načinom na koji se rješavaju sukobi među učenicima u školi.',
		'Osjećam se kao dio zajednice u školi. ',
		'Vjerujem da mogu postići rezultate kojima sam zadovoljan u školi.',
		'Vjerujem da mogu biti uspješan u skoro svim školskim predmetima.',
		'Vjerujem da ću sve školske obveze uspješno riješiti.',
		'Vjerujem da će mi trud u školi pomoći da ostvarim svoje ciljeve u budućnosti.',
		'Uspješno rješavam testove u školi.',
		'Kada me ispituju, pokazujem dobro znanje.',
		'Ostvarujem dobre rezultate u projektnim radovima.',
		'Mojim uspjehom u školi mogu zadovoljiti očekivanja roditelja.',
		'Mojim uspjehom u školi mogu zadovoljiti očekivanja nastavnika.',
		'Kada učim, pokušavam utvrditi postoji li nešto što nisam dobro razumio/la.',
		'Nakon učenja provjerim svoje znanje i razumijevanje gradiva.',
		'Da bih se spremio/la za ispit, pročitam bilješnicu ili knjigu više puta ili riješim puno zadataka.',
		'Opsežne tekstove pokušavam sažeti i osmisliti u par važnih rečenica ili natuknica.',
		'Učeći pokušavam razlikovati važne od nevažnih informacija.',
		'Pokušavam osmisliti kako se sadržaji koje učim mogu podijeliti u smislene grupe.',
		'Dok učim, ponekad dobijem vlastitu ideju kako bi nešto moglo biti bolje napravljeno.',
		'Kada nastavnik/ca nešto objasni i ponudi rješenje, zapitam se može li se to riješiti i na drugačiji način.',
		'Kada god je to moguće, pokušavam povezati gradivo koje učim s onim što sam naučio na drugim predmetima ili što sam saznao od drugih.'
	];

	const options = [
		{ label: 'Strogo se ne slažem', value: 1 },
		{ label: 'Ne slažem se', value: 2 },
		{ label: 'Neutralan sam', value: 3 },
		{ label: 'Slažem se', value: 4 },
		{ label: 'Snažno se slažem', value: 5 }
	];

	const themes = {
		forest: 'bg-gradient-to-r from-green-700 to-green-900 text-gray-800'
	};

	const buttonThemes = {
		forest: 'bg-green-800 hover:bg-green-900'
	};

	let selectedTheme = 'forest';
	let currentQuestion = 0;
	let currentCategoryIndex = 0;
	const responses = writable([]);
	let selectedValue = null;
	let showResult = false;
	let totalScore = 0;
	let categoryScores = [];


	let demographicsCompleted = false;
	let selectedGrade = '';
	let selectedGender = '';

	function calculateCategoryScores() {
		categoryScores = categories.map((category) => {
			const categoryResponses = $responses.slice(category.start, category.end + 1);
			const maxPossibleScore = (category.end - category.start + 1) * 5;
			const actualScore = categoryResponses.reduce((a, b) => a + b, 0);
			return {
				name: category.name,
				label: category.label,
				percentage: Math.round((actualScore / maxPossibleScore) * 100)
			};
		});
	}

	function submitDemographics() {
		if (selectedGrade && selectedGender) {
			demographicsCompleted = true;
		}
	}

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

			if (currentQuestion > categories[currentCategoryIndex].end) {
				currentCategoryIndex += 1;
			}
		} else {
			totalScore = $responses.reduce((a, b) => a + b, 0);
			calculateCategoryScores();
			showResult = true;
		}
	}

	function restartSurvey() {
		responses.set([]);
		currentQuestion = 0;
		currentCategoryIndex = 0;
		showResult = false;
		totalScore = 0;
		categoryScores = [];
		demographicsCompleted = false;
		selectedGrade = '';
		selectedGender = '';
	}
</script>

<div class="flex min-h-screen items-center justify-center p-4 transition-colors duration-1000 {themes[selectedTheme]}">
	<div class="w-full max-w-md rounded-lg p-6 shadow-lg backdrop-blur-lg bg-white bg-opacity-90">
		{#if !demographicsCompleted}
			<div class="demographics-form">
				<h2 class="text-2xl mb-6 text-center">Osobni podaci</h2>

				<div class="mb-6">
					<label class="block mb-2">Odaberi razred:</label>
					<select
						class="w-full rounded border border-gray-300 px-3 py-2 focus:outline-none focus:ring-2 focus:ring-green-600"
						bind:value={selectedGrade}
					>
						<option value="" disabled selected>Odaberite...</option>
						<option value="1.">1.</option>
						<option value="2.">2.</option>
						<option value="3.">3.</option>
						<option value="4.">4.</option>
					</select>
				</div>

				<div class="mb-6">
					<label class="block mb-2">Odaberi spol:</label>
					<select
						class="w-full rounded border border-gray-300 px-3 py-2 focus:outline-none focus:ring-2 focus:ring-green-600"
						bind:value={selectedGender}
					>
						<option value="" disabled selected>Odaberite...</option>
						<option value="Muško">Muško</option>
						<option value="Žensko">Žensko</option>
						<option value="Ne želim odgovoriti">Ne želim odgovoriti</option>
					</select>
				</div>

				<button
					on:click={submitDemographics}
					class="w-full rounded px-4 py-2 transition duration-300 text-white {buttonThemes[selectedTheme]}"
					disabled={!selectedGrade || !selectedGender}
				>
					Započni anketu
				</button>
			</div>
		{:else}
			<div class="flex justify-center mb-6">
				{#each categories as category, index}
					<div
						class="w-4 h-4 rounded-full mx-2 transition-colors duration-300 {
							index < currentCategoryIndex
								? 'bg-green-800'
								: (index === currentCategoryIndex
									? 'bg-green-600'
									: 'bg-gray-300')
						}"
					></div>
				{/each}
			</div>

			<div class="relative">
				{#if !showResult}
					{#key currentQuestion}
						<div class="question-content">
							<p class="mb-6 text-center text-xl">{questions[currentQuestion]}</p>

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
								class="mt-6 w-full rounded px-4 py-2 transition duration-300 text-white {buttonThemes[selectedTheme]}"
								disabled={selectedValue === null}
							>
								{currentQuestion === questions.length - 1 ? 'Predaj' : 'Dalje'}
							</button>
						</div>
					{/key}
				{:else}
					<div class="result-content">
						<h2 class="mb-4 text-2xl font-semibold">Hvala vam!</h2>
						<p class="mb-6">Rezultati po kategorijama:</p>
						<div class="space-y-4">
							{#each categoryScores as category}
								<div class="bg-green-100 p-4 rounded-lg">
									<p class="text-lg">{category.label}:</p>
									<p class="text-3xl font-bold text-green-800">{category.percentage} %</p>
								</div>
							{/each}
						</div>
						<button
							on:click={restartSurvey}
							class="mt-6 w-full rounded px-4 py-2 transition duration-300 text-white {buttonThemes[selectedTheme]}"
						>
							Ponovno pokreni anketu
						</button>
					</div>
				{/if}
			</div>
		{/if}
	</div>
</div>

<style>
	.question-content {
		animation: fadeIn 1s ease-in-out;
	}

	.result-content {
		animation: fadeIn 1s ease-in-out;
	}

	.demographics-form {
		animation: fadeIn 1s ease-in-out;
	}

	@keyframes fadeIn {
		from {
			opacity: 0;
		}
		to {
			opacity: 1;
		}
	}

	body {
		margin: 0;
		font-family: Arial, sans-serif;
	}

	select {
		appearance: none;
		background-color: white;
		background-image: url("data:image/svg+xml;charset=US-ASCII,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 4 5'%3E%3Cpath fill='%23999' d='M2 0L0 2h4zm0 5L0 3h4z'/%3E%3C/svg%3E");
		background-repeat: no-repeat;
		background-position: right 0.5rem center;
		background-size: 0.65em auto;
	}
</style>