<template>
    <tr
        @dblclick.prevent="playRightAwayyyyyyy"
        class="song-item"
        :class="{ selected: selected, playing: song.playbackState === 'playing' || song.playbackState === 'paused' }"
    >
        <td class="title">{{ song.title }}</td>
        <td class="artist">{{ song.album.artist.name }}</td>
        <td class="album">{{ song.album.name }}</td>
        <td class="time">{{ song.fmtLength }}</td>
        <td class="play" @click.stop="doPlayback">
            <i class="fa fa-pause-circle" v-show="song.playbackState === 'playing'"></i>
            <i class="fa fa-play-circle" v-else></i>
        </td>
    </tr>
</template>

<script>
    import playback from '../../services/playback';
    import queueStore from '../../stores/queue';

    export default {
        props: ['song'],

        data() {
            return {
                selected: false,
            };
        },

        methods: {
            /**
             * Play the song right away.
             */
            playRightAwayyyyyyy() {
                if (!queueStore.contains(this.song)) {
                    queueStore.queueAfterCurrent(this.song);
                }

                playback.play(this.song);
            },

            /**
             * Take the right playback action based on the current playback state.
             */
            doPlayback() {
                switch (this.song.playbackState) {
                    case 'playing':
                        playback.pause();
                        break;
                    case 'paused':
                        playback.resume();
                        break;
                    default:
                        this.playRightAwayyyyyyy();
                        break;
                }
            },

            /**
             * Toggle the "selected" state of the current component.
             */
            toggleSelectedState() {
                this.selected = !this.selected;
            },

            /**
             * Select the current component (apply a CSS class on its DOM).
             */
            select() {
                this.selected = true;
            },

            /**
             * Deselect the current component.
             */
            deselect() {
                this.selected = false;
            }
        },
    };
</script>

<style lang="sass">
    @import "../../../sass/partials/_vars.scss";
    @import "../../../sass/partials/_mixins.scss";

    .song-item {
        border-bottom: 1px solid $color2ndBgr;

        html.no-touchevents &:hover {
            background: rgba(255, 255, 255, .05);
        }

        .time {
            color: $color2ndText;
        }

        .title {
            min-width: 192px;
        }

        .play {
            max-width: 32px;
            opacity: .5;

            i {
                font-size: 150%;
            }
        }

        &.selected {
            background-color: rgba(255, 255, 255, .08);
        }

        &.playing {
            color: $colorHighlight;
        }
    }
</style>
