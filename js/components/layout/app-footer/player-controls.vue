<template>
  <div class="side player-controls">
    <i
      @click.prevent="playPrev"
      class="prev fa fa-step-backward control"
      role="button"
      tabindex="0"
      title="Play previous song"
      data-testid="play-prev-btn"
    />

    <span class="album-thumb-wrapper">
      <span :style="{ backgroundImage: `url('${cover}')` }" class="album-thumb"></span>
      <span
        @click.prevent="toggle"
        class="play"
        role="button"
        tabindex="0"
        title="Play or resume"
        data-testid="play-btn"
        v-if="shouldDisplayPlayButton"
      >
        <i class="fa fa-play"></i>
      </span>
      <span
        @click.prevent="toggle"
        class="pause"
        role="button"
        tabindex="0"
        title="Pause"
        data-testid="pause-btn"
        v-else
      >
        <i class="fa fa-pause"></i>
      </span>
    </span>

    <i
      @click.prevent="playNext"
      class="next fa fa-step-forward control"
      role="button"
      tabindex="0"
      title="Play next song"
      data-testid="play-next-btn"
    />
  </div>
</template>

<script lang="ts">
import Vue, { PropOptions } from 'vue'
import { playback } from '@/services'
import { getDefaultCover } from '@/utils'

export default Vue.extend({
  props: {
    song: {
      type: Object
    } as PropOptions<Song>
  },

  computed: {
    cover (): string {
      return this.song && this.song.album.cover ? this.song.album.cover : getDefaultCover()
    },

    shouldDisplayPlayButton (): boolean {
      return !this.song || (this.song && this.song.playbackState !== 'Playing')
    }
  },

  methods: {
    playPrev: (): void => playback.playPrev(),
    playNext: (): void => playback.playNext(),
    toggle: (): void => playback.toggle()
  }
})
</script>

<style lang="scss" scoped>
@import "~#/partials/_mixins.scss";

.player-controls {
  @include vertical-center();
  flex: 0 0 256px;
  font-size: 1.8rem;

  &:hover {
    .album-thumb-wrapper {
      margin-left: 1rem;
      margin-right: 1rem;
    }

    .album-thumb {
      filter: brightness(.4);
    }

    .prev, .next {
      opacity: 1;
    }

    .play, .pause {
      opacity: .7;
    }
  }

  .album-thumb-wrapper {
    flex: 0 0 calc(var(--footer-height) + 30px);
    height: calc(var(--footer-height) + 30px);
    transition: .2s ease-out;
    position: relative;
    overflow: hidden;
    border-radius: 50%;
    box-shadow: 0 0 20px rgba(0, 0, 0, .2);
    margin-left: -3rem;
    margin-right: -3rem;

    @media (hover: none) {
      margin-left: 1rem;
      margin-right: 1rem;
    }

    @include vertical-center();

    &:hover {
      .album-thumb {
        transform: scale(1.1);

        &::after {
          display: block;
        }
      }

      .play, .pause {
        opacity: 1;
      }
    }
  }

  .album-thumb {
    position: relative;
    background-color: transparent;
    height: 100%;
    width: 100%;
    left: 0;
    top: 0;
    z-index: 0;
    border-radius: 50%;
    background-size: cover;
    transition: .2s ease-out;
    overflow: hidden;

    &::after {
      content: "";
      position: absolute;
      height: 100%;
      width: 100%;
      top: 0;
      left: 0;
      background: linear-gradient(135deg, rgba(235,241,246,0) 0%,rgba(255,255,255,.3) 41%,rgba(255,255,255,0) 41%);
    }
  }

  .prev, .next {
    transition: .4s ease-out;
    opacity: 0;
    padding: 1rem;
    margin: -.75rem;
  }

  .play, .pause {
    @include inset-when-pressed();

    border-radius: 50%;
    position: absolute;
    top: 0;
    left: 0;
    transition: opacity .4s ease-out;
    font-size: 3rem;
    display: inline-block;
    width: 100%;
    height: 100%;
    line-height: calc(var(--footer-height) + 30px);
    text-align: center;
    text-indent: 2px;
    color: var(--color-text-primary);
    opacity: 0;
    text-shadow: 0 0 1px rgba(0, 0, 0, 0.5);
  }

  .pause {
    text-indent: 0;
    font-size: 2rem;
  }

  .enabled {
    opacity: 1;
  }

  @media only screen and (max-width: 768px) {
    flex: 1;

    &::before {
      display: none;
    }

    .album-thumb-wrapper {
      flex: 0 0 48px;
      height: 48px;
      box-shadow: 0 0 0 1px var(--color-text-secondary);
    }

    .album-thumb {
      background-image: none !important;

      &::after {
        opacity: 0;
      }
    }

    .play, .pause {
      line-height: 48px;
    }

    .prev, .next, .play, .pause {
      opacity: 1;
      font-size: 2rem;
      color: var(--color-text-secondary);
    }
  }
}
</style>
