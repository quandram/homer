<template>
  <div>
    <div
      class="card"
      :style="`background-color:${item.background};`"
      :class="[item.class, { 'is-unavailable': !this.isAvailable }]"
    >
      <a :href="item.url" :target="item.target" rel="noreferrer">
        <div class="card-content">
          <div :class="mediaClass">
            <slot name="icon">
              <div v-if="item.logo" class="media-left">
                <figure class="image is-48x48">
                  <img :src="item.logo" :alt="`${item.name} logo`" />
                </figure>
              </div>
              <div v-if="item.icon" class="media-left">
                <figure class="image is-48x48">
                  <i style="font-size: 32px" :class="['fa-fw', item.icon]"></i>
                </figure>
              </div>
            </slot>
            <div class="media-content">
              <slot name="content">
                <p class="title">{{ item.name }}</p>
                <p v-if="item.quick" class="quicklinks">
                  <a
                    v-for="(link, linkIndex) in item.quick"
                    :key="linkIndex"
                    :style="`background-color:${link.color};`"
                    :href="link.url"
                    :target="link.target"
                    rel="noreferrer"
                  >
                    <span v-if="link.icon"
                      ><i
                        style="font-size: 12px"
                        :class="['fa-fw', link.icon]"
                      ></i
                    ></span>
                    {{ link.name }}
                  </a>
                </p>
                <p v-if="item.subtitle" class="subtitle">
                  {{ item.subtitle }}
                </p>
              </slot>
            </div>
            <slot name="indicator" class="indicator"></slot>
          </div>
          <div v-if="item.tag" class="tag" :class="item.tagstyle">
            <strong class="tag-text">#{{ item.tag }}</strong>
          </div>
        </div>
      </a>
    </div>
  </div>
</template>

<script>
export default {
  name: "Generic",
  props: {
    item: Object,
  },
  data: () => ({
    isAvailable: null,
  }),
  computed: {
    mediaClass: function () {
      return { media: true, "no-subtitle": !this.item.subtitle };
    },
  },
  created() {
    // Need no-cors to differentiate between CORS error and connection error
    fetch(this.item.url, { mode: "no-cors" })
      .then((response) => {
        if (!response.ok) {
          // There's some problem or other, probably CORS
        }
        this.isAvailable = true;
      })
      .catch(() => {
        this.isAvailable = false;
      });
  },
};
</script>

<style scoped lang="scss">
.is-unavailable {
  opacity: 0.5;
  pointer-events: none;
  .card-content {
    border: 0.2rem solid #ff0000;
  }
}
.media-left {
  .image {
    display: flex;
    align-items: center;
  }

  img {
    max-height: 100%;
    object-fit: contain;
  }
}

a[href=""] {
  pointer-events: none;
  cursor: default;
}

.quicklinks {
  float: right;
  a {
    font-size: 0.75rem;
    padding: 3px 6px;
    margin-left: 6px;
    border-radius: 100px;
    background-color: var(--background);
    z-index: 9999;
    pointer-events: all;
  }
}
</style>
