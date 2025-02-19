<template>
    <section id="playlistWrapper">
        <h1 class="heading">
            <span>{{ playlist.name }}
                <i class="fa fa-angle-down toggler"
                    v-show="isPhone && !showingControls"
                    @click="showingControls = true"></i>
                <i class="fa fa-angle-up toggler"
                    v-show="isPhone && showingControls"
                    @click.prevent="showingControls = false"></i>

                <span class="meta" v-show="meta.songCount">
                    {{ meta.songCount }} {{ meta.songCount | pluralize 'song' }}
                    •
                    {{ meta.totalLength }}
                </span>
            </span>

            <div class="buttons" v-show="!isPhone || showingControls">
                <button class="play-shuffle btn btn-orange"
                    @click.prevent="shuffle"
                    v-if="playlist.songs.length && selectedSongs.length < 2"
                >
                    <i class="fa fa-random"></i> All
                </button>
                <button class="play-shuffle" @click.prevent="shuffleSelected" v-if="selectedSongs.length > 1">
                    <i class="fa fa-random"></i> Selected
                </button>
                <button class="btn btn-green" @click.prevent.stop="showingAddToMenu = !showingAddToMenu" v-if="selectedSongs.length">
                    {{ showingAddToMenu ? 'Cancel' : 'Add To…' }}
                </button>
                <button class="del btn btn-red"
                    title="Delete this playlist"
                    @click.prevent="del">
                    <i class="fa fa-times"></i> Playlist
                </button>

                <add-to-menu :songs="selectedSongs" :showing="showingAddToMenu && playlist.songs.length"><add-to-menu>
            </div>
        </h1>

        <song-list v-show="playlist.songs.length"
            :items="playlist.songs"
            :selected-songs.sync="selectedSongs"
            :playlist="playlist"
            type="playlist">
        </song-list>

        <div v-show="!playlist.songs.length" class="none">
            The playlist is currently empty. You can fill it up by dragging songs into its name in the sidebar,
            or use the &quot;Add To…&quot; button.
        </div>
    </section>
</template>

<script>
    import isMobile from 'ismobilejs';

    import playlistStore from '../../../stores/playlist';
    import playback from '../../../services/playback';
    import hasSongList from '../../../mixins/has-song-list';

    export default {
        mixins: [hasSongList],

        data() {
            return {
                playlist: playlistStore.stub,
                isPhone: isMobile.phone,
                showingControls: false,
            };
        },

        events: {
            /**
             * Listen to 'main-content-view:load' event (triggered from $root currently)
             * to load the requested playlist into view if applicable.
             *
             * @param {String} view     The view's name.
             * @param {Object} playlist
             */
            'main-content-view:load': function (view, playlist) {
                if (view === 'playlist') {
                    this.playlist = playlist;
                }
            },
        },

        methods: {
            /**
             * Shuffle the songs in the current playlist.
             */
            shuffle() {
                playback.queueAndPlay(this.playlist.songs, true);
            },

            /**
             * Delete the current playlist.
             */
            del() {
                playlistStore.delete(this.playlist, () => {
                    // Reset the current playlist to our stub, so that we don't encounter
                    // any property reference error.
                    this.playlist = playlistStore.stub;

                    // Switch back to Queue screen
                    this.$root.loadMainView('queue');
                });
            },
        },
    };
</script>

<style lang="sass">
    @import "../../../../sass/partials/_vars.scss";
    @import "../../../../sass/partials/_mixins.scss";

    #playlistWrapper {
        button.play-shuffle, button.del {
            i {
                margin-right: 0 !important;
            }
        }

        .none {
            color: $color2ndText;
            padding: 16px 24px;

            a {
                color: $colorHighlight;
            }
        }
    }
</style>
