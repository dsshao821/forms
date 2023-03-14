<!--
  - @copyright Copyright (c) 2020 John Molakvoæ <skjnldsv@protonmail.com>
  -
  - @author John Molakvoæ <skjnldsv@protonmail.com>
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
  -
  -->
<template>
	<div class="top-bar" role="toolbar">
		<PillMenu v-if="!canOnlySubmit"
			:active="currentView"
			:options="availableViews"
			@update:active="onChangeView" />
		<NcButton v-if="canShare && !sidebarOpened"
			:aria-label="isMobile ? t('forms', 'Share form') : null"
			type="tertiary"
			@click="onShareForm">
			<template #icon>
				<IconShareVariant :size="20" />
			</template>
			<template v-if="!isMobile">
				{{ t('forms', 'Share') }}
			</template>
		</NcButton>
		<NcButton v-if="showSidebarToggle"
			v-tooltip="t('forms', 'Toggle settings')"
			:aria-label="t('forms', 'Toggle settings')"
			type="tertiary"
			@click="toggleSidebar">
			<template #icon>
				<IconMenuOpen :size="24"
					:class="{ 'icon--flipped' : sidebarOpened }" />
			</template>
		</NcButton>
	</div>
</template>

<script>
import NcButton from '@nextcloud/vue/dist/Components/NcButton.js'
import isMobile from '@nextcloud/vue/dist/Mixins/isMobile.js'
import IconEye from 'vue-material-design-icons/Eye.vue'
import IconMenuOpen from 'vue-material-design-icons/MenuOpen.vue'
import IconPencil from 'vue-material-design-icons/Pencil.vue'
import IconPoll from 'vue-material-design-icons/Poll.vue'
import IconShareVariant from 'vue-material-design-icons/ShareVariant.vue'

import PermissionTypes from '../mixins/PermissionTypes.js'
import PillMenu from './PillMenu.vue'

const submitView = {
	ariaLabel: t('forms', 'View form'),
	icon: IconEye,
	title: t('forms', 'View'),
	route: 'submit',
}
const editView = {
	ariaLabel: t('forms', 'Edit form'),
	icon: IconPencil,
	title: t('forms', 'Edit'),
	route: 'edit',
}
const resultsView = {
	ariaLabel: t('forms', 'Show results'),
	icon: IconPoll,
	title: t('forms', 'Results'),
	route: 'results',
}

export default {
	name: 'TopBar',

	components: {
		IconMenuOpen,
		IconShareVariant,
		NcButton,
		PillMenu,
	},

	mixins: [isMobile, PermissionTypes],

	props: {
		sidebarOpened: {
			type: Boolean,
			default: null,
		},
		permissions: {
			type: Array,
			default: () => [],
		},
	},

	computed: {
		currentView() {
			return this.availableViews.filter(v => v.route === this.$route.name)[0]
		},
		availableViews() {
			const views = []
			if (this.canSubmit) {
				views.push(submitView)
			}
			if (this.canEdit) {
				views.push(editView)
			}
			if (this.canSeeResults) {
				views.push(resultsView)
			}
			return views
		},
		canSubmit() {
			return this.permissions.includes(this.PERMISSION_TYPES.PERMISSION_SUBMIT)
		},
		canEdit() {
			return this.permissions.includes(this.PERMISSION_TYPES.PERMISSION_EDIT)
		},
		canSeeResults() {
			return this.permissions.includes(this.PERMISSION_TYPES.PERMISSION_RESULTS)
		},
		canShare() {
			// This probably can get a permission of itself
			return this.canEdit
		},
		canOnlySubmit() {
			return this.permissions.length === 1 && this.permissions.includes(this.PERMISSION_TYPES.PERMISSION_SUBMIT)
		},
		showSidebarToggle() {
			return this.canEdit && this.sidebarOpened !== null
		},
	},

	methods: {
		toggleSidebar() {
			this.$emit('update:sidebarOpened', !this.sidebarOpened)
		},

		/**
		 * Router methods
		 *
		 * @param {object} option The selected pill menu option
		 */
		onChangeView(option) {
			if (this.$route.name !== option.route) {
				this.$router.push({
					name: option.route,
					params: {
						hash: this.$route.params.hash,
					},
				})
			}
		},

		onShareForm() {
			this.$emit('share-form')
		},
	},
}
</script>

<style lang="scss" scoped>
.top-bar {
	top: 0;
	z-index: 100;
	display: flex;
	position: sticky;
	align-items: center;
	align-self: flex-end;
	justify-content: flex-end;
	padding: calc(var(--default-grid-baseline, 4px) * 2);
}

.icon--flipped {
	transform: scaleX(-1);
}
</style>
