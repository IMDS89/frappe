<script setup>
import { ref, nextTick, computed } from "vue";
import { useStore } from "../store";
let store = useStore();

let props = defineProps({
	text: {
		type: String
	},
	placeholder: {
		default: __("No Label")
	},
	empty_label: {
		default: __("No Label")
	},
});

let editing = ref(false);
let input_text = ref(null);
let hidden_text = ref(null);
let hidden_placeholder = ref(null);

let hidden_span_width = computed(() => {
	if (hidden_text.value && props.text) {
		return hidden_text.value.offsetWidth + 15 + "px";
	} else if (!props.text) {
		return hidden_placeholder.value.offsetWidth + 15 + "px";
	}
	return "40px";
});

function focus_on_label() {
	if (!store.read_only) {
		editing.value = true;
		nextTick(() => input_text.value.focus());
	}
}
</script>

<template>
	<div @dblclick="focus_on_label" :title="__('Double click to edit label')">
		<input
			v-if="editing"
			class="input-text"
			ref="input_text"
			:disabled="store.read_only"
			type="text"
			:placeholder="placeholder"
			v-model="text"
			:style="{ width: hidden_span_width }"
			@input="event => $emit('update:modelValue', event.target.value)"
			@keydown.enter="editing = false"
			@blur="editing = false"
			@click.stop
		/>
		<span v-else-if="text">{{ text }}</span>
		<i v-else class="text-muted">
			{{ empty_label }}
		</i>
		<span class="hidden-span" ref="hidden_text">{{ text }}</span>
		<span class="hidden-span" ref="hidden_placeholder">{{ placeholder }}</span>
	</div>
</template>

<style lang="scss" scoped>
.input-text {
	border: none;
	min-width: 50px;
	padding: 0px !important;

	&:focus {
		outline: none;
		border-radius: var(--border-radius);
		background-color: inherit;
	}

	&::placeholder {
		font-style: italic;
		font-weight: normal;
	}
}

.hidden-span {
	visibility: hidden;
	position: absolute;
	width: max-content;
}
</style>
