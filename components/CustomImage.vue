<template>
  <div :style="placeholderStyle">
    <progressive-background
      :imageCustomClass="mainCls"
      :src="src"
      placeholder=""
    >
      <div slot="content" slot-scope="{ visible }">
        <div v-if="visible">
          <div
            :style="{
              background: themeConfig.primaryImage,
              ...placeholderStyle,
            }"
            v-bind="$attrs"
          />
        </div>
      </div>
    </progressive-background>
  </div>
</template>

<script>
import { css } from '@emotion/css';
import { themeConfig } from '../utils/constants';

const mainCls = css`
  img {
    opacity: 1;
    transition: opacity 0.25s ease-in;
  }

  img.loading-img {
    opacity: 0.5;
  }
`;

export default {
  name: 'AppImage',
  props: {
    src: String,
    srcSet: String,
    className: String,
    placeholderStyle: Object,
    alt: String,
  },
  data() {
    return {
      mainCls,
      themeConfig,
    };
  },
  components: {},
};
</script>
