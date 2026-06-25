<template>
	<view class="bottom-nav" :style="rootStyle">
		<view class="bottom-nav__frame" :style="frameStyle">
			<view class="bottom-nav__shell" id="bottom-nav-shell" :style="shellStyle">
				<view
					class="bottom-nav__pill"
					:style="pillStyle"
				></view>

				<view class="bottom-nav__content" :style="contentStyle">
					<view class="bottom-nav__group" :style="groupStyle">
						<button
							v-for="(item, index) in leftItems"
							:key="item.id || `left-${index}`"
							:id="itemAnchorId(item, index)"
							class="bottom-nav__item"
							:style="itemStyle"
							:class="{ 'bottom-nav__item--active': resolvedCurrentIndex === index, 'bottom-nav__item--disabled': item.disabled }"
							hover-class="none"
							@tap="handleItemTap(index, item)"
						>
							<view class="bottom-nav__icon-wrap" :style="iconWrapStyle">
								<image
									class="bottom-nav__icon"
									:style="iconStyle"
									:src="resolveIcon(item, resolvedCurrentIndex === index)"
									mode="aspectFit"
								/>
								<view
									v-if="item.badge && item.badge.type === 'count'"
									class="bottom-nav__badge bottom-nav__badge--count"
									:style="badgeStyle"
								>
									<text class="bottom-nav__badge-text" :style="badgeTextStyle">{{ formatBadge(item.badge.value) }}</text>
								</view>
								<view
									v-else-if="item.badge && item.badge.type === 'dot'"
									class="bottom-nav__badge bottom-nav__badge--dot"
									:style="badgeDotStyle"
								></view>
							</view>
							<text class="bottom-nav__label" :style="labelStyle(resolvedCurrentIndex === index)">{{ item.label }}</text>
						</button>
					</view>

					<view class="bottom-nav__center-gap" :style="centerGapStyle"></view>

					<view class="bottom-nav__group" :style="groupStyle">
						<button
							v-for="(item, offsetIndex) in rightItems"
							:key="item.id || `right-${offsetIndex}`"
							:id="itemAnchorId(item, offsetIndex + leftItems.length)"
							class="bottom-nav__item"
							:style="itemStyle"
							:class="{ 'bottom-nav__item--active': resolvedCurrentIndex === offsetIndex + leftItems.length, 'bottom-nav__item--disabled': item.disabled }"
							hover-class="none"
							@tap="handleItemTap(offsetIndex + leftItems.length, item)"
						>
							<view class="bottom-nav__icon-wrap" :style="iconWrapStyle">
								<image
									class="bottom-nav__icon"
									:style="iconStyle"
									:src="resolveIcon(item, resolvedCurrentIndex === offsetIndex + leftItems.length)"
									mode="aspectFit"
								/>
								<view
									v-if="item.badge && item.badge.type === 'count'"
									class="bottom-nav__badge bottom-nav__badge--count"
									:style="badgeStyle"
								>
									<text class="bottom-nav__badge-text" :style="badgeTextStyle">{{ formatBadge(item.badge.value) }}</text>
								</view>
								<view
									v-else-if="item.badge && item.badge.type === 'dot'"
									class="bottom-nav__badge bottom-nav__badge--dot"
									:style="badgeDotStyle"
								></view>
							</view>
							<text class="bottom-nav__label" :style="labelStyle(resolvedCurrentIndex === offsetIndex + leftItems.length)">{{ item.label }}</text>
						</button>
					</view>
				</view>
			</view>

			<button
				v-if="centerAction && centerAction.visible !== false"
				class="bottom-nav__center-button"
				:style="centerButtonStyle"
				:hover-class="'bottom-nav__center-button--pressed'"
				hover-stay-time="80"
				@tap="handleCenterTap"
			>
				<image
					class="bottom-nav__center-icon"
					:style="centerIconStyle"
					:src="centerAction.icon"
					mode="aspectFit"
				/>
			</button>
		</view>
	</view>
</template>

<script>
const DEFAULT_THEME = {
	shellBackgroundImage: '',
	activePillGradientStart: 'rgba(221, 215, 207, 0.92)',
	activePillGradientEnd: 'rgba(221, 215, 207, 0.74)',
	activePillShadowColor: 'rgba(221, 215, 207, 0.3)',
	labelColor: 'rgba(46, 43, 39, 0.76)',
	activeLabelColor: '#2E2B27',
	badgeBackgroundColor: '#FF5A5F',
	badgeBorderColor: 'rgba(255, 255, 255, 0.92)',
	centerButtonBackgroundColor: '#FFFCF7',
	centerButtonBorderColor: 'rgba(255, 255, 255, 0.55)',
	centerButtonShadowColor: 'rgba(86, 66, 46, 0.24)'
}

