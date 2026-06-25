# Ryan Common Bottom Navigation

An iOS-style capsule bottom navigation component for uni-app, with a floating center action button, animated active pill, scaffold container, and configurable notch geometry.

## Components

- `ryan-common_bottom_navigation`
- `ryan-common_bottom_navigation_scaffold`

## Features

- Reusable bottom navigation for uni-app projects
- Floating center action button with notch-matched shell
- Animated active capsule background
- Configurable badge count and dot badge
- Responsive layout scaling for different widths
- Scaffold wrapper for page switching scenarios
- Configurable notch depth and shell shape parameters

## Install

Place the module in:

```text
uni_modules/ryan-common_bottom_navigation
```

## Basic Usage

```vue
<template>
	<ryan-common_bottom_navigation
		:items="items"
		:current-index="currentIndex"
		:center-action="centerAction"
		:layout="layout"
		@change="handleChange"
		@centerTap="handleCenterTap"
	/>
</template>
```

## Scaffold Usage

```vue
<template>
	<ryan-common_bottom_navigation_scaffold
		:items="items"
		:pages="pages"
		:current-index="currentIndex"
		:center-action="centerAction"
		:layout="layout"
		@change="handleChange"
	>
		<template #page="{ page, index }">
			<component :is="page.component" :key="index" />
		</template>
	</ryan-common_bottom_navigation_scaffold>
</template>
```

## Item Format

```js
[
	{
		id: 'bookshelf',
		label: 'Bookshelf',
		inactiveIcon: '/static/icons/book.svg',
		activeIcon: '/static/icons/book-fill.svg',
		badge: { type: 'count', value: 8 }
	}
]
```

## Main Props

### `ryan-common_bottom_navigation`

- `items`: navigation item array
- `value` / `currentIndex`: active tab index
- `centerAction`: center button config object
- `shellInset`: horizontal inset of the navigation shell
- `theme`: color and background theme overrides
- `layout`: size, spacing, notch, and scaling overrides
- `absolute`: whether the nav is absolutely positioned at the bottom

### `ryan-common_bottom_navigation_scaffold`

- `pages`: page config array
- `contentPaddingBottom`: custom bottom padding for page content
- `absoluteNavigation`: whether the navigation stays absolute

## Events

- `input`
- `update:currentIndex`
- `itemTap`
- `change`
- `centerTap`

## Notch Configuration

You can control the shell notch through the `layout` prop:

```js
layout: {
	notchDepth: 10,
	notchShoulderLeft: 139,
	notchShoulderCurveLeft: 157,
	notchArcStartX: 167,
	notchArcEndX: 223,
	notchShoulderCurveRight: 233,
	notchShoulderRight: 251,
	notchArcRadius: 38
}
```

Increase `notchDepth` to make the center indentation deeper.

## Optional Static Asset

If you prefer a static shell background image instead of generated SVG, use:

```text
/uni_modules/ryan-common_bottom_navigation/static/nav-shell.svg
```
