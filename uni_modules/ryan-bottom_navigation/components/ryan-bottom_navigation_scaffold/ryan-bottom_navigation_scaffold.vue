<template>
	<view class="bottom-nav-scaffold">
		<view class="bottom-nav-scaffold__content" :style="contentStyle">
			<slot
				name="page"
				:page="activePage"
				:index="resolvedCurrentIndex"
			>
				<component
					v-if="activePage && activePage.component"
					:is="activePage.component"
					v-bind="activePage.props || {}"
				/>
			</slot>
		</view>

		<RyanCommonBottomNavigation
			:items="items"
			:current-index="resolvedCurrentIndex"
			:center-action="centerAction"
			:shell-inset="shellInset"
			:theme="theme"
			:layout="layout"
			:absolute="absoluteNavigation"
			@input="handleInput"
			@update:currentIndex="handleUpdateCurrentIndex"
			@itemTap="handleItemTap"
			@change="handleChange"
			@centerTap="handleCenterTap"
		/>
	</view>
</template>

<script>
import RyanCommonBottomNavigation from '../ryan-bottom_navigation/ryan-bottom_navigation.vue'

export default {
	name: 'RyanCommonBottomNavigationScaffold',
	components: {
		RyanCommonBottomNavigation
	},
	props: {
		value: {
			type: Number,
			default: undefined
		},
		currentIndex: {
			type: Number,
			default: 0
		},
		items: {
			type: Array,
			default() {
				return []
			}
		},
		pages: {
			type: Array,
			default() {
				return []
			}
		},
		centerAction: {
			type: Object,
			default() {
				return null
			}
		},
		shellInset: {
			type: Number,
			default: 24
		},
		theme: {
			type: Object,
			default() {
				return {}
			}
		},
		layout: {
			type: Object,
			default() {
				return {}
			}
		},
		contentPaddingBottom: {
			type: Number,
			default: 0
		},
		absoluteNavigation: {
			type: Boolean,
			default: true
		}
	},
	computed: {
		resolvedCurrentIndex() {
			if (typeof this.value === 'number') {
				return this.value
			}
			return this.currentIndex
		},
		activePage() {
			return this.pages[this.resolvedCurrentIndex] || null
		},
		contentStyle() {
			const fallbackPadding = this.layout && this.layout.height ? this.layout.height : 172
			return {
				paddingBottom: `${this.contentPaddingBottom || fallbackPadding}rpx`
			}
		}
	},
	methods: {
		handleInput(index) {
			this.$emit('input', index)
		},
		handleUpdateCurrentIndex(index) {
			this.$emit('update:currentIndex', index)
		},
		handleItemTap(payload) {
			this.$emit('itemTap', payload)
		},
		handleChange(payload) {
			this.$emit('change', payload)
		},
		handleCenterTap(payload) {
			this.$emit('centerTap', payload)
		}
	}
}
</script>

<style lang="scss" scoped>
.bottom-nav-scaffold {
	position: relative;
	min-height: 100%;
}

.bottom-nav-scaffold__content {
	min-height: 100%;
	box-sizing: border-box;
}
</style>