const DEFAULT_LAYOUT = {
	height: 172,
	shellHeight: 124,
	maxWidth: 702,
	minScale: 0.84,
	shellViewBoxWidth: 390,
	shellViewBoxHeight: 62,
	shellCornerRadius: 31,
	notchDepth: 10,
	notchShoulderLeft: 139,
	notchShoulderCurveLeft: 157,
	notchArcStartX: 167,
	notchArcEndX: 223,
	notchShoulderCurveRight: 233,
	notchShoulderRight: 251,
	notchArcRadius: 38,
	contentPaddingTop: 20,
	contentPaddingHorizontal: 36,
	contentPaddingBottom: 12,
	centerGap: 128,
	pillBottom: 12,
	pillHeight: 100,
	pillMinWidth: 124,
	pillHorizontalPadding: 44,
	centerButtonSize: 112,
	centerButtonOffsetY: -4
}

export default {
	name: 'RyanCommonBottomNavigation',
	props: {
		value: {
			type: Number,
			default: undefined
		},
		items: {
			type: Array,
			default() {
				return []
			}
		},
		currentIndex: {
			type: Number,
			default: 0
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
		absolute: {
			type: Boolean,
			default: true
		}
	},
	data() {
		return {
			pillLeftPx: 0,
			pillWidthPx: 112,
			shellWidthPx: 0
		}
	},
	computed: {
		leftItems() {
			return this.items.slice(0, 2)
		},
		rightItems() {
			return this.items.slice(2, 4)
		},
		resolvedCurrentIndex() {
			if (typeof this.value === 'number') {
				return this.value
			}
			return this.currentIndex
		},
		resolvedTheme() {
			return Object.assign({}, DEFAULT_THEME, this.theme)
		},
		resolvedLayout() {
			return Object.assign({}, DEFAULT_LAYOUT, this.layout)
		},
		layoutScale() {
			const baseWidthPx = uni.upx2px(this.resolvedLayout.maxWidth)
			if (!baseWidthPx || !this.shellWidthPx) {
				return 1
			}
			const scaled = this.shellWidthPx / baseWidthPx
			return Math.max(this.resolvedLayout.minScale, Math.min(1, scaled))
		},
		rootStyle() {
			return {
				position: this.absolute ? 'absolute' : 'relative',
				left: 0,
				right: 0,
				bottom: 0,
				height: this.scaledPx(this.resolvedLayout.height)
			}
		},
		frameStyle() {
			return {
				width: `calc(100% - ${uni.upx2px(this.shellInset * 2)}px)`,
				maxWidth: `${uni.upx2px(this.resolvedLayout.maxWidth)}px`,
				height: '100%'
			}
		},
		shellStyle() {
			const shellBackgroundImage = this.resolvedTheme.shellBackgroundImage
				? `url('${this.resolvedTheme.shellBackgroundImage}')`
				: `url("${this.generatedShellDataUri}")`
			return {
				height: this.scaledPx(this.resolvedLayout.shellHeight),
				backgroundImage: shellBackgroundImage
			}
		},
		generatedShellDataUri() {
			const layout = this.resolvedLayout
			const width = layout.shellViewBoxWidth
			const height = layout.shellViewBoxHeight
			const radius = layout.shellCornerRadius
			const depth = layout.notchDepth
			const leftStart = layout.notchShoulderLeft
			const leftCurve = layout.notchShoulderCurveLeft
			const arcStart = layout.notchArcStartX
			const arcEnd = layout.notchArcEndX
			const rightCurve = layout.notchShoulderCurveRight
			const rightEnd = layout.notchShoulderRight
			const arcRadius = layout.notchArcRadius

			const fillColor = this.escapeSvgColor(this.resolvedTheme.centerButtonBackgroundColor)
			const borderColor = this.escapeSvgColor('rgba(255,255,255,0.55)')
			const shadowColor = this.escapeSvgColor('rgba(87,64,46,0.16)')
			const path = `M${radius} 0H${leftStart}Q${leftCurve} 0 ${arcStart} ${depth}A${arcRadius} ${arcRadius} 0 0 0 ${arcEnd} ${depth}Q${rightCurve} 0 ${rightEnd} 0H${width - radius}A${radius} ${radius} 0 0 1 ${width} ${radius}A${radius} ${radius} 0 0 1 ${width - radius} ${height}H${radius}A${radius} ${radius} 0 0 1 0 ${radius}A${radius} ${radius} 0 0 1 ${radius} 0Z`
			const borderPath = `M${radius + 0.6} 0.6H${leftStart}Q${leftCurve} 0.6 ${arcStart - 0.3} ${depth + 0.3}A${Math.max(0, arcRadius - 0.6)} ${Math.max(0, arcRadius - 0.6)} 0 0 0 ${arcEnd + 0.3} ${depth + 0.3}Q${rightCurve} 0.6 ${rightEnd} 0.6H${width - radius - 0.6}A${radius - 0.6} ${radius - 0.6} 0 0 1 ${width - 0.6} ${radius}A${radius - 0.6} ${radius - 0.6} 0 0 1 ${width - radius - 0.6} ${height - 0.6}H${radius + 0.6}A${radius - 0.6} ${radius - 0.6} 0 0 1 0.6 ${radius}A${radius - 0.6} ${radius - 0.6} 0 0 1 ${radius + 0.6} 0.6Z`
			const svg = `<svg width="${width}" height="${height}" viewBox="0 0 ${width} ${height}" fill="none" xmlns="http://www.w3.org/2000/svg"><defs><filter id="shadow" x="-20" y="-8" width="${width + 40}" height="${height + 36}" filterUnits="userSpaceOnUse" color-interpolation-filters="sRGB"><feFlood flood-opacity="0" result="BackgroundImageFix"/><feColorMatrix in="SourceAlpha" type="matrix" values="0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 127 0" result="hardAlpha"/><feOffset dy="10"/><feGaussianBlur stdDeviation="10"/><feColorMatrix type="matrix" values="0 0 0 0 ${shadowColor.r} 0 0 0 0 ${shadowColor.g} 0 0 0 0 ${shadowColor.b} 0 0 0 ${shadowColor.a} 0"/><feBlend mode="normal" in2="BackgroundImageFix" result="effect1_dropShadow"/><feBlend mode="normal" in="SourceGraphic" in2="effect1_dropShadow" result="shape"/></filter></defs><g filter="url(#shadow)"><path d="${path}" fill="${fillColor.css}"/><path d="${borderPath}" stroke="${borderColor.css}" stroke-width="1.2"/></g></svg>`
			return `data:image/svg+xml;utf8,${encodeURIComponent(svg)}`
		},
		contentStyle() {
			return {
				padding: `${this.scaledNumber(this.resolvedLayout.contentPaddingTop)}px ${this.scaledNumber(this.resolvedLayout.contentPaddingHorizontal)}px ${this.scaledNumber(this.resolvedLayout.contentPaddingBottom)}px`
			}
		},
		groupStyle() {
			return {
				gap: `${this.scaledNumber(20)}px`
			}
		},
		centerGapStyle() {
			return {
				flex: `0 0 ${this.scaledNumber(this.resolvedLayout.centerGap)}px`
			}
		},
		pillStyle() {
			return {
				width: `${this.pillWidthPx}px`,
				transform: `translateX(${this.pillLeftPx}px)`,
				bottom: this.scaledPx(this.resolvedLayout.pillBottom),
				height: this.scaledPx(this.resolvedLayout.pillHeight),
				background: `linear-gradient(to bottom, ${this.resolvedTheme.activePillGradientStart}, ${this.resolvedTheme.activePillGradientEnd})`,
				boxShadow: `0 12rpx 30rpx ${this.resolvedTheme.activePillShadowColor}`
			}
		},
		centerButtonStyle() {
			return {
				transform: `translate(-50%, ${this.scaledNumber(this.resolvedLayout.centerButtonOffsetY)}px)`,
				width: this.scaledPx(this.resolvedLayout.centerButtonSize),
				height: this.scaledPx(this.resolvedLayout.centerButtonSize),
				borderColor: this.resolvedTheme.centerButtonBorderColor,
				background: this.resolvedTheme.centerButtonBackgroundColor,
				boxShadow: `0 20rpx 36rpx ${this.resolvedTheme.centerButtonShadowColor}`
			}
		},
		centerIconStyle() {
			return {
				width: this.scaledPx(42),
				height: this.scaledPx(42)
			}
		},
		iconWrapStyle() {
			return {
				width: this.scaledPx(68),
				height: this.scaledPx(48)
			}
		},
		iconStyle() {
			return {
				width: this.scaledPx(46),
				height: this.scaledPx(46)
			}
		},
		itemStyle() {
			return {
				padding: `${this.scaledNumber(6)}px 0 ${this.scaledNumber(4)}px`
			}
		},
		badgeStyle() {
			return {
				background: this.resolvedTheme.badgeBackgroundColor,
				borderColor: this.resolvedTheme.badgeBorderColor,
				top: `${this.scaledNumber(-8)}px`,
				right: `${this.scaledNumber(-2)}px`,
				minWidth: this.scaledPx(32),
				height: this.scaledPx(32),
				padding: `0 ${this.scaledNumber(8)}px`,
				borderWidth: `${this.scaledNumber(2)}px`
			}
		},
		badgeDotStyle() {
			return {
				background: this.resolvedTheme.badgeBackgroundColor,
				borderColor: this.resolvedTheme.badgeBorderColor,
				top: `${this.scaledNumber(-6)}px`,
				right: `${this.scaledNumber(2)}px`,
				width: this.scaledPx(16),
				height: this.scaledPx(16),
				borderWidth: `${this.scaledNumber(2)}px`
			}
		},
		badgeTextStyle() {
			return {
				fontSize: this.scaledPx(18)
			}
		}
	},
	watch: {
		value() {
			this.$nextTick(() => {
				this.updatePillPosition()
			})
		},
		currentIndex() {
			this.$nextTick(() => {
				this.updatePillPosition()
			})
		},
		items: {
			deep: true,
			handler() {
				this.$nextTick(() => {
					this.updatePillPosition()
				})
			}
		},
		layout: {
			deep: true,
			handler() {
				this.$nextTick(() => {
					this.updatePillPosition()
				})
			}
		}
	},
	mounted() {
		this.$nextTick(() => {
			this.updatePillPosition()
		})
		if (typeof uni.onWindowResize === 'function') {
			uni.onWindowResize(this.handleWindowResize)
		}
	},
	beforeDestroy() {
		if (typeof uni.offWindowResize === 'function') {
			uni.offWindowResize(this.handleWindowResize)
		}
	},
	methods: {
		escapeSvgColor(color) {
			if (!color) {
				return { css: '#FFFCF7', r: 1, g: 0.988235, b: 0.968627, a: 1 }
			}
			const rgba = this.parseColorToRgba(color)
			return {
				css: `rgba(${rgba.r},${rgba.g},${rgba.b},${rgba.a})`,
				r: +(rgba.r / 255).toFixed(6),
				g: +(rgba.g / 255).toFixed(6),
				b: +(rgba.b / 255).toFixed(6),
				a: +rgba.a
			}
		},
		parseColorToRgba(color) {
			if (color.startsWith('#')) {
				const hex = color.slice(1)
				const normalized = hex.length === 3
					? hex.split('').map((char) => char + char).join('')
					: hex
				const r = parseInt(normalized.slice(0, 2), 16)
				const g = parseInt(normalized.slice(2, 4), 16)
				const b = parseInt(normalized.slice(4, 6), 16)
				return { r, g, b, a: 1 }
			}
			const rgbaMatch = color.match(/rgba?\(([^)]+)\)/i)
			if (rgbaMatch) {
				const parts = rgbaMatch[1].split(',').map((part) => part.trim())
				return {
					r: Number(parts[0]) || 0,
					g: Number(parts[1]) || 0,
					b: Number(parts[2]) || 0,
					a: parts.length > 3 ? Number(parts[3]) || 0 : 1
				}
			}
			return { r: 255, g: 252, b: 247, a: 1 }
		},
		handleWindowResize() {
			this.$nextTick(() => {
				this.updatePillPosition()
			})
		},
		scaledNumber(rpxValue) {
			return Math.round(uni.upx2px(rpxValue) * this.layoutScale * 100) / 100
		},
		scaledPx(rpxValue) {
			return `${this.scaledNumber(rpxValue)}px`
		},
		itemAnchorId(item, index) {
			return `bottom-nav-item-${item.id || index}`
		},
		resolveIcon(item, isActive) {
			return isActive ? (item.activeIcon || item.inactiveIcon) : item.inactiveIcon
		},
		labelStyle(isActive) {
			return {
				marginTop: this.scaledPx(10),
				fontSize: this.scaledPx(20),
				lineHeight: 1.2,
				color: isActive ? this.resolvedTheme.activeLabelColor : this.resolvedTheme.labelColor
			}
		},
		handleItemTap(index, item) {
			if (item && item.disabled) {
				return
			}
			const previousIndex = this.resolvedCurrentIndex
			this.$emit('itemTap', { index, item, previousIndex })
			this.$emit('input', index)
			this.$emit('update:currentIndex', index)
			if (previousIndex !== index) {
				this.$emit('change', { index, item, previousIndex })
			}
		},
		handleCenterTap() {
			this.$emit('centerTap', this.centerAction)
		},
		formatBadge(value) {
			return value > 99 ? '99+' : String(value)
		},
		updatePillPosition() {
			if (!this.items.length) {
				return
			}

			const activeItem = this.items[this.resolvedCurrentIndex]
			if (!activeItem) {
				return
			}

			const query = uni.createSelectorQuery().in(this)
			query.select('#bottom-nav-shell').boundingClientRect()
			query.select(`#${this.itemAnchorId(activeItem, this.resolvedCurrentIndex)}`).boundingClientRect()
			query.exec((result) => {
				const shellRect = result && result[0]
				const itemRect = result && result[1]
				if (!shellRect || !itemRect) {
					return
				}

				this.shellWidthPx = shellRect.width

				const horizontalPadding = this.scaledNumber(this.resolvedLayout.pillHorizontalPadding)
				const itemWidth = Math.max(
					this.scaledNumber(this.resolvedLayout.pillMinWidth),
					Math.min(itemRect.width + horizontalPadding, shellRect.width)
				)
				const itemCenter = itemRect.left - shellRect.left + (itemRect.width / 2)
				this.pillWidthPx = itemWidth
				this.pillLeftPx = Math.max(
					0,
					Math.min(itemCenter - (itemWidth / 2), shellRect.width - itemWidth)
				)
			})
		}
	}
}
</script>

