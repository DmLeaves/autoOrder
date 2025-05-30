<template>
  <div class="donut-chart-container" :style="{ width: `${size}px`, height: `${size}px` }">
    <svg :width="size" :height="size" viewBox="0 0 36 36">
      <!-- 背景圆环 -->
      <circle
          cx="18"
          cy="18"
          r="15.915"
          :stroke="bgColor"
          :stroke-width="thickness"
          fill="none"
          stroke-linecap="round"
      />

      <!-- 前景圆环（进度） -->
      <circle
          cx="18"
          cy="18"
          r="15.915"
          :stroke="color"
          :stroke-width="thickness"
          fill="none"
          :stroke-dasharray="`${dashArray} ${circumference}`"
          stroke-dashoffset="0"
          class="donut-segment"
          stroke-linecap="round"
      />
    </svg>

    <!-- 居中显示文本 -->
    <div class="donut-label">{{ formattedValue }}</div>
  </div>
</template>

<script>
import { computed } from 'vue';

export default {
  name: 'DonutChart',
  props: {
    value: {
      type: Number,
      required: true,
      validator: (val) => val >= 0 && val <= 1
    },
    size: {
      type: Number,
      default: 120
    },
    thickness: {
      type: Number,
      default: 8
    },
    color: {
      type: String,
      default: 'var(--accent-blue)'
    },
    bgColor: {
      type: String,
      default: 'rgba(255, 255, 255, 0.1)'
    },
    format: {
      type: String,
      default: 'percent' // 'percent' 或 'value'
    },
    minVisible: {
      type: Number,
      default: 0.02 // 最小可见度（2%）
    }
  },
  setup(props) {
    // 计算圆的周长
    const radius = 15.915;
    const circumference = computed(() => {
      return 2 * Math.PI * radius;
    });

    // 计算仪表盘数组值
    const dashArray = computed(() => {
      // 确保超小值至少有最小可见度
      const visibleValue = props.value < props.minVisible && props.value > 0
          ? props.minVisible
          : props.value;

      return visibleValue * circumference.value;
    });

    // 格式化显示值
    const formattedValue = computed(() => {
      if (props.format === 'percent') {
        return `${Math.round(props.value * 100)}%`;
      }
      return props.value.toString();
    });

    return {
      dashArray,
      formattedValue,
      circumference
    };
  }
}
</script>

<style scoped>
.donut-chart-container {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}

svg {
  transform: rotate(-90deg);
  filter: drop-shadow(0px 0px 3px rgba(0, 0, 0, 0.5));
}

.donut-segment {
  transition: stroke-dasharray 0.8s ease;
}

.donut-label {
  position: absolute;
  font-size: 1.8rem;
  font-weight: 600;
  color: var(--text-primary);
  display: flex;
  align-items: center;
  justify-content: center;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
}
</style>