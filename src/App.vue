<template>
    <div id="app">
        <input type="file" multiple accept="audio/*" id="addFiles" @change="addFiles" style="display: none;">
        <div class="cover" style="transition:.25s;transition-property:opacity;width: 100%;height: 100%;position: fixed;z-index: 11;background: linear-gradient(#e74e8e, #ef9135);display: none;justify-content: center;align-items: center;">
            <!-- generated svg, slightly modified -->
            <svg style="width:150px;height:150px;" viewBox="0 0 1080 1080">
                <path class="st0" d="M653.4,462.8c-57.9-50-115.1-163.8-138.3-268.2c-42.6,37.6-109.4,138.7-97.3,281.5"/>
                <g>
                    <path class="st1" d="M299.9,861.7c-28.6-18.9-51.1-40.6-51.1-40.6c-19.1-18.4-33-36-42.5-49.5l0,0
                        c133.7,18.1,198.3-24.1,221.3-42.4c18.6-14.7,39.2-37,59-58.4c41.9-45.2,66.7-80.1,85.7-105.6C659.3,448.8,705,400.8,745.2,386.6
                        c68.6-24.3,117,32,160.2,53.6c0,0,0.2,0.1,0.2,0.1c-0.2,0.1-0.5,0.4-0.8,0.7c-39.6,35.1-56.8,104.8-63.9,179.3
                        C830.4,731.4,764,824.2,670,884.3c-58.4,37.3-123.4,58.6-184.3,52.4C401.4,928,306.3,865.9,299.9,861.7z"/>
                </g>
                <path class="st1" d="M152.8,211.5"/>
                <path class="st1" d="M450.4,710.8c-332.9,7.1-254.1-463.4-254.1-463.4c39.2,59.5,53.8,76.5,83.5,112.9c56.5,69,95.3,84.4,151.3,119.1c11.6,7.2,28.8,17.1,53.3,34.7c35.1,25.1,61.5,49,78.7,65.6"/>
            </svg>
        </div>
        <Panel/>
        <div class="main-content-wrapper">
            <SideBar/>
            <div class="screen-container">
                <TopBar/>
                <div class="container" ref="container">
                    <router-view/>
                </div>
            </div>
        </div>
        <PlayingBar/>
    </div>
</template>

<script>
import TopBar from './components/TopBar'
import SideBar from './components/sidebar/SideBar.vue'
import PlayingBar from './components/playingbar/PlayingBar'
import Panel from './components/panel/Panel'
import { ipcRenderer } from 'electron'

export default {
    name: 'App',
    components: {
        TopBar,
        SideBar,
        PlayingBar,
        Panel
    },
    methods: {
        addFiles(evt) {
            let files = []
            Array.from(evt.target.files).forEach(f => {
                files.push([f.path,f.name])
            })
            console.log(evt.target.files)
            ipcRenderer.send('addToLibrary', files)
        }
    },
    async mounted() {
        let port = await ipcRenderer.invoke('getPort')
        this.$store.commit('nav/updatePort', port)
        let ver = await ipcRenderer.invoke('getVersion')
        document.title = `Firetail ${ver}`
        this.$refs.container.addEventListener('scroll', e => {
            this.$store.commit('nav/updateCurrentScroll', e.target.scrollTop)
        })
    }
}
</script>

<style lang="scss">
.st0{fill:none;stroke:#221f1f;stroke-width:55;}
.st1{fill:none;stroke:#221f1f;stroke-width:55;stroke-miterlimit:10;}

.main-content-wrapper {
    display: flex;
    height: calc(100vh - 85px);
}

.screen-container {
    
}

.container {
    overflow: hidden;
    overflow-y: auto;
    position: fixed;
    width: calc(100% - #{$sidebarwidth});
    height: calc(100% - 135px);
    padding-top: 50px;
}

body {
    margin: 0;
    background-color: var(--bg);
    color: var(--text);
    font-family: 'Inter', 'Noto Sans JP', 'Noto Sans SC', 'Noto Sans KR', 'Overpass', 'Dosis', Arial, Helvetica, sans-serif !important;
    user-select: none;
    -webkit-user-select: none;
}

a {
    text-decoration: none;
    color: var(--text);
}

::-webkit-scrollbar {
    width: 13px !important;
    background: var(--bg);
}

::-webkit-scrollbar-thumb {
    border: solid 5px var(--bg);
    background: var(--bd);
}

::-webkit-scrollbar-button {
    display: none
}

html.dark {
    --bg: #131313;
    --bg-op: #181818ad;
    --text: #ffffff;
    --fg-bg: #1e1e1e;
    --fg-bg-op: #25252584;
    --bd: #3a3a3a;
    --li-hv: #212121;
    --logo: 0.9;
    --switch: #3d3d3d;
    --hl: #252525;
}

html.light {
    --bg: #f3f3f3;
    --bg-op: #a6a6a633;
    --text: #3d3d3d;
    --fg-bg: #ffffff;
    --fg-bg-op: #ffffff84;
    --bd: #b4b4b4;
    --li-hv: #dddddd;
    --logo: 0.25;
    --switch: #cccccc;
    --hl: #cecece;
}

html.firetail {
    --hl-txt: #eb6e64;
    --hl-op: #eb6e641a;
    --gradient1: #e74e8e;
    --gradient2: #ef9135;
}

@font-face {
    font-family: 'Inter';
    font-weight: normal;
    src: url('./assets/Inter-Regular.ttf') format('truetype');
}

@font-face {
    font-family: 'Inter';
    font-weight: bold;
    src: url('./assets/Inter-Bold.ttf') format('truetype');
}

@font-face {
    font-family: 'Noto Sans JP';
    font-weight: normal;
    src: url('./assets/NotoSansJP-Regular.otf') format('opentype');
}

@font-face {
    font-family: 'Noto Sans JP';
    font-weight: bold;
    src: url('./assets/NotoSansJP-Bold.otf') format('opentype');
}

@font-face {
    font-family: 'Noto Sans SC';
    font-weight: normal;
    src: url('./assets/NotoSansSC-Regular.otf') format('opentype');
}

@font-face {
    font-family: 'Noto Sans SC';
    font-weight: bold;
    src: url('./assets/NotoSansSC-Bold.otf') format('opentype');
}

@font-face {
    font-family: 'Noto Sans KR';
    font-weight: normal;
    src: url('./assets/NotoSansKR-Regular.otf') format('opentype');
}

@font-face {
    font-family: 'Noto Sans KR';
    font-weight: bold;
    src: url('./assets/NotoSansKR-Bold.otf') format('opentype');
}

.screen-container {
    
}

.load-spinner {
    border: 2px solid transparent;
    border-top: 2px solid var(--text);
    border-radius: 50%;
    width: 30px;
    height: 30px;
    animation: spin 1s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>