<style lang="scss" scoped>
.bottom-nav {
	z-index: 4;
}

.bottom-nav__frame {
	position: relative;
	height: 100%;
	margin: 0 auto;
}

.bottom-nav__shell {
	position: absolute;
	left: 0;
	right: 0;
	bottom: 0;
	background-position: center bottom;
	background-size: 100% 100%;
	background-repeat: no-repeat;
	overflow: visible;
}

.bottom-nav__content {
	position: relative;
	display: flex;
	align-items: stretch;
	height: 100%;
	z-index: 2;
}

.bottom-nav__group {
	flex: 1;
	display: flex;
	align-items: stretch;
	justify-content: space-between;
	min-width: 0;
}

.bottom-nav__pill {
	position: absolute;
	left: 0;
	border-radius: 999rpx;
	transition: transform 320ms ease, width 320ms ease;
}

.bottom-nav__item {
	position: relative;
	z-index: 2;
	flex: 1;
	min-width: 0;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	background: transparent;
	border: 0;
	border-radius: 0;
	line-height: 1;
}

.bottom-nav__item::after {
	border: 0;
}

.bottom-nav__item--disabled {
	opacity: 0.42;
}

.bottom-nav__icon-wrap {
	position: relative;
	display: flex;
	align-items: center;
	justify-content: center;
}

.bottom-nav__label {
	white-space: nowrap;
}

.bottom-nav__item--active .bottom-nav__label {
	font-weight: 500;
}

.bottom-nav__badge {
	position: absolute;
	display: flex;
	align-items: center;
	justify-content: center;
	border: 2rpx solid rgba(255, 255, 255, 0.92);
}

.bottom-nav__badge--count {
	border-radius: 999rpx;
}

.bottom-nav__badge--dot {
	border-radius: 999rpx;
}

.bottom-nav__badge-text {
	line-height: 1;
	font-weight: 600;
	color: #FFFFFF;
}

.bottom-nav__center-button {
	position: absolute;
	left: 50%;
	top: 0;
	border-radius: 999rpx;
	border: 2rpx solid;
	display: flex;
	align-items: center;
	justify-content: center;
	padding: 0;
	z-index: 3;
}

.bottom-nav__center-button::after {
	border: 0;
}

.bottom-nav__center-button--pressed {
	opacity: 0.9;
}
</style>
