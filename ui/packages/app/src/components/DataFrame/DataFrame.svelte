<script lang="ts">
	import { Table } from "@gradio/table";
	import StatusTracker from "../StatusTracker/StatusTracker.svelte";
	import type { LoadingStatus } from "../StatusTracker/types";
	import { createEventDispatcher, tick } from "svelte";

	type Headers = Array<string>;
	type Data = Array<Array<string | number>>;

	export let headers: Headers = [];
	export let elem_id: string = "";
	export let value: Data | { data: Data; headers: Headers } = [["", "", ""]];
	export let mode: "static" | "dynamic";
	export let col_count: [number, "fixed" | "dynamic"];
	export let row_count: [number, "fixed" | "dynamic"];
	export let parent: string | null = null;

	$: {
		if (!Array.isArray(value)) {
			if (Array.isArray(value.headers)) headers = value.headers;
			value =
				value.data.length === 0 ? [Array(headers.length).fill("")] : value.data;
		} else {
			value = value;
		}
	}

	const dispatch = createEventDispatcher();

	export let loading_status: LoadingStatus;

	async function handle_change({ detail }) {
		value = detail;
		await tick();
		dispatch("change", detail);
	}
</script>

<div
	id={elem_id}
	class="relative overflow-hidden"
	class:flex-1={parent === "row" || !parent}
>
	<StatusTracker {...loading_status} />
	<Table
		{row_count}
		{col_count}
		values={value}
		{headers}
		on:change={handle_change}
		editable={mode === "dynamic"}
	/>
</div>
