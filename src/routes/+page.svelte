<script lang="ts">
	import { onMount } from 'svelte';
	import { getPropsObj } from '$lib/internal/helpers';
	import { schemas } from './(previews)/schemas';

	import Copy from '~icons/lucide/copy';
	import Check from '~icons/lucide/check';
	import ArrowRight from '~icons/lucide/arrow-right';

	// eslint-disable-next-line @typescript-eslint/no-explicit-any
	function getPropsObjForSchema(schema: (typeof schemas)[keyof typeof schemas]): any {
		// eslint-disable-next-line @typescript-eslint/ban-types
		return getPropsObj<{}>(schema.meta);
	}

	$: sortedSchemas = Object.entries(schemas).sort((a, b) => {
		return a[1].title.toLowerCase().localeCompare(b[1].title.toLowerCase());
	});

	let copied = false;
	function copyInstallCommand() {
		navigator.clipboard.writeText(`npm install radix-svelte`);
		copied = true;
		setTimeout(() => {
			copied = false;
		}, 2500);
	}

	onMount(() => {
		const blob = document.getElementById('blob');

		window.onpointermove = (event) => {
			const { clientX, clientY } = event;

			if (blob) {
				blob.animate(
					{
						left: `${clientX}px`,
						top: `${clientY}px`,
					},
					{ duration: 3000, fill: 'forwards' }
				);
			}
		};
	});
</script>

<div id="blob-container">
	<div id="blob" />
</div>
<div id="blur" />

<div class="relative grid grow place-items-center p-6">
	<div class="grid grid-cols-1 gap-8 lg:grid-cols-2 xl:grid-cols-3">
		<div class="col-span-full flex flex-col gap-4 py-24">
			<h1 class="text-4xl font-bold text-white lg:text-5xl">Don’t reinvent the wheel.</h1>
			<p class="text-lg text-white opacity-75 lg:text-xl">
				Unstyled, accessible components for building high‑quality design systems and web apps, now
				in Svelte.
			</p>
			<div class="flex flex-row gap-4">
				<a
					href="/docs/accordion"
					class="text-md rounded bg-vermilion-600 p-4 font-sans font-semibold text-white transition hover:bg-vermilion-800 active:translate-y-0.5"
				>
					Read the docs
					<ArrowRight class="ml-2 inline-block h-5 w-5 text-white" />
				</a>
				<button
					on:click={copyInstallCommand}
					class="text-md group rounded bg-zinc-900 p-4 font-mono text-white transition hover:bg-zinc-800 active:translate-y-0.5"
					><span>npm install radix-svelte</span>
					{#if copied}
						<Check class="ml-2 inline-block h-5 w-5 text-vermilion-500" />
					{:else}
						<Copy class="ml-2 inline-block h-5 w-5 group-hover:text-vermilion-500" />
					{/if}
				</button>
			</div>
		</div>
		{#each sortedSchemas as [identifier, schema], idx}
			{@const propsObj = getPropsObjForSchema(schema)}
			<div
				class="flex min-h-[256px] w-full flex-col gap-2 overflow-hidden transition lg:h-[512px] lg:max-w-[512px]"
			>
				<a href={`/docs/${identifier}`} class="flex items-baseline justify-between">
					<h2 class="text-xl font-normal capitalize text-white">{schema.title}</h2>
					<span
						class="text-md text-white underline-offset-2 opacity-75 hover:underline hover:opacity-100 focus:underline active:opacity-75"
						>View docs</span
					></a
				>
				<div class="comp-preview grow place-items-center">
					<svelte:component this={schema.example} {propsObj} />
				</div>
			</div>
		{/each}
	</div>
</div>

<style>
	#blob-container {
		position: fixed;
		z-index: -2;
	}

	#blob {
		background-color: white;
		height: 34vmax;
		aspect-ratio: 1;
		position: absolute;
		left: 50%;
		top: 50%;
		translate: -50% -50%;
		border-radius: 50%;
		/* background: linear-gradient(to right, #6ee7b7, #ff590a); */
		background: linear-gradient(90deg, rgba(110, 231, 183, 1) 0%, rgba(255, 89, 10, 1) 60%);
		animation: rotate 20s infinite;
		opacity: 0.8;
	}

	@keyframes rotate {
		from {
			rotate: 0deg;
		}

		50% {
			scale: 1 1.25;
		}

		to {
			rotate: 360deg;
		}
	}

	#blur {
		margin-top: -64px;
		height: 100%;
		width: 100%;
		position: fixed;
		z-index: -1;
		backdrop-filter: blur(12vmax);
	}
</style>
