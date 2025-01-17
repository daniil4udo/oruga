<script setup lang="ts">
import { computed, type Ref } from "vue";

import { baseComponentProps } from "@/utils/SharedProps";
import {
    useComputedClass,
    useClassProps,
    useProviderChild,
} from "@/composables";

/**
 * A Slideshow item used by the carousel
 * @displayName Carousel Item
 */
defineOptions({
    isOruga: true,
    name: "OCarouselItem",
    configField: "carousel",
});

const props = defineProps({
    // add global shared props (will not be displayed in the docs)
    ...baseComponentProps,
    /** Make item clickable */
    clickable: { type: Boolean, default: false },
    // add class props (will not be displayed in the docs)
    ...useClassProps(["itemClass", "itemActiveClass", "itemClickableClass"]),
});

// Inject functionalities and data from the parent carousel component
const { parent, item } = useProviderChild<Ref<any>>();

const isActive = computed(() => parent.value.activeIndex === item.value.index);

const itemStyle = computed(() => ({ width: `${parent.value.itemWidth}px` }));

function onClick(event: MouseEvent): void {
    if (isActive.value) parent.value.onClick(event);
    if (props.clickable) parent.value.setActive(item.value.index);
}

// --- Computed Component Classes ---

const itemClasses = computed(() => [
    useComputedClass("itemClass", "o-car__item"),
    {
        [useComputedClass("itemActiveClass", "o-car__item--active")]:
            isActive.value,
    },
    {
        [useComputedClass("itemClickableClass", "o-car__item--clickable")]:
            props.clickable,
    },
]);
</script>

<template>
    <div
        :class="itemClasses"
        :style="itemStyle"
        :data-id="`carousel-${item.identifier}`"
        data-oruga="carousel-item"
        @click="onClick">
        <!--
            @slot Default content
        -->
        <slot />
    </div>
</template>
