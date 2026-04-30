<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch } from 'vue';

/**
 * Turn.js 配置选项接口
 */
interface TurnOptions {
  /** 翻书容器宽度 */
  width: number;
  /** 翻书容器高度 */
  height: number;
  /** 是否自动居中 */
  autoCenter?: boolean;
  /** 显示模式：single（单页）或 double（双页） */
  display?: 'single' | 'double';
  /** 是否启用硬件加速 */
  acceleration?: boolean;
  /** 页面翻转时的高度 */
  elevation?: number;
  /** 是否启用渐变效果 */
  gradients?: boolean;
  /** 页面翻转持续时间（毫秒） */
  duration?: number;
  /** 事件回调 */
  when?: {
    /** 页面翻转完成后触发 */
    turned?: (event: Event, page: number) => void;
    /** 开始翻转时触发 */
    turning?: (event: Event, page: number, view: number[]) => void;
    /** 第一次初始化时触发 */
    first?: (event: Event) => void;
    /** 最后一页时触发 */
    last?: (event: Event) => void;
    /** 点击时触发 */
    start?: (event: Event, pageObject: unknown, corner: string) => void;
    /** 动画结束时触发 */
    end?: (event: Event, pageObject: unknown, corner: string) => void;
    /** 缺少页面时触发 */
    missing?: (event: Event, pages: number[]) => void;
  };
}

/**
 * Flipbook 组件 Props 接口
 */
interface Props {
  /** Turn.js 配置选项 */
  options?: Partial<TurnOptions>;
  /** 自定义类名 */
  className?: string;
  /** 自定义样式 */
  style?: Record<string, string>;
  /** 当前页码（支持 v-model） */
  modelValue?: number;
}

/**
 * 组件事件定义
 */
interface Emits {
  /** 更新当前页码 */
  'update:modelValue': [page: number];
  /** 页面翻转完成事件 */
  turned: [page: number];
  /** 开始翻转事件 */
  turning: [page: number, view: number[]];
}

const props = withDefaults(defineProps<Props>(), {
  options: () => ({}),
  className: '',
  style: () => ({}),
  modelValue: 1,
});

const emit = defineEmits<Emits>();

/** 翻书容器引用 */
const flipbookRef = ref<HTMLDivElement | null>(null);
/** 是否已初始化 */
const isInitialized = ref(false);

/**
 * 检查 jQuery 和 Turn.js 是否可用
 */
const isTurnAvailable = (): boolean => {
  if (typeof window === 'undefined') return false;
  const $ = window.$;
  if (!$) return false;
  // eslint-disable-next-line @typescript-eslint/no-unsafe-member-access
  return typeof $.fn?.turn === 'function';
};

/**
 * 获取默认配置选项
 */
const getDefaultOptions = (): TurnOptions => ({
  width: 800,
  height: 600,
  autoCenter: true,
  display: 'single',
  acceleration: true,
  elevation: 50,
  gradients: true,
  duration: 600,
  when: {
    turned: (event: Event, page: number) => {
      emit('update:modelValue', page);
      emit('turned', page);
      const target = event.target as HTMLElement;
      console.log('Current view: ', window.$(target).turn('view'));
    },
    turning: (_event: Event, page: number, view: number[]) => {
      emit('turning', page, view);
    },
  },
});

/**
 * 初始化 Turn.js
 */
const initTurn = () => {
  if (!flipbookRef.value || typeof window === 'undefined') return;

  if (!isTurnAvailable()) {
    console.error('jQuery 或 Turn.js 未加载');
    return;
  }

  const $ = window.$;

  // 检查是否有子页面元素
  const pageCount = $(flipbookRef.value).children().length;
  if (pageCount === 0) {
    console.error('Flipbook: 没有页面元素，跳过初始化');
    return;
  }

  const defaultOptions = getDefaultOptions();
  const mergedOptions = { ...defaultOptions, ...props.options };

  // 合并事件回调
  if (props.options?.when) {
    mergedOptions.when = { ...defaultOptions.when, ...props.options.when };
  }

  // 销毁已存在的实例
  if (isInitialized.value) {
    try {
      $(flipbookRef.value).turn('destroy');
    } catch {
      // 忽略销毁错误
    }
  }

  $(flipbookRef.value).turn(mergedOptions);
  isInitialized.value = true;

  // 设置初始页码
  if (props.modelValue && props.modelValue > 0) {
    $(flipbookRef.value).turn('page', props.modelValue);
  }
};

/**
 * 销毁 Turn.js 实例
 */
const destroyTurn = () => {
  if (!flipbookRef.value || !isInitialized.value) return;

  if (isTurnAvailable()) {
    window.$(flipbookRef.value).turn('destroy');
  }
  isInitialized.value = false;
};

/**
 * 跳转到指定页面
 */
const goToPage = (page: number) => {
  if (!flipbookRef.value || !isInitialized.value) return;

  if (isTurnAvailable()) {
    window.$(flipbookRef.value).turn('page', page);
  }
};

/**
 * 获取当前视图页码
 */
const getView = (): number[] => {
  if (!flipbookRef.value || !isInitialized.value) return [];

  if (isTurnAvailable()) {
    return window.$(flipbookRef.value).turn('view');
  }
  return [];
};

/**
 * 获取总页数
 */
const getPages = (): number => {
  if (!flipbookRef.value || !isInitialized.value) return 0;

  if (isTurnAvailable()) {
    return window.$(flipbookRef.value).turn('pages');
  }
  return 0;
};

/**
 * 下一页
 */
const next = () => {
  if (!flipbookRef.value || !isInitialized.value) return;

  if (isTurnAvailable()) {
    window.$(flipbookRef.value).turn('next');
  }
};

/**
 * 上一页
 */
const previous = () => {
  if (!flipbookRef.value || !isInitialized.value) return;

  if (isTurnAvailable()) {
    window.$(flipbookRef.value).turn('previous');
  }
};

/**
 * 监听 modelValue 变化
 */
watch(
  () => props.modelValue,
  (newPage) => {
    if (newPage && newPage > 0) {
      goToPage(newPage);
    }
  },
);

/**
 * 监听 options 变化，重新初始化
 */
watch(
  () => props.options,
  () => {
    destroyTurn();
    initTurn();
  },
  { deep: true },
);

onMounted(() => {
  initTurn();
});

onUnmounted(() => {
  destroyTurn();
});

/**
 * 暴露方法给父组件
 */
defineExpose({
  goToPage,
  getView,
  getPages,
  next,
  previous,
});
</script>

<template>
  <div ref="flipbookRef" :class="['flipbook', className]" :style="style">
    <slot />
  </div>
</template>

<style scoped>
.flipbook {
  position: relative;
  overflow: hidden;
}

/* Turn.js 页面的背景色 */
.flipbook :deep(.page) {
  background-color: #fff;
}
</style>
