<script lang="ts">
	import { goto } from '$app/navigation';
	import { POST } from '$lib/api';
	import type { components } from '$lib/cosearch';

	import ColorPicker from 'svelte-awesome-color-picker';

	export let data;

	let new_tag: components['schemas']['TagCreate'] = {
		display_name: '',
		color: ''
	};

	let creationError: Error | null = null;

	async function createTag(event: Event) {
		const form = event.target.closest('form');
		const valid = form.checkValidity();
		if (!valid) {
			form.reportValidity();
			return;
		}
		try {
			new_tag.color = hex;
			await POST('/tags', {
				body: new_tag
			});
			goto('/tags');
		} catch (error) {
			creationError = error;
			console.error(error);
		}
	}

	let hex = '#d9ae21';
</script>

<nav class="flex" aria-label="Breadcrumb">
	<ol role="list" class="flex items-center space-x-0">
		<li>
			<div>
				<a href="/tags" class="text-gray-400 hover:text-gray-500">
					Tags {#if data.tags}({data.tags.length}){/if}
				</a>
			</div>
		</li>
		<li>
			<div class="flex items-center">
				<svg
					class="h-5 w-5 flex-shrink-0 text-gray-400"
					viewBox="0 0 20 20"
					fill="currentColor"
					aria-hidden="true"
				>
					<path
						fill-rule="evenodd"
						d="M7.21 14.77a.75.75 0 01.02-1.06L11.168 10 7.23 6.29a.75.75 0 111.04-1.08l4.5 4.25a.75.75 0 010 1.08l-4.5 4.25a.75.75 0 01-1.06-.02z"
						clip-rule="evenodd"
					/>
				</svg>
				<span class="ml-0 text-sm font-medium text-gray-500 hover:text-gray-700">New</span>
			</div>
		</li>
	</ol>
</nav>

<!-- TODO: this form's DOM needs to be cleaned, using flex everywhere instead of grid.
    TODO: fix inputs name attributes and labels.
-->
<form class="sm:w-4/5">
	<div class="space-y-5">
		<div class="border-b border-gray-900/10 pb-10">
			<!-- <h2 class="text-base font-semibold leading-7 text-gray-900">New Contributor</h2> -->
			<!-- <p class="mt-1 text-sm leading-6 text-gray-600">
				Any result, experiment, successful or unsuccessful idea, and more generally, anything which
				you believe is helpful for the future of the project is a suitable contribution.
			</p> -->

			<div class=" grid grid-cols-1 gap-x-6 gap-y-6 sm:grid-cols-6">
				<div class="sm:col-span-2">
					<label
						for="tag_display_name"
						class="inline-block text-sm font-medium leading-6 text-gray-900"
						>Text
					</label>

					<div class="mt-2">
						<input
							type="text"
							name="tag_display_name"
							id="tag_display_name"
							autocomplete="off"
							bind:value={new_tag.display_name}
							class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
							required
						/>
					</div>
				</div>

				<div class="sm:col-span-3">
					<label for="personal_website" class="block text-sm font-medium leading-6 text-gray-900"
						>Color
					</label>

					<ColorPicker bind:hex position="responsive" />
				</div>
			</div>
		</div>
	</div>

	<div class="mt-6 flex items-center justify-end gap-x-6 mb-10">
		<button
			type="button"
			class="text-sm font-semibold leading-6 text-gray-900"
			on:click={() => {
				goto('/tags');
			}}>Cancel</button
		>
		<button
			type="submit"
			on:click={async (event) => {
				await createTag(event);
			}}
			class="rounded-md bg-indigo-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
			>Save</button
		>
	</div>
</form>
