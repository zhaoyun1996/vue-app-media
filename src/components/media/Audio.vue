<template lang="">
    <div class="overlay"></div>
    <div id="audio">
        <div id="search-song">
            <input
                ref="inputSearch"
                v-model="searchValue"
                @keyup.enter="search"
                id="search-value"
                placeholder="Nhập từ khóa để tìm kiếm bài hát"
                autocomplete="off"
            />
            <!-- <button @click="getChartHome">Get Chart Home</button> -->
        </div>
        <div class="lyric-left" v-if="sentences && sentences.new">
            <div
                v-for="(item, index) in sentences.new"
                class="lyric-left-item"
                :key="idSongActived + index"
            >
                {{ item.dataSource }}
            </div>
        </div>
        <div class="player">
            <div class="dashboard">
                <div class="header" v-if="idSongActived">
                    <h2 id="title">{{ title }}</h2>
                </div>
                <div class="cd">
                    <div
                        class="cd-thumb"
                        :style="{ backgroundImage: 'url(' + thumbnailM + ')' }"
                    ></div>
                    <div class="playing-icon" v-if="isPlay"></div>
                </div>
                <div class="control">
                    <div
                        class="btn btn-repeat"
                        :class="{ active: isRepeat }"
                        @click="repeat"
                    >
                        <i class="fas fa-redo"></i>
                    </div>
                    <div class="btn btn-prev" @click="prevSong">
                        <i class="fas fa-step-backward"></i>
                    </div>
                    <div class="btn btn-toggle-play" v-if="isPlay" @click="pause">
                        <i
                            class="fas fa-pause icon-pause"
                        ></i>
                    </div>
                    <div class="btn btn-toggle-play" v-else @click="play">
                        <i
                            class="fas fa-play icon-play"
                        ></i>
                    </div>
                    <div class="btn btn-next" @click="nextSong">
                        <i class="fas fa-step-forward"></i>
                    </div>
                    <div
                        class="btn btn-random"
                        :class="{ active: isRandom }"
                        @click="random"
                    >
                        <i class="fas fa-random"></i>
                    </div>
                </div>

                <div class="time-progress">
                    <div>{{ currentTime }}</div>
                    <input
                        id="progress"
                        class="progress"
                        type="range"
                        value="0"
                        step="1"
                        min="0"
                        max="100"
                    />
                    <div>{{ totalTime }}</div>
                </div>

                <div class="lyric" v-html="lyric"></div>

                <audio id="audio-control" controls v-show="false">
                    <source type="audio/mpeg" />
                </audio>
            </div>

            <div class="playlist" :style="lyric ? 'margin-top: 502px' : 'margin-top: 486px'">
                <div
                    v-for="(item, index) in songs"
                    :key="index"
                    class="song"
                    :class="{ active: item.encodeId == idSongActived }"
                    :id="item.encodeId"
                    @click="selectSong"
                >
                    <div
                        class="thumb"
                        :style="{
                            backgroundImage: 'url(' + item.thumbnail + ')',
                        }"
                    ></div>
                    <div class="body">
                        <h3 class="title">{{ item.title }}</h3>
                        <p class="author">{{ item.artistsNames }}</p>
                    </div>
                    <div class="option">
                        <i class="fas fa-ellipsis-h"></i>
                    </div>
                </div>
            </div>
        </div>
        <div class="lyric-right" v-if="true">
            
        </div>
    </div>
