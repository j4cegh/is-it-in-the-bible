<script lang="ts">
	import axios from 'axios';

	const API_URL = import.meta.env.DEV
		? `http://127.0.0.1:5000/bible?search=`
		: 'https://api.i3tb.j4ce.xyz/bible?search=';

	let search: string;
	let hasLooked: boolean = false;
	let verseInfo: {
		verseAvailable: boolean;
		inBible: boolean;
		verse: string | null;
	} = { verseAvailable: false, inBible: false, verse: null };

	interface VerseResponse {
		error?: string;
		verse: string;
	}

	async function getVerse(search: string): Promise<string | null> {
		const response = await axios.get(API_URL + search);
		const { error, verse }: VerseResponse = response.data;

		if (error) {
			return null;
		}

		return verse;
	}

	const searchSubmit = async () => {
		verseInfo = {
			verseAvailable: false,
			inBible: false,
			verse: null
		};

		let verse = await getVerse(search);

		verseInfo = {
			verseAvailable: verse != null,
			inBible: verse != null,
			verse: verse
		};

		hasLooked = true;
	};
</script>

<div class="flex flex-col justify-center mt-[250px] text-center">
	<main>
		<h1 class="text-white text-5xl">Is it in the bible?</h1>
		<div class="mt-2.5">
			<input
				bind:value={search}
				on:keypress={(event) => event.key == 'Enter' && searchSubmit()}
				type="text"
				placeholder="Enter a word, a sentence, etc."
				class=" mt-2.5 w-[500px] rounded-md border border-[#e0e0e0] bg-white py-3 px-6 text-base font-medium text-[#6B7280] outline-none focus:border-[#6A64F1] focus:shadow-md"
			/>
		</div>
		{#if verseInfo.verseAvailable}
			<div class="flex flex-col mt-20">
				<span class="text-5xl text-green-500">It's in the bible!</span>
				<span class="text-3xl text-white mt-2.5">
					{verseInfo.verse}
				</span>
			</div>
		{:else if !verseInfo.verseAvailable && hasLooked}
			<div class="flex flex-col mt-10">
				<span class="text-5xl text-red-500">It's not in the bible!</span>
				<span class="text-3xl text-white mt-2.5">{':('}</span>
			</div>
		{/if}
	</main>
	<div class="fixed inset-x-0 bottom-0">
		<h1 class="text-white">
			Made with <a class="underline" href="https://github.com/j4cegh/create-untitled-app"
				>create-untitled-app</a
			>
		</h1>
	</div>
</div>
