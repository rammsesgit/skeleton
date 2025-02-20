<script lang="ts">
	import DocsShell from '$docs/layouts/DocsShell/DocsShell.svelte';
	import { DocsFeature, type DocsShellSettings } from '$docs/layouts/DocsShell/types';
	import DocsPreview from '$docs/components/DocsPreview/DocsPreview.svelte';
	// Types
	import type { AutocompleteOption } from '$lib/components/Autocomplete/types';
	// Components
	import Autocomplete from '$lib/components/Autocomplete/Autocomplete.svelte';
	import InputChip from '$lib/components/InputChip/InputChip.svelte';
	// Utilities
	import CodeBlock from '$lib/utilities/CodeBlock/CodeBlock.svelte';
	// Sveld
	import sveldAutocomplete from '$lib/components/Autocomplete/Autocomplete.svelte?raw&sveld';

	// Docs Shell
	const settings: DocsShellSettings = {
		feature: DocsFeature.Component,
		name: 'Autocomplete',
		description: 'Displays a list of suggested options.',
		imports: ['Autocomplete'],
		types: ['AutocompleteOption'],
		source: 'components/Autocomplete',
		// aria: 'https://www.w3.org/WAI/ARIA/apg/',
		components: [{ sveld: sveldAutocomplete }],
		keyboard: [
			['<kbd>Tab</kbd>', 'Select the next autocomplete option.'],
			['<kbd>Shift</kbd> + <kbd>Tab</kbd>', 'Select the previous autocomplete option.'],
			['<kbd>Space</kbd> or <kbd>Enter</kbd>', 'Select the current autocomplete option.']
		]
	};

	// Local
	let inputDemo = '';
	let inputWhitelist = '';
	const flavorOptions: AutocompleteOption[] = [
		{ label: 'Vanilla', value: 'vanilla', keywords: 'plain, basic', meta: { healthy: false } },
		{ label: 'Chocolate', value: 'chocolate', keywords: 'dark, white', meta: { healthy: false } },
		{ label: 'Strawberry', value: 'strawberry', keywords: 'fruit', meta: { healthy: true } },
		{ label: 'Neapolitan', value: 'neapolitan', keywords: 'mix, strawberry, chocolate, vanilla', meta: { healthy: false } },
		{ label: 'Pineapple', value: 'pineapple', keywords: 'fruit', meta: { healthy: true } },
		{ label: 'Peach', value: 'peach', keywords: 'fruit', meta: { healthy: true } }
	];
	const flavorWhitelist: string[] = ['neapolitan', 'pineapple', 'peach'];
	let flavorBlacklist: string[] = ['vanilla', 'chocolate'];

	// Input Chip
	let inputChip = '';
	let inputChipList: string[] = ['vanilla', 'chocolate'];

	function onDemoSelection(event: any): void {
		console.log(event.detail);
		inputDemo = event.detail.label;
	}

	function onWhitelistSelect(event: any): void {
		console.log(event.detail);
		inputWhitelist = event.detail.label;
	}

	function onBlacklistSelect(event: any): void {
		console.log(event.detail);
		flavorBlacklist = [event.detail.value];
	}

	function onInputChipSelect(event: any): void {
		// console.log(event.detail);
		if (inputChipList.includes(event.detail.value) === false) {
			inputChipList = [...inputChipList, event.detail.value];
			inputChip = '';
		}
	}
</script>