</template>
<script>
import axios from "axios";
export default {
    name: "Audio",

    data() {
        window.audio = this;
        return {
            searchValue: "",
            songs: [],
            idSongActived: "",
            title: "",
            thumbnailM: "",
            isPlay: false,
            audioControl: null,
            cdThumbAnimate: null,
            isRepeat: false,
            isRandom: false,
            currentTime: "00:00",
            totalTime: "00:00",
            sentences: {
                old: [],
                new: []
            },
            lyric: "",
            timeStart: 0,
            word: "",
            rowLyric: -1
        };
    },

    methods: {
        /**
         * Thực hiện lấy bài hát
         * Get Song
         */
        async getSong(id) {
            const me = this;

            let res = await axios.get(`/getSong?id=${id}`);

            me.audioControl.src = res.data.data[128];
        },

        /**
         * TODO: Thực hiện lấy chi tiết các danh sách
         * Get Detail Playlist
         */
        async getDetailPlaylist() {
            let id = "ZWZB969E";

            let res = await axios.get(`/getDetailPlaylist?id=${id}`);

            console.log(res);
        },

        /**
         * TODO: Thực hiện lấy bài hát ở trang chủ
         * Get Home
         */
        async getHome() {
            let id = "1";

            let res = await axios.get(`/getHome?id=${id}`);

            console.log(res);
        },

        /**
         * TODO: Thực hiện lấy top 100
         * Get Top 100
         */
        async getTop100() {
            const me = this;

            let res = await axios.get("/getTop100");

            if (res && res.data && res.data.msg == "Success" && res.data.data) {
                me.songs = res.data.data.songs;
            }

            console.log(res);
        },

        /**
         * Thực hiện lấy bảng xếp hạng
         * Get Chart Home
         */
        async getChartHome() {
            const me = this;

            let res = await axios.get("/getChartHome");

            if (res && res.data && res.data.msg == "Success" && res.data.data) {
                me.songs = res.data.data.RTChart.items;

                if (me.songs.length > 0) {
                    me.idSongActived = me.songs[0].encodeId;
                    me.selectSong();
                }
            }

            console.log(res);
        },

        /**
         * Thực hiện lấy bảng xếp hạng mới nhất
         * Get New Release Chart
         */
        async getNewReleaseChart() {
            // const me = this;

            let res = await axios.get("/getNewReleaseChart");
            // audio = document.getElementById("audio");

            console.log(res);
        },

        /**
         * Thực hiện lấy thông tin bài hát
         * Get Song Info
         */
        async getInfoSong() {
            // const me = this;

            let res = await axios.get("/getInfoSong");
            // audio = document.getElementById("audio");

            console.log(res);
        },

        /**
         * Thực hiện lấy thông tin ca sĩ
         * Get Artist
         */
        async getArtist() {
            // const me = this;

            let res = await axios.get("/getArtist");
            // audio = document.getElementById("audio");

            console.log(res);
        },

        /**
         * Thực hiện lấy lời bài hát
         * Get Lyric Song
         */
        async getLyric(id) {
            const me = this;

            let res = await axios.get(`/getLyric?id=${id}`);

            if (res && res.data && res.data.msg == "Success" && res.data.data) {
                me.sentences.old = res.data.data.sentences;
                me.sentences.new = [];
                me.lyric = '';
                me.rowLyric = -1;

                if(Array.isArray(me.sentences.old)) {
                    me.sentences.old.forEach(item => {
                        me.sentences.new.push({
                            startTime: item.words[0].startTime,
                            endTime: item.words[item.words.length - 1].endTime,
                            data: item.words.map((x, index) => `<span id="word-${index}">${x.data}</span>`).join(" "),
                            dataSource: item.words.map(x => x.data).join(" ")
                        });
                    })
                }

                // Khi chọn bài mới thì scroll lời bên trái lên đầu
                me.$nextTick(() => {
                    let lyricLeftItem = document.getElementsByClassName("lyric-left-item");

                    if(lyricLeftItem && lyricLeftItem.length > 0) {
                        lyricLeftItem[0].scrollIntoView();
                    }
                })
            }
        },

        /**
         * Thực hiện tìm kiếm bài hát
         * Search Song
         */
        async search() {
            const me = this;

            let res = await axios.get(`/search?searchValue=${me.searchValue}`);

            if (res && res.data && res.data.msg == "Success" && res.data.data) {
                me.songs = res.data.data.songs;

                if (me.songs.length > 0) {
                    me.idSongActived = me.songs[0].encodeId;
                    me.selectSong();
                }
            }
        },

        /**
         * TODO: Thực hiện lấy danh sách MV
         * Get List MV
         */
        async getListMV() {
            // const me = this;

            let res = await axios.get("/getListMV");
            // audio = document.getElementById("audio");

            console.log(res);
        },

        /**
         * TODO: Thực hiện lấy danh mục MV
         * Get Category MV
         */
        async getCategoryMV() {
            // const me = this;

            let res = await axios.get("/getCategoryMV");
            // audio = document.getElementById("audio");

            console.log(res);
        },

        /**
         * TODO: Thực hiện lấy video MV
         * Get Video MV
         */
        async getVideo() {
            // const me = this;

            let res = await axios.get("/getVideo");
            // audio = document.getElementById("audio");

            console.log(res);
        },

        /**
         * Lắng nghe sự kiện
         */
        listenEvent() {
            const me = this,
                progress = document.getElementById("progress");

            let songInterval;

            // Khi nhạc đang phát
            me.audioControl.onplay = () => {
                songInterval = setInterval(() => {
                    me.currentTime = me.convertTime(parseInt(me.audioControl.currentTime, 10));

                    me.runLyric();
                }, 100);
            };

            // Khi nhạc dừng lại
            me.audioControl.onpause = () => {
                clearInterval(songInterval);
            };

            // Khi hết bài
            me.audioControl.onended = () => {
                // Random bài hát
                if (me.isRandom) {
                    me.randomSong();
                }
                // Lặp lại bài hát
                else if (me.isRepeat) {
                    me.selectSong();
                }
                // Chuyển sang bài kế tiếp
                else {
                    me.nextSong();
                }

                // TODO: Làm phần tự động scroll bài đang active
            };

            // Khi tiến độ bài hát thay đổi
            // When the song progress changes
            me.audioControl.ontimeupdate = () => {
                if (me.audioControl.duration) {
                    const progressPercent = Math.floor(
                        (me.audioControl.currentTime /
                            me.audioControl.duration) *
                            100
                    );
                    progress.value = progressPercent;
                }
            };

            // Xử lý khi tua song
            // Handling when seek
            progress.onchange = function (e) {
                const seekTime =
                    (me.audioControl.duration / 100) * e.target.value;
                me.audioControl.currentTime = seekTime;
            };
        },

        /**
         * Chọn bài hát
         */
        async selectSong(e) {
            const me = this;

            // Nếu là người dùng tự chọn bài
            if (e) {
                // Nếu chọn khác bài đang phát thì chuyển bài
                if(me.idSongActived != e.currentTarget.getAttribute("id")) {
                    me.idSongActived = e.currentTarget.getAttribute("id");
                }
                // Ngược lại thì không làm gì cả
                else {
                    return;
                }
            }

            me.setSong(me.idSongActived);

            await me.getSong(me.idSongActived);

            me.getLyric(me.idSongActived);

            me.scrollToActiveSong();

            me.play();
        },

        /**
         * Bài hát liền sau
         */
        nextSong() {
            const me = this;

            var indexSong = me.songs.findIndex(
                (item) => item.encodeId == me.idSongActived
            );

            if (indexSong > -1) {
                var indexSongNew =
                    indexSong + 1 == me.songs.length ? 0 : indexSong + 1;

                me.idSongActived = me.songs[indexSongNew].encodeId;
                me.title = me.songs[indexSongNew].title;
                me.thumbnailM = me.songs[indexSongNew].thumbnailM;
                me.totalTime = me.convertTime(me.songs[indexSongNew].duration);
            }

            me.selectSong();
        },

        /**
         * Bài hát liền trước
         */
        prevSong() {
            const me = this;

            var indexSong = me.songs.findIndex(
                (item) => item.encodeId == me.idSongActived
            );

            if (indexSong > -1) {
                var indexSongNew =
                    indexSong - 1 < 0 ? me.songs.length - 1 : indexSong - 1;

                me.idSongActived = me.songs[indexSongNew].encodeId;
                me.title = me.songs[indexSongNew].title;
                me.thumbnailM = me.songs[indexSongNew].thumbnailM;
                me.totalTime = me.convertTime(me.songs[indexSongNew].duration);
            }

            me.selectSong();
        },

        /**
         * Bài hát ngẫu nhiên
         */
        randomSong() {
            const me = this;

            for (let i = 0; ; i++) {
                let indexSong = Math.floor(Math.random() * me.songs.length);

                if (me.idSongActived != me.songs[indexSong].encodeId) {
                    me.idSongActived = me.songs[indexSong].encodeId;
                    me.title = me.songs[indexSong].title;
                    me.thumbnailM = me.songs[indexSong].thumbnailM;
                    me.totalTime = me.convertTime(me.songs[indexSong].duration);
                    break;
                }
            }

            me.selectSong();
        },

        /**
         * Gán bài hát để chuẩn bị phát
         */
        setSong(id) {
            const me = this;

            var song = me.songs.find((item) => item.encodeId == id);

            if (song) {
                me.title = song.title;
                me.thumbnailM = song.thumbnailM;
                me.totalTime = me.convertTime(song.duration);

                // Thay đổi ảnh nền
                document.getElementById("right-content").style.backgroundImage = `url(${song.thumbnailM})`;
                document.getElementById("right-content").style.backgroundSize = '100% 100%';
            }
        },

        /**
         * Phát nhạc
         */
        async play() {
            const me = this;

            me.isPlay = true;

            me.cdThumbAnimate.play();

            me.audioControl.play();
        },

        /**
         * Dừng nhạc
         */
        async pause() {
            const me = this;

            me.isPlay = false;

            me.cdThumbAnimate.pause();

            me.audioControl.pause();
        },

        /**
         * Khi chọn lặp lại bài hát
         */
        repeat() {
            const me = this;

            me.isRepeat = !me.isRepeat;
            me.isRandom = false;
        },

        /**
         * Khi chọn ngẫu nhiên bài hát
         */
        random() {
            const me = this;

            me.isRandom = !me.isRandom;
            me.isRepeat = false;
        },

        /**
         * Chuyển đổi thời gian từ giây sang phút và giây
         */
        convertTime(time) {
            let sec = time % 60,
                min = (time - sec) / 60;

            if (min < 10 && sec < 10) {
                return `0${min}:0${sec}`;
            } else if (min >= 10 && sec < 10) {
                return `${min}:0${sec}`;
            } else if (min < 10 && sec >= 10) {
                return `0${min}:${sec}`;
            } else {
                return `${min}:${sec}`;
            }
        },

        /**
         * Di chuyển đến bài đang phát
         */
        scrollToActiveSong() {
            setTimeout(() => {
                document.getElementsByClassName("playlist")[0].getElementsByClassName("song active")[0].scrollIntoView({
                    behavior: "smooth",
                    block: "nearest"
                });
            }, 300);
        },

        /**
         * Chạy lời bài hát
         */
        runLyric() {
            const me = this;

            if(Array.isArray(me.sentences.old)) {
                // Xử lý lời ở cd
                for(let i = me.rowLyric + 1; i < me.sentences.new.length; i++) {
                    let sentence = me.sentences.new[i];
                    let timeLyric = me.audioControl.currentTime * 1000;
                    if(timeLyric >= sentence.startTime && timeLyric <= sentence.endTime) {
                        me.lyric = sentence.data;
                        if(i != me.rowLyric) {
                            me.runLyricLeft(i);
                        }
                        me.rowLyric = i;
                        break;
                    }
                }

                // Xử lý từng chữ trong lời bài hát
                let sentence = me.sentences.old[me.rowLyric];
                if(sentence && Array.isArray(sentence.words)) {
                    for(let y = 0; y < sentence.words.length; y++) {
                        let timeLyric = me.audioControl.currentTime * 1000;
                        let word = sentence.words[y];
    
                        // Sửa lại style
                        let text = document.getElementById(`word-${y}`);
                        if(text && timeLyric >= word.startTime) {
                            text.style.color = "red";
                        }
    
                        if(timeLyric >= word.startTime && timeLyric <= word.endTime) {
                            me.word = word.data;
                            return;
                        }
                    }
                }
            }
        },

        /**
         * Chạy lời bài hát bên trái
         */
        runLyricLeft(rowIndex) {
            const me = this;

            let lyricLeftItem = document.getElementsByClassName('lyric-left-item');

            if(lyricLeftItem && lyricLeftItem.length > 0) {
                let lyricLeftItemActive = document.getElementsByClassName('lyric-left-item row-active');

                if(lyricLeftItemActive && lyricLeftItemActive.length > 0) {
                    lyricLeftItemActive[0].classList.add("is-over");
                    lyricLeftItemActive[0].classList.remove("row-active");
                }

                lyricLeftItem[rowIndex ?? me.rowLyric].classList.add("row-active");
                lyricLeftItem[rowIndex ?? me.rowLyric].scrollIntoView({
                    behavior: "smooth",
                    block: "center"
                });
            }
        }
    },

    mounted() {
        const me = this;

        // Lấy control audio để xử lý
        me.audioControl = document.getElementById("audio-control");

        // Xử lý CD quay / dừng
        // Handle CD spins / stops
        me.cdThumbAnimate = document
            .getElementsByClassName("cd-thumb")[0]
            .animate([{ transform: "rotate(360deg)" }], {
                duration: 10000, // 10 seconds
                iterations: Infinity,
            });
        me.cdThumbAnimate.pause();

        // Lắng nghe các event của control audio
        me.listenEvent();

        // Thực hiện focus vào ô tìm kiếm
        if(me.$refs && me.$refs.inputSearch) {
            me.$refs.inputSearch.focus();
        }
    },
};
</script>
<style lang="css" scoped>
#audio {
    margin: 0 auto;
    font-family: "Poppins", sans-serif;
}

