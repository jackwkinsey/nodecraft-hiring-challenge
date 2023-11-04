<template>
	<div class="relative">
		<button
			type="button"
			class="
				block
				w-full
				px-4
				py-2
				text-sm
				font-medium
				text-white
				bg-indigo-600
				rounded-lg
				hover:bg-indigo-700
				focus:outline-none
				focus-visible:ring
				focus-visible:ring-indigo-500
				focus-visible:ring-opacity-75
			"
			v-on:click="toggleMenu"
			v-on:keydown.space.exact.prevent="toggleMenu"
			v-on:keydown.esc.exact="hideMenu"
			v-on:keydown.shift.tab="hideMenu"
			v-on:keydown.down.exact.prevent="navigateMenu(0)"
		>
			<span v-if="!value">Select a Hero</span>
			<span v-else v-text="`Selected: ${value?.name}`"></span>
		</button>
		<ul
			v-show="isOpen"
			class="
				absolute
				right-0
				z-10
				w-32
				mt-2
				origin-top-right
				bg-slate-800
				border border-slate-500
				rounded-md
				shadow-lg
			"
		>
			<li v-for="(option, index) in props.options" v-bind:key="option.name" ref="optionRefs" class="py-1">
				<a
					href="#"
					class="
						flex
						items-center
						px-4
						py-2
						text-sm text-slate-400
						hover:bg-slate-700
						gap-2
					"
					v-on:click="setOption(option)"
					v-on:keydown.space.exact.prevent="setOption(option)"
					v-on:keydown.esc.exact="hideMenu"
					v-on:keydown.tab.exact="closeMenu(index)"
					v-on:keydown.up.exact.prevent="navigateMenu(index - 1)"
					v-on:keydown.down.exact.prevent="navigateMenu(index + 1)"
				>
					<img
						v-bind:src="option.avatar"
						alt="avatar"
						class="w-6 aspect-square rounded"
					>
					<span v-text="option.name"></span>
				</a>
			</li>
		</ul>
	</div>
</template>

<script setup lang="ts">
import {ref} from 'vue';
import type {PropType, Ref} from 'vue';
import type {Hero} from '../types';
const props = defineProps({
	value: Object as PropType<Hero | null>,
	options: Array as PropType<Hero[]>,
});

const emit = defineEmits(['selected']);
const isOpen = ref(false);
const optionRefs: Ref<Array<HTMLElement>> = ref([]);

function toggleMenu() {
	isOpen.value = !isOpen.value;
}

function hideMenu() {
	isOpen.value = false;
}

// Helper function to allow us to hide the menu when tabbing off of last item
function closeMenu(index: number) {
	if(props.options && index === props.options.length - 1) {
		hideMenu();
	}
}

function navigateMenu(index: number) {
	if(!props.options || !isOpen.value) {
		return;
	}

	// Allow for navigating to bottom from top and to bottom from top
	let i = index;
	if(i < 0) {
		i = props.options.length - 1;
	}else if(i === props.options.length) {
		i = 0;
	}

	(optionRefs.value[i].children[0] as HTMLElement).focus();
}

function setOption(input: Hero) {
	emit('selected', input);
	toggleMenu();
}
</script>
