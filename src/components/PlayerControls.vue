<template>
  <div class="track-controls">
    <transition name="progress-loading" mode="out-in">
      <div v-if="loadingCurrentTrack" key="1" class="track-loading">
        <loader type="spinner" label="Loading track"></loader><span>Loading track...</span>
      </div>
      <div v-else key="2" class="track-progress">
        <span class="track-current-time">{{currentTime | formatTime}}</span>
        <progress ref="progress" class="track-progress-bar" :value="trackProgress" max="1"></progress>
        <span class="track-duration">{{duration | formatTime}}</span>
      </div>
    </transition>
    <div class="track-buttons">
      <button @click="togglePlayback" class="btn" theme="dark" title="Stop playback">
        <stop-icon></stop-icon>        
      </button>
      <button v-if="inPlaylist" @click="removeFromPlaylist" class="btn" theme="dark" title="Remove this track from the playlist">
        <playlist-check-icon></playlist-check-icon>
      </button>
      <button v-else @click="addToPlaylist" class="btn" theme="dark" title="Add this track to the playlist">
        <playlist-add-icon></playlist-add-icon>
      </button>
      <a @click="stopPlayback" :href="soundcloudUrl" class="btn" theme="dark" target="_blank" rel="noopener" title="Open this track on SoundCloud">
        <soundcloud-icon></soundcloud-icon>
      </a>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import Loader from './Loader'
import PlaylistAddIcon from 'vue-material-design-icons/playlist-plus'
import PlaylistCheckIcon from 'vue-material-design-icons/playlist-check'
import SoundcloudIcon from 'vue-material-design-icons/soundcloud'
import StopIcon from 'vue-material-design-icons/stop-circle-outline'

export default {
  name: 'PlayerControls',
  components: {
    Loader,
    PlaylistAddIcon,
    PlaylistCheckIcon,
    SoundcloudIcon,
    StopIcon
  },
  
  computed: {
    ...mapGetters([
      'currentTime',
      'currentTrack',
      'duration',
      'isPlaying',
      'inPlaylist',
      'loadingCurrentTrack',
      'soundcloudUrl',
      'trackProgress'
    ])
  },

  filters: {
    formatTime(ms) {
      var mins = Math.floor(ms / 60000);
      var secs = (Math.floor(ms % 60000 / 1000) < 10 ? '0' : '') + Math.floor(ms % 60000 / 1000);
      return mins + ':' + secs;
    }
  },

  methods: {
    addToPlaylist() {
      this.$store.commit('ADD_TO_PLAYLIST', this.currentTrack);
    },

    removeFromPlaylist() {
      this.$store.commit('REMOVE_FROM_PLAYLIST', this.currentTrack);
    },

    togglePlayback() {
      this.$store.commit('SET_CURRENT_TRACK', this.currentTrack);
    },
    
    stopPlayback() {
      if (this.isPlaying) this.$store.state.current_track.is_playing = false;
    }
  }
}
</script>