#search-song {
    width: 85%;
    text-align: center;
    position: fixed;
    margin: 10px auto;
    top: 5%;
}

#search-value {
    height: calc(2.25rem + 2px);
    padding: 0.375rem 0.75rem;
    font-size: 1rem;
    line-height: 1.5;
    color: #495057;
    background-color: #fff;
    background-clip: padding-box;
    border: 1px solid #ced4da;
    border-radius: 0.25rem;
    transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
    width: 300px;
}

.player {
    position: relative;
    max-width: 480px;
    margin: 0 auto;
}

.overlay {
    height: 85%;
    width: 85%;
    background-color: #291547;
    opacity: 0.8;
    backdrop-filter: blur(10px);
    position: absolute;
}

/* DASHBOARD */
.dashboard {
    padding: 16px 16px 14px;
    background-color: #fff;
    top: 120px;
    width: 100%;
    max-width: 448px;
    border-bottom: 1px solid #ebebeb;
    position: fixed;
    border-radius: 5px;
}

.dashboard .header {
    text-align: center;
}

.dashboard .header h2 {
    margin-bottom: 10px;
    font-size: 20px;
}

.dashboard .cd {
    display: flex;
    margin: auto;
    width: 200px;
    position: relative;
}

.dashboard .cd .cd-thumb {
    width: 100%;
    padding-top: 100%;
    border-radius: 50%;
    background-color: #333;
    background-size: cover;
    margin: auto;
}

