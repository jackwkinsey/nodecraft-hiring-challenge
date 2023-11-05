<script setup lang="ts">
import {computed, ref} from 'vue';

import heroPicker from './components/heroPicker.vue';
import type {Hero} from './types';

import AchillesAvatar from '../public/heros/achilles.png';
import OdysseusAvatar from '../public/heros/odysseus.png';
import HerculesAvatar from '../public/heros/hercules.png';

const heros = ref<Hero[]>([
	{
		name: 'Achilles',
		avatar: AchillesAvatar,
		speed: 10,
		strength: 4,
		intelligence: 6,
	},
	{
		name: 'Odysseus',
		avatar: OdysseusAvatar,
		speed: 6,
		strength: 5,
		intelligence: 9,
	},
	{
		name: 'Hercules',
		avatar: HerculesAvatar,
		speed: 6,
		strength: 10,
		intelligence: 4,
	},
]);
const baseStatTotal = 20;
const numberOfStats = 3;
const hero = ref<Hero | null>(null);
const bonus = computed(() => {
	if(!hero.value) {
		return 0;
	}

	const heroStatTotal = hero.value.speed + hero.value.strength + hero.value.intelligence;
	return Math.floor((heroStatTotal - baseStatTotal) / numberOfStats);
});

function doBonus() {
	if(!hero.value) {
		return;
	}
	if(bonus.value >= 5) {
		return alert('Only 5 bonus allowed!');
	}

	hero.value.intelligence += 1;
	hero.value.speed += 1;
	hero.value.strength += 1;
}

function clearHero() {
	if(hero.value) {
		hero.value = null;
	}
}

function handleUpdate(input: Hero) {
	hero.value = input;
}
</script>

<template>
	<div
		class="
			bg-slate-800
			rounded
			max-w-sm
			grow
			w-full
			text-white
			flex flex-col
			gap-4
			p-4
		"
	>
		<h1>Hero Stats:</h1>
		<div class="flex gap-4">
			<heroPicker
				class="grow"
				v-bind:value="hero"
				v-bind:options="heros"
				v-on:selected="handleUpdate"
			></heroPicker>
			<button
				type="button"
				class="
					border-green-500
					text-sm text-green-400
					border
					p-4
					px-4
					py-2
					rounded
				"
				v-on:click="doBonus()"
			>
				BONUS {{ bonus > 0 ? `(${bonus})` : '' }} ✨
			</button>
		</div>
		<div v-if="hero" class="bg-slate-400 text-black rounded flex gap-2 overflow-hidden">
			<div
				class="close-btn absolute ml-2 cursor-pointer font-bold drop-shadow-[0_1px_5px_rgba(255,255,255,0.75)]"
				v-on:click="clearHero"
			></div>
			<img v-bind:src="hero.avatar" class="w-52 h-52 object-cover">
			<div>
				<h2 class="uppercase text-xs mb-4">
					Hero Summary
				</h2>
				<p>
					<span v-text="hero.name"></span>
				</p>
				<dl>
					<dt class="uppercase text-sm">
						Speed:
					</dt>
					<dd v-text="hero.speed"></dd>
					<dt class="uppercase text-sm">
						Strength:
					</dt>
					<dd v-text="hero.strength"></dd>
					<dt class="uppercase text-sm">
						Intelligence:
					</dt>
					<dd v-text="hero.intelligence"></dd>
				</dl>
			</div>
		</div>
	</div>
</template>

<style>
.close-btn::after {
  content: "\00d7";
}
</style>
