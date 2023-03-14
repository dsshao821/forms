<!--
  - @copyright Copyright (c) 2022 Jan C. Borchardt https://jancborchardt.net
  -
  - @author Jan C. Borchardt https://jancborchardt.net
  - @author Ferdinand Thiessen <rpm@fthiessen.de>
  -
  - @license AGPL-3.0-or-later
  -
  - This program is free software: you can redistribute it and/or modify
  - it under the terms of the GNU Affero General Public License as
  - published by the Free Software Foundation, either version 3 of the
  - License, or (at your option) any later version.
  -
  - This program is distributed in the hope that it will be useful,
  - but WITHOUT ANY WARRANTY; without even the implied warranty of
  - MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
  - GNU Affero General Public License for more details.
  -
  - You should have received a copy of the GNU Affero General Public License
  - along with this program.  If not, see <http://www.gnu.org/licenses/>.
  -->

<template>
	<div class="pill-menu">
		<NcButton v-for="(option, key) in options"
			:key="key"
			:aria-label="isMobile ? option.ariaLabel : null"
			:type="active == option ? 'secondary' : 'tertiary'"
			@click="onClick(option)">
			<template v-if="option.icon" #icon>
				<component :is="option.icon" :size="20" />
			</template>
			{{ !isMobile || !option.icon ? option.title : null }}
		</NcButton>
	</div>
</template>

<script>
import NcButton from '@nextcloud/vue/dist/Components/NcButton.js'
import isMobile from '@nextcloud/vue/dist/Mixins/isMobile.js'

export default {
	name: 'PillMenu',

	components: {
		NcButton,
	},

	mixins: [isMobile],

	props: {
		/**
		 * The active option
		 */
		active: {
			type: Object,
			required: true,
		},
		/**
		 * List of available options
		 * `option: {title: string, ariaLabel: string, icon: Component}`
		 */
		options: {
			type: Array,
			required: true,
		},
	},

	methods: {
		onClick(option) {
			this.$emit('update:active', option)
		},
	},
}
</script>

<style lang="scss" scoped>
.pill-menu {
	align-items: center;
	align-self: flex-end;
	background: var(--color-main-background);
	border: 2px solid var(--color-border);
	border-radius: var(--border-radius-pill);
	box-sizing: content-box!important;
	display: flex;
	height: 44px;
	justify-content: flex-end;
}
</style>
