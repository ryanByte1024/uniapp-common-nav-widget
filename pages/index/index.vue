<template>
	<view class="page">
		<RyanCommonBottomNavigationScaffold
			:items="navItems"
			:pages="pages"
			:current-index="currentIndex"
			:center-action="centerAction"
			:layout="navLayout"
			@change="handleNavigationChange"
			@centerTap="handleCenterTap"
		>
			<template #page="{ page }">
				<scroll-view scroll-y class="page-scroll" show-scrollbar="false">
					<view class="page-content">
						<view
							class="hero-icon"
							:style="{ backgroundColor: page.accentSoft }"
						>
							<image
								class="hero-icon__image"
								:src="iconSrc(page.icon)"
								mode="aspectFit"
							/>
						</view>

						<text class="page-title">{{ page.title }}</text>
						<text class="page-description">{{ page.description }}</text>

						<view class="mapping-card">
							<text class="mapping-card__title">Page & Navigation Mapping</text>
							<view class="mapping-chip-list">
								<view
									v-for="example in page.itemExamples"
									:key="example.label"
									class="mapping-chip"
								>
									<image
										class="mapping-chip__icon"
										:src="iconSrc(example.icon)"
										mode="aspectFit"
									/>
									<text class="mapping-chip__label">{{ example.label }}</text>
								</view>
							</view>
							<text class="mapping-card__description">
								Each example page corresponds to a bottom navigation item. Replace
								these with your actual business pages and pass them to your page
								container.
							</text>
						</view>
					</view>
				</scroll-view>
			</template>
		</RyanCommonBottomNavigationScaffold>
	</view>
</template>

<script>
import RyanCommonBottomNavigationScaffold from '../../uni_modules/ryan-bottom_nav/components/ryan-bottom_nav_scaffold/ryan-bottom_nav_scaffold.vue'

const navItems = [
	{
		id: 'bookshelf',
		label: 'Bookshelf',
		inactiveIcon: '/static/icons/book.svg',
		activeIcon: '/static/icons/book-fill.svg'
	},
	{
		id: 'stats',
		label: 'Stats',
		inactiveIcon: '/static/icons/chart.svg',
		activeIcon: '/static/icons/chart-fill.svg',
		badge: { type: 'count', value: 8 }
	},
	{
		id: 'review',
		label: 'Review',
		inactiveIcon: '/static/icons/review.svg',
		activeIcon: '/static/icons/review-fill.svg'
	},
	{
		id: 'profile',
		label: 'Profile',
		inactiveIcon: '/static/icons/profile.svg',
		activeIcon: '/static/icons/profile-fill.svg',
		badge: { type: 'dot' }
	}
]

const pages = [
	{
		title: 'Bookshelf',
		description: 'Book shelf home, recent reads, category navigation, and more.',
		icon: '/static/icons/book-fill.svg',
		accentSoft: 'rgba(185, 143, 84, 0.14)',
		itemExamples: [
			{ label: 'Bookshelf', icon: '/static/icons/book-fill.svg' },
			{ label: 'Recent Reads', icon: '/static/icons/time.svg' },
			{ label: 'Categories', icon: '/static/icons/grid.svg' }
		]
	},
	{
		title: 'Stats',
		description: 'Reading time, check-in trends, completion rate, and other stats.',
		icon: '/static/icons/chart-fill.svg',
		accentSoft: 'rgba(109, 138, 166, 0.14)',
		itemExamples: [
			{ label: 'Reading Time', icon: '/static/icons/chart-fill.svg' },
			{ label: 'Completion Rate', icon: '/static/icons/percent.svg' },
			{ label: 'Check-in Trends', icon: '/static/icons/graph.svg' }
		]
	},
	{
		title: 'Review',
		description: 'Retrospectives, knowledge review, timeline, and related content.',
		icon: '/static/icons/review-fill.svg',
		accentSoft: 'rgba(127, 140, 107, 0.14)',
		itemExamples: [
			{ label: 'Retrospect', icon: '/static/icons/review-fill.svg' },
			{ label: 'Timeline', icon: '/static/icons/clock.svg' },
			{ label: 'Knowledge Cards', icon: '/static/icons/doc.svg' }
		]
	},
	{
		title: 'Profile',
		description: 'Profile, settings, membership center, and other modules.',
		icon: '/static/icons/profile-fill.svg',
		accentSoft: 'rgba(156, 125, 125, 0.14)',
		itemExamples: [
			{ label: 'Profile', icon: '/static/icons/profile-fill.svg' },
			{ label: 'Settings', icon: '/static/icons/settings.svg' },
			{ label: 'Membership', icon: '/static/icons/star.svg' }
		]
	}
]

export default {
	components: {
		RyanCommonBottomNavigationScaffold
	},
	data() {
		return {
			currentIndex: 0,
			navItems,
			pages,
			navLayout: {
				height: 188,
				maxWidth: 702
			},
			centerAction: {
				icon: '/static/icons/pen.svg',
				visible: true
			}
		}
	},
	methods: {
		handleNavigationChange({ index }) {
			this.currentIndex = index
		},
		handleCenterTap() {
			uni.showToast({
				title: 'Center action',
				icon: 'none'
			})
		},
		iconSrc(path) {
			return path
		}
	}
}
</script>

<style lang="scss">
.page {
	min-height: 100vh;
	background: #F1E7DB;
}

.page-scroll {
	height: 100vh;
}

.page-content {
	padding: 72rpx 32rpx 0;
}

.hero-icon {
	width: 172rpx;
	height: 172rpx;
	margin: 0 auto;
	border-radius: 999rpx;
	display: flex;
	align-items: center;
	justify-content: center;
}

.hero-icon__image {
	width: 76rpx;
	height: 76rpx;
}

.page-title {
	display: block;
	margin-top: 48rpx;
	text-align: center;
	font-size: 68rpx;
	line-height: 1.06;
	font-weight: 600;
	color: #2E2B27;
}

.page-description {
	display: block;
	margin: 28rpx auto 0;
	max-width: 640rpx;
	text-align: center;
	font-size: 36rpx;
	line-height: 1.65;
	color: #5F5A53;
}

.mapping-card {
	margin-top: 44rpx;
	padding: 36rpx;
	border-radius: 44rpx;
	background: rgba(255, 255, 255, 0.62);
}

.mapping-card__title {
	display: block;
	font-size: 30rpx;
	line-height: 1.4;
	font-weight: 600;
	color: #4B4339;
}

.mapping-chip-list {
	display: flex;
	flex-wrap: wrap;
	gap: 20rpx;
	margin-top: 24rpx;
}

.mapping-chip {
	display: inline-flex;
	align-items: center;
	padding: 20rpx 24rpx;
	border-radius: 32rpx;
	background: rgba(255, 255, 255, 0.84);
}

.mapping-chip__icon {
	width: 32rpx;
	height: 32rpx;
}

.mapping-chip__label {
	margin-left: 12rpx;
	font-size: 26rpx;
	line-height: 1;
	font-weight: 500;
	color: #5F5A53;
}

.mapping-card__description {
	display: block;
	margin-top: 28rpx;
	font-size: 25rpx;
	line-height: 1.55;
	color: #6A6258;
}

</style>
