<template>
    <li class="results-link" @mouseover="listHover" @mouseleave="listHoverLeave" :class="isActive">
        <i class="material-icons-outlined play-pause" :style="listIconVisible" @click="decidePlaySong" @mouseover="listIconHover" @mouseleave="listIconHoverLeave">{{ listIcon }}</i>
        <i class="material-icons-outlined favourite-icon" @click="handleFavourite">{{ favouriteIcon }}</i>
        <div class="artist-title-album" @dblclick="playSong">
            <div class="list-title">
                <p>{{song.title}}</p>
                <span>{{song.album}}</span>
            </div>
            <p class="list-duration"><span>{{song.duration}}</span></p>
        </div>
    </li>
</template>

<script>
import { ipcRenderer } from 'electron'
export default {
    props: ['song'],
    computed: {
        isActive() {
            let view = this.$route.query.view
            if (this.song.id == this.$store.state.audio.currentSong && view == this.$store.state.nav.playingView) {
                return "active"
            } else {
                return ""
            }
        },
        listIcon() {
            let view = this.$route.query.view
            if (this.song.id == this.$store.state.audio.currentSong && this.$store.state.audio.paused && view == this.$store.state.nav.playingView) {
                return 'play_arrow'
            } else if (this.song.id == this.$store.state.audio.currentSong && this.isIconHover && view == this.$store.state.nav.playingView) {
                return 'pause'
            } else if (this.song.id == this.$store.state.audio.currentSong && view == this.$store.state.nav.playingView) {
                return 'volume_up'
            } else {
                return 'play_arrow'
            }
        },
        listIconVisible() {
            let view = this.$route.query.view
            if (this.song.id == this.$store.state.audio.currentSong && view == this.$store.state.nav.playingView || this.isHover) {
                return 'opacity: 1'
            } else {
                return 'opacity: 0'
            }
        },
        favouriteIcon() {
            if (this.$store.state.nav.favouriteSongs.indexOf(this.song.id) != -1) {
                return 'favorite'
            } else {
                return 'favorite_border'
            }
        }
    },
    data() {
        return {
            isHover: false,
            isIconHover: false
        }
    },
    methods: {
        playSong() {
            let currentList = []
            this.$store.state.audio.currentList.forEach(f => { currentList.push(f) })
            this.$store.commit('audio/genNewQueue', currentList)
            this.$store.dispatch('audio/playSong', this.song)
        },
        decidePlaySong() {
            let view = this.$route.query.view
            if (this.song.id == this.$store.state.audio.currentSong && view == this.$store.state.nav.playingView) {
                this.$store.dispatch('audio/playPause')
            } else {
                this.playSong()
            }
        },
        listHover() {
            this.isHover = true
        },
        listHoverLeave() {
            this.isHover = false
            this.isIconHover = false
        },
        listIconHover() {
            if (this.song.id == this.$store.state.audio.currentSong) {
                this.isIconHover = true
            }
        },
        listIconHoverLeave() {
            this.isIconHover = false
        },
        handleFavourite() {
            if (this.$store.state.nav.favouriteSongs.indexOf(this.song.id) == -1) {
                this.addToFavourite()
            } else {
                this.removeFromFavourites()
            }
        },
        addToFavourite() {
            ipcRenderer.send('addFavourite', this.song.id)
            console.log('yeah')
        },
        removeFromFavourites() {
            ipcRenderer.send('removeFavourite', this.song.id)
        }
    }
}
</script>

<style lang="scss" scoped>
.results-link {
    border-bottom: 1px solid var(--bd);
    background: var(--bg);
    overflow: hidden;
    position: relative;
    height: 55px;
    display: flex;
    align-items: center;
    transition: .25s;
    transition-property: margin-left;
}

li:hover {
    background: var(--li-hv);
}

li.nohover:hover {
    background: none;
}

.results-link i, .list-section i {
    padding: 0 5px 0 15px;
}

.play-pause {
    font-size: 24px !important;
    padding: 0;
    cursor: pointer;
    opacity: 0;
}

.play-pause:hover, .favourite-icon:hover {
    opacity: .5 !important;
}

.results-link.active i {
    opacity: 1;
}

.results-link.active {
    color: var(--hl-txt);
}

.artist-title-album {
    display: flex;
    width: calc(100% - 72px);
    font-size: 14px;
    align-items: center;
}

.list-title {
    margin: 0;
    margin-left: 14px;
    width: 100%;
    padding-right: 40px !important;
    overflow: hidden;
    text-overflow: ellipsis;
    padding: 9px 0;
    pointer-events: none;
    white-space: nowrap;
    display: flex;
    flex-direction: column;
    justify-content: center;

    p {
        margin: 0 0 5px;
        font-size: 15px;
    }

    span {
        font-size: 12px;
        opacity: 0.75;
    }
}

.list-duration {
    width: calc(30% - 77px);
    overflow: hidden;
    text-overflow: ellipsis;
    padding: 9px 0;
    padding-right: 40px;
    pointer-events: none;
    white-space: nowrap;
}

.list-duration {
    width: 40px;
    text-align: right;
    padding-right: 20px;
}

.results-link .favourite-icon {
    font-size: 20px;
    cursor: pointer;
    padding-right: 10px;
}
</style>