<DocsShell {settings}>
	<!-- Slot: Sandbox -->
	<svelte:fragment slot="sandbox">
		<DocsPreview>
			<svelte:fragment slot="preview">
				<div class="text-token w-full max-w-sm space-y-2">
					<input class="input" type="search" name="ac-demo" bind:value={inputDemo} placeholder="Search..." />
					<Autocomplete
						bind:input={inputDemo}
						options={flavorOptions}
						class="card w-full max-w-sm max-h-48 p-4 overflow-y-auto"
						on:selection={onDemoSelection}
					/>
				</div>
			</svelte:fragment>
			<svelte:fragment slot="source">
				<p>Create a variable to hold bind your search value.</p>
				<CodeBlock language="ts" code={`let inputDemo = '';`} />
				<p>Provide an array of objects containing <code>label</code> and <code>value</code> keys.</p>
				<CodeBlock
					language="ts"
					code={`
const flavorOptions: AutocompleteOption[] = [
	{ label: 'Vanilla', value: 'vanilla' }, 
	{ label: 'Chocolate', value: 'chocolate' }, 
	{ label: 'Strawberry', value: 'strawberry' }, 
	{ label: 'Neapolitan', value: 'neapolitan' }, 
	{ label: 'Pineapple', value: 'pineapple' }, 
	{ label: 'Peach', value: 'peach' }, 
];
				`}
				/>
				<p>Create a selection event handler, to handle the result of the selected value.</p>
				<CodeBlock
					language="ts"
					code={`
function onDemoSelection(event: any): void {
	inputDemo = event.detail.label;
}
				`}
				/>
				<p>Create your search input and bind the search value.</p>
				<CodeBlock
					language="html"
					code={`<input class="input" type="search" name="demo" bind:value={inputDemo} placeholder="Search..." />`}
				/>
				<p>Implement the autocomplete component.</p>
				<CodeBlock language="html" code={`<Autocomplete bind:input={inputDemo} options={demoOptions} on:selection={onDemoSelection} />`} />
			</svelte:fragment>
		</DocsPreview>
	</svelte:fragment>

	<!-- Slot: Usage -->
	<svelte:fragment slot="usage">
		<!-- prettier-ignore -->
		<p>
			The Autocomplete component does not contain it's own input by default. Instead, by using input binding paired with an <code>on:selection</code> event, you may utilize this component alongside any type of input that takes in suggested values.
		</p>
		<section class="space-y-4">
			<h2>Data Structure</h2>
			<p>
				You may optionally append <code>keywords</code> to provide additional search terms. As well as <code>meta</code> to provide
				arbitrary data - which is not utilizing for filtering. All data option data is returned by <code>on:selection</code>, including
				these.
			</p>
			<CodeBlock
				language="ts"
				code={`
const flavorOptions: AutocompleteOption[] = [
	{ ..., keywords: 'mix, strawberry, chocolate, vanilla' },
	{ ..., meta: { healthy: false } },
];
			`}
			/>
		</section>
		<section class="space-y-4">
			<h2>Whitelist</h2>
			<p>Provide a list of values you wish to whitelist. Only options with a matching value will be displayed.</p>
			<DocsPreview background="neutral" regionFooter="text-center">
				<svelte:fragment slot="preview">
					<div class="text-token w-full max-w-sm space-y-2">
						<input
							class="input autocomplete"
							type="search"
							name="autocomplete-search"
							bind:value={inputWhitelist}
							placeholder="Search..."
						/>
						<Autocomplete
							bind:input={inputWhitelist}
							options={flavorOptions}
							whitelist={flavorWhitelist}
							class="card w-full max-w-sm max-h-48 p-4 overflow-y-auto"
							on:selection={onWhitelistSelect}
						/>
					</div>
				</svelte:fragment>
				<svelte:fragment slot="footer">
					<span class="text-sm">Whitelist</span> <code>[{flavorWhitelist.join(', ')}]</code>
				</svelte:fragment>
				<svelte:fragment slot="source">
					<CodeBlock language="ts" code={`const flavorWhitelist: string[] = ['neapolitan', 'pineapple', 'peach'];`} />
					<CodeBlock language="html" code={`<Autocomplete ... whitelist={flavorWhitelist} />`} />
				</svelte:fragment>
			</DocsPreview>
		</section>
		<section class="space-y-4">
			<h2>Blacklist</h2>
			<p>Provide a list of values you wish to blacklist. Blacklisted options will be excluded from the list.</p>
			<DocsPreview background="neutral" regionFooter="text-center">
				<svelte:fragment slot="preview">
					<div class="text-token w-full max-w-sm space-y-2">
						<Autocomplete
							options={flavorOptions}
							blacklist={flavorBlacklist}
							class="card w-full max-w-sm max-h-48 p-4 overflow-y-auto"
							on:selection={onBlacklistSelect}
						/>
					</div>
				</svelte:fragment>
				<svelte:fragment slot="footer">
					<span class="text-sm">Blacklist</span> <code>[{flavorBlacklist.join(', ')}]</code>
				</svelte:fragment>
				<svelte:fragment slot="source">
					<CodeBlock language="ts" code={`let flavorBlacklist: string[] = ['vanilla', 'chocolate'];`} />
					<CodeBlock language="html" code={`<Autocomplete ... blacklist={flavorBlacklist} />`} />
				</svelte:fragment>
			</DocsPreview>
		</section>
		<section class="space-y-4">
			<h2>Input Chip</h2>
			<p>We've provide a demo of using Autocomplete alongside a Skeleton Input Chip component below.</p>
			<DocsPreview background="neutral" regionFooter="text-center">
				<svelte:fragment slot="preview">
					<div class="text-token w-full max-w-sm space-y-2">
						<InputChip bind:input={inputChip} bind:value={inputChipList} name="chips" />
						<Autocomplete
							bind:input={inputChip}
							options={flavorOptions}
							blacklist={inputChipList}
							class="card w-full max-w-sm max-h-48 p-4 overflow-y-auto"
							on:selection={onInputChipSelect}
						/>
					</div>
				</svelte:fragment>
				<svelte:fragment slot="source">
					<CodeBlock language="ts" code={`let inputChip = '';`} />
					<CodeBlock language="ts" code={`let inputChipList: string[] = ['vanilla', 'chocolate'];`} />
					<CodeBlock language="html" code={`<InputChip bind:input={inputChip} bind:value={inputChipList} name="chips" />`} />
					<CodeBlock
						language="html"
						code={`
<Autocomplete
	bind:input={inputChip}
	options={flavorOptions}
	blacklist={inputChipList}
	class="card w-full max-w-sm max-h-48 p-4 overflow-y-auto"
	on:selection={onInputChipSelect}
/>
					`}
					/>
				</svelte:fragment>
			</DocsPreview>
		</section>
	</svelte:fragment>
</DocsShell>
