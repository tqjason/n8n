<script setup lang="ts">
import { computed } from 'vue';
import TitledList from '@/components/TitledList.vue';
import { useNodeHelpers } from '@/composables/useNodeHelpers';
import { useCanvasNode } from '@/composables/useCanvasNode';

const nodeHelpers = useNodeHelpers();

const {
	hasPinnedData,
	issues,
	hasIssues,
	executionStatus,
	executionWaiting,
	executionRunning,
	hasRunData,
	runDataIterations,
	isDisabled,
} = useCanvasNode();

const hideNodeIssues = computed(() => false); // @TODO Implement this
</script>

<template>
	<div
		v-if="hasIssues && !hideNodeIssues"
		:class="[$style.status, $style.issues]"
		data-test-id="node-issues"
	>
		<N8nTooltip :show-after="500" placement="bottom">
			<template #content>
				<TitledList :title="`${$locale.baseText('node.issues')}:`" :items="issues" />
			</template>
			<FontAwesomeIcon icon="exclamation-triangle" />
		</N8nTooltip>
	</div>
	<div
		v-else-if="executionWaiting || executionStatus === 'waiting'"
		:class="[$style.status, $style.waiting]"
	>
		<N8nTooltip placement="bottom">
			<template #content>
				<div v-text="executionWaiting"></div>
			</template>
			<FontAwesomeIcon icon="clock" />
		</N8nTooltip>
	</div>
	<div
		v-else-if="hasPinnedData && !nodeHelpers.isProductionExecutionPreview.value && !isDisabled"
		data-test-id="canvas-node-status-pinned"
		:class="[$style.status, $style.pinnedData]"
	>
		<FontAwesomeIcon icon="thumbtack" />
	</div>
	<div v-else-if="executionStatus === 'unknown'">
		<!-- Do nothing, unknown means the node never executed -->
	</div>
	<div
		v-else-if="executionRunning || executionStatus === 'running'"
		data-test-id="canvas-node-status-running"
		:class="[$style.status, $style.running]"
	>
		<FontAwesomeIcon icon="sync-alt" spin />
	</div>
	<div
		v-else-if="hasRunData"
		data-test-id="canvas-node-status-success"
		:class="[$style.status, $style.runData]"
	>
		<FontAwesomeIcon icon="check" />
		<span v-if="runDataIterations > 1" :class="$style.count"> {{ runDataIterations }}</span>
	</div>
</template>

<style lang="scss" module>
.status {
	display: flex;
	align-items: center;
	gap: var(--spacing-5xs);
}

.runData {
	font-weight: 600;
	color: var(--color-success);
}

.pinnedData {
	color: var(--color-secondary);
}

.running {
	width: calc(100% - 2 * var(--canvas-node--status-icons-offset));
	height: calc(100% - 2 * var(--canvas-node--status-icons-offset));
	display: flex;
	align-items: center;
	justify-content: center;
	font-size: 3.75em;
	color: hsla(var(--color-primary-h), var(--color-primary-s), var(--color-primary-l), 0.7);
}

.issues {
	color: var(--color-danger);
	cursor: default;
}

.count {
	font-size: var(--font-size-s);
}
</style>