.playing-icon {
    position: absolute;
    top: calc(50% - 12px);
    left: calc(50% - 12px);
    height: 24px;
    width: 24px;
    background-size: cover;
    background-image: url('https://zmp3-static.zmdcdn.me/skins/zmp3-v6.1/images/icons/icon-playing.gif');
}

/* CONTROL */
.control {
    display: flex;
    align-items: center;
    justify-content: space-around;
    padding: 18px 0 8px 0;
}

.control .btn {
    color: #666;
    padding: 18px;
    font-size: 18px;
}

.control .btn.active {
    color: #ec1f55;
}

.control .btn-toggle-play {
    width: 56px;
    height: 56px;
    border-radius: 50%;
    font-size: 24px;
    color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: #ec1f55;
}

.time-progress {
    display: flex;
    font-size: 13px;
    align-items: center;
}

.progress {
    margin: 0 5px;
    width: 100%;
    -webkit-appearance: none;
    height: 6px;
    background: #d3d3d3;
    outline: none;
    opacity: 0.7;
    -webkit-transition: 0.2s;
    transition: opacity 0.2s;
}

.progress::-webkit-slider-thumb {
    -webkit-appearance: none;
    appearance: none;
    width: 12px;
    height: 6px;
    background-color: #ec1f55;
    cursor: pointer;
}

/* PLAYLIST */
.playlist {
    padding: 12px;
}

.song {
    display: flex;
    align-items: center;
    margin-bottom: 12px;
    background-color: #fff;
    padding: 8px 16px;
    border-radius: 5px;
    box-shadow: 0 2px 3px rgba(0, 0, 0, 0.1);
}

.song.active {
    background-color: #ec1f55;
}

.song:active {
    opacity: 0.8;
}

.song.active .option,
.song.active .author,
.song.active .title {
    color: #fff;
}

.song .thumb {
    width: 44px;
    height: 44px;
    border-radius: 50%;
    background-size: cover;
    margin: 0 8px;
}

.song .body {
    flex: 1;
    padding: 0 16px;
}

.song .title {
    font-size: 18px;
    color: #333;
}

.song .author {
    font-size: 12px;
    color: #999;
}

.song .option {
    padding: 16px 8px;
    color: #999;
    font-size: 18px;
}

.lyric {
    margin-top: 10px;
    text-align: center;
}

/* LYRIC LEFT */
.lyric-left {
    position: fixed;
    top: 120px;
    height: 420px;
    overflow: auto;
    -webkit-mask-image: linear-gradient(180deg,hsla(0,0%,100%,0),hsla(0,0%,100%,.8) 10%,#fff 25%,#fff 75%,hsla(0,0%,100%,.8) 90%,hsla(0,0%,100%,0));
    width: calc((85% - 480px)/2);
}

.lyric-left-item {
    padding: 10px 0;
    letter-spacing: -1px;
    font-weight: 700;
    font-size: inherit;
    line-height: 1.2;
    color: #fff;
    font-size: 20px;
}

.row-active {
    color: #ffed00;
}

.is-over {
    color: hsla(0,0%,100%,.5);
}
</style>
