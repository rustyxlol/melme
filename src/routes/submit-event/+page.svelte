<script>
	import { enhance } from "$app/forms";

	import Input from "$lib/components/ui/Input.svelte";
	import Textarea from "$lib/components/ui/Textarea.svelte";
	import RadioButtons from "$lib/components/ui/RadioButtons.svelte";
	import PrimaryButton from "$lib/components/ui/PrimaryButton.svelte";
	import ArrowLeft from "$lib/components/icons/ArrowLeft.svelte";
	import TogglerLink from "./TogglerLink.svelte";
	import CategoryPicker from "./CategoryPicker.svelte";
	import Seo from "$lib/components/Seo.svelte";
	import DateAndTimePicker from "./DateAndTimePicker.svelte";
	import AddressPicker from "./AddressPicker.svelte";
	import LocationPicker from "./LocationPicker.svelte";

	import { handleClickBack } from "$lib/utils/index.js";
	import { categoryList, timeOptions } from "$lib/config.js";

	export let form;

	const enhanceCallback = ({ data }) => {
		data.append("categories", JSON.stringify(pickedList));
	};

	const updateView = data => {
		if (!data || data.success) return;

		window.scrollTo({ top: 0, behavior: "smooth" });
	};

	const resetValues = () => {
		pickedList = [];
		imgPreview = null;
	};

	$: updateView(form);

	let pickedList = [];
	$: imgPreview = null;
</script>

<Seo title="melme | submit event" />

<section class="max-w-2xl mx-auto py-8">
	{#if form?.success}
		<p class="text-2xl mb-2 text-center">Thank you!</p>
		<p class="text-center">
			The event was successfully sent! You can review it in
			<a
				href="/moderation-queue"
				class="underline"
			>
				the submission queue.
			</a>
			or
			<a
				href="/submit-event"
				class="underline"
				on:click={resetValues}
			>
				submit another one
			</a>
		</p>
	{:else}
		<a
			href="/"
			on:click|preventDefault={handleClickBack}
			class="p-3 block -ml-3 mb-2"
		>
			<ArrowLeft />
		</a>

		<h1 class="mb-8 text-2xl">
			To submit an event, please, fill all the necessary fields marked with *
		</h1>

		{#if form?.error}
			<p class="my-4 text-red-500 text-center">
				Error: {form.error}
			</p>
		{/if}

		<form
			method="POST"
			use:enhance={enhanceCallback}
		>
			<Input
				title="*Name of the event"
				id="event-title"
				name="title"
				autofocus={true}
				maxlength={64}
				placeholder="Title of the event, e.g. Rally cars showcase."
				class="mb-4"
				externalError={form?.missingTitle && "The title is missing"}
			/>

			<Textarea
				title="*Description of the event"
				placeholder="Provide the description here, you can paste the original one or describe in your own words."
				id="event-description"
				name="description"
				maxlength={2048}
				class="mb-4"
				externalError={form?.shortDescription &&
					"The description is too short, minimum is 20 characters"}
			/>

			<Input
				title="*Link to the event from an official source"
				id="event-link"
				name="linkToEvent"
				maxlength={128}
				placeholder="Where did you hear it from?"
				class="mb-4"
				externalError={form?.missingLink && "The link is missing"}
			/>

			<AddressPicker
				externalError={form?.missingAddress && "The address is missing"}
			/>

			<LocationPicker title="Could you point to the location(s) on the map?" />

			<DateAndTimePicker
				title="*Pick a date and a local time when it starts"
				externalError={form?.missingDate && "Something wrong with the dates"}
			/>

			<RadioButtons
				title="Do you know how long it will be?"
				id="event-duration"
				name="duration"
				options={timeOptions}
				class="mb-4"
			/>

			<Textarea
				title="Provide specific requirements if there are any"
				placeholder="For example, you need to pre-register online and be in shoes."
				id="event-requirements"
				name="requirements"
				maxlength={512}
				class="mb-4"
			/>

			<TogglerLink
				title="Do you need to register for this event?"
				secondTitle="Provide the link to the registration."
				inputPlaceholder="Paste a link to the registration form."
				inputId="event-registration-link"
				optionOneText="Yes"
				optionTwoText="No"
				name="isRegistrationNeeded"
				inputName="registrationLink"
			/>

			<TogglerLink
				title="Is that a free-to-enter event?"
				secondTitle="How much does it cost?"
				inputPlaceholder="Specify AED or USD."
				inputId="event-cost"
				isInputChecked={true}
				optionOneText="Yes"
				optionTwoText="No"
				name="isEventFree"
				inputName="price"
			/>

			<Input
				title="Paste image preview link ('copy image URL' or 'Open image in new tab' and copy the URL)"
				id="event-image"
				name="imgSrc"
				maxlength={256}
				placeholder="Find some nice image."
				class="mb-4"
				bind:value={imgPreview}
			/>

			{#if imgPreview}
				<p class="lowercase text-sm mb-2">Image preview</p>

				<img
					src={imgPreview}
					alt="Preview of a picture from external site"
					class="mb-4"
				/>
			{/if}

			<Input
				title="Who is hosting this event?"
				id="event-host"
				name="hostName"
				maxlength={32}
				placeholder="Will be important in the future."
				class="mb-4"
			/>

			<CategoryPicker
				title="Pick a category for this event (multiple allowed)"
				list={categoryList}
				bind:pickedList
			/>

			<PrimaryButton
				title="Submit"
				class="mt-8"
			/>
		</form>
	{/if}
</section>
