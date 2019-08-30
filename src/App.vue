<template>
    <div id="app">
        <header id="titlebar">
            <div id="drag-region">

                <div id="window-title">
                    <i class="bell outline icon" style="margin-top: -4px"></i>
                    <span>Vue Alarm</span>
                </div>

                <div id="window-controls">
                    <div class="button" id="min-button" @click="minimize">
                        <span><i class="window minimize icon"></i></span>
                    </div>
                    <div class="button" id="max-button" @click="help">
                        <span><i class="question icon"></i></span>
                    </div>
                    <div class="button" id="close-button" @click="quit">
                        <span><i class="close icon"></i></span>
                    </div>
                </div>
            </div>
        </header>
        <div class="ui container">
            <sui-grid :columns="3">
                <sui-grid-row>
                    <sui-grid-column class="col stretched">
                        <sui-segment>
                            <div class="ui top attached two item tabular menu">
                                <div class="item" :class="{ active : tab }" @click="tab = !tab">Звонки</div>
                                <div class="item" :class="{ active : !tab }" @click="tab = !tab">Гимн</div>
                            </div>
                            <div class="ui bottom attached tab segment" :class="{ active : tab }">
                                <div class="ui action input fluid" :class="{error : !validTime}">
                                    <imask-input
                                        v-model="newAlarm"
                                        @focus="validTime = true"
                                        :mask="'00{:}00'"
                                        radix="."
                                        :unmask="true"
                                        placeholder='Время'/>
                                    <div class="ui grey button tiny" @click="addAlarm(0)">
                                        <i class="add icon"></i>
                                        Добавить
                                    </div>
                                </div>
                                
                                <div class="ui card fluid">
                                    <div class="content" v-for="(alarm, index) in alarms" v-bind:key="index">
                                        <i class="alarm icon"></i>
                                        {{alarm[0]}}
                                        <i class="trash icon right delete-icon" style="float: right" @click="removeAlarm(index)"></i>
                                    </div>
                                    <div class="content" v-if="alarms.length == 0">
                                        <div class="ui placeholder">
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="ui bottom attached tab segment" :class="{ active : !tab }">    
                                <div class="ui action input fluid" :class="{error : !validTime}">
                                    <imask-input
                                        v-model="newAlarm"
                                        @focus="validTime = true"
                                        :mask="'00{:}00'"
                                        radix="."
                                        :unmask="true"
                                        placeholder='Время'/>
                                    <div class="ui primary button tiny" @click="addAlarm(1)">
                                        <i class="add icon"></i>
                                        Добавить
                                    </div>
                                </div>
                                <div class="ui card fluid">
                                    <div class="content" v-for="(alarm, index) in anthems" v-bind:key="index">
                                        <i class="music icon"></i>
                                        {{alarm[0]}}
                                        <i class="trash icon right delete-icon" style="float: right" @click="removeAlarm(index, 1)"></i>
                                    </div>
                                    <div class="content" v-if="anthems.length == 0">
                                        <div class="ui placeholder">
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                            <div class="line"></div>
                                        </div>
                                    </div>
                                    
                                </div>
                            </div>
                        </sui-segment>
                    </sui-grid-column>
                    <sui-grid-column class="col">
                        <sui-segment>
                            <h4 class="ui header">Настройки аудио</h4>

                            <input type="file" class="inputfile" id="embedpollfileinput" @change="setAudio($event, 0)"/>

                            <label for="embedpollfileinput" class="ui grey icon left labeled button fluid">
                                <i class="ui bell icon"></i> 
                                Аудио звонка
                            </label>

                            <div class="ui divider"></div>
                            <input type="file" class="inputfile" id="embedpollfileinput2" @change="setAudio($event, 1)"/>

                            <label for="embedpollfileinput2" class="ui primary icon left labeled button fluid">
                                <i class="ui music icon"></i> 
                                Аудио гимна
                            </label>


                            <div class="ui divider"></div>
                            <input type="file" class="inputfile" id="embedpollfileinput3" @change="setAudio($event, 2)"/>

                            <label for="embedpollfileinput3" class="ui orange icon left labeled button fluid">
                                <i class="ui fire extinguisher icon"></i> 
                                Аудио тревоги
                            </label>

                            <div class="ui divider"></div>
                            <div class="ui form">
                                <div class="two fields">
                                    <div class="field">
                                        <label>Звонок</label>
                                        <div class="ui fluid left icon input"  data-tooltip="Длительность звонка" data-position="top left" data-inverted="">
                                            <input type="number" placeholder="секунды" v-model="alarmDuration">
                                            <i class="hourglass end icon"></i>
                                        </div>
                                    </div>
                                    <div class="field">
                                        <label>Гимн</label>
                                        <div class="ui fluid left icon input" data-tooltip="Длительность гимна" data-position="top left" data-inverted="">
                                            <input type="number" placeholder="секунды" v-model="anthemDuration">
                                            <i class="hourglass start icon"></i>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <!-- <div class="ui fluid left icon input">
                                <input type="number" placeholder="Длительность звонка" v-model="alarmDuration">
                                <i class="hourglass end icon"></i>
                            </div> -->
                        </sui-segment>
                        <sui-segment>
                            <h4 class="ui header">Контроль звука</h4>
                            <div class="inline field">
                                <div class="ui toggle checkbox" @click="toggleMute" style="margin-bottom: 11.5px">
                                    <input type="checkbox" :checked="muted" tabindex="0" class="hidden">
                                    <label>Выключить звук</label>
                                </div>
                            </div>
                            <!-- <div class="ui fluid tiny buttons">
                                <button role="button" class="ui blue button" @click="muteSound(false)">Включить звук</button>
                                <div class="or" data-text=""></div>
                                <button role="button" class="ui brown button" @click="muteSound()">Выключить звук</button>
                            </div> -->
                        </sui-segment>
                    </sui-grid-column>
                    <sui-grid-column class="col">
                        <sui-segment>
                            <Clock></Clock>
                        </sui-segment>
                        <sui-segment>
                            <h4 class="ui header">Ручной запуск</h4>
                            <div class="three ui buttons">
                                <button class="ui button" @click="playAlarm">
                                    Звонок
                                </button>
                                <button class="ui button" @click="playAnthem">
                                    Гимн
                                </button>
                                <button class="ui button" @click="playAlert">
                                    Тревога
                                </button>
                            </div>

                            <div class="ui divider"></div>
                            <button class="ui labeled orange icon button" @click="stopSound">
                                <i class="stop icon"></i>
                                Остановить
                            </button>
                        </sui-segment>
                        <!-- <sui-segment>3</sui-segment> -->
                    </sui-grid-column>
                </sui-grid-row>
            </sui-grid>
        </div>
    </div>
</template>

<script>
// import Home from '@/views/Home.vue'
import Clock from '@/components/Clock.vue'
import {IMaskComponent} from 'vue-imask';
import alarmAudio from '@/assets/alarm.mp3'
import anthemAudio from '@/assets/music.mp3'
import alertAudio from '@/assets/danger.mp3'
const remote = require('electron').remote;
let shell = require('electron').shell;
let sqlite3 = require('sqlite3').verbose();
let db = new sqlite3.Database('sqlite.db');

export default {
    components: {
		Clock,
		'imask-input': IMaskComponent
    },
    data() {
        return {
            audioFile: new Audio(alarmAudio),
            anthemAudioFile: new Audio(anthemAudio),
            alertAudioFile: new Audio(alertAudio),
            newAlarm: '',
            validTime: true,
            alarmDuration: 30,
            anthemDuration: 45,
            alarms: [],
            anthems: [],
            alarmSet: [],
			intervalSet: [],
			muted: false,
            tab: true
        }
    },
    mounted() {
        let self = this

        db.run("CREATE TABLE if not exists alarms (id INTEGER PRIMARY KEY AUTOINCREMENT, type INT NOT NULL, alarm TEXT NOT NULL)")

        db.each("SELECT id, type, alarm FROM alarms", function(err, row) {
            self.newAlarm = row.alarm
            self.addAlarm(row.type, false)
        })

        self.newAlarm = ''

        db.get("SELECT * FROM sounds where type = 0", function(err, row) {
            if (typeof row != "undefined") {
                self.audioFile = new Audio(row.file)
            }
        })
        db.get("SELECT * FROM sounds where type = 1", function(err, row) {
            if (typeof row != "undefined") {
                self.anthemAudioFile = new Audio(row.file)
            }
        })
        db.get("SELECT * FROM sounds where type = 2", function(err, row) {
            if (typeof row != "undefined") {
                self.alertAudioFile = new Audio(row.file)
            }
        })
    },
    methods: {
        setAudio(e, type) {
            let files = e.target.files || e.dataTransfer.files;
            if (!files.length)
				return;
				
			if(type == 0) {
				this.audioFile = new Audio(files[0].path)
			} else if(type == 1) {
				this.anthemAudioFile = new Audio(files[0].path)
			} else {
				this.alertAudioFile = new Audio(files[0].path)
			}

            db.run("CREATE TABLE if not exists sounds (id INTEGER PRIMARY KEY AUTOINCREMENT, type INT NOT NULL, file TEXT UNIQUE)")
			
            db.get("SELECT * FROM sounds where type = " + type, function(err, row) {
                if (typeof row == "undefined") {
                    db.run("INSERT INTO sounds (type, file) VALUES (" + type + ", '" + files[0].path + "')")
                } else {
                    db.run("UPDATE sounds SET file = '" + files[0].path + "' WHERE type=" + type)
                }
            })
        },
        addAlarm(type, newAlarm = true) {

            let self = this
            let audio, duration
            let today = new Date()
            let H = today.getHours()
            let M = today.getMinutes()
            let S = today.getSeconds()
            

            let dayInMilliseconds = 1000 * 60 * 60 * 24
            let alarm = this.newAlarm.replace('_', '')
            
            let h = alarm.split(':')[0]
            let m = alarm.split(':')[1]

            if(h > 23 || h < 0 || m > 59 || m < 0 || !h || !m || alarm == '') {
                this.validTime = false
                return
            }

            this.validTime = true

            if (newAlarm) {
                let exists
                if(type == 0) {
                    exists = this.alarms.find(function(element) {
                        return element[0] == alarm;
                    });

                    audio = self.audioFile
                    duration = self.alarmDuration
                } else {
                    exists = this.anthems.find(function(element) {
                        return element[0] == alarm;
                    });

                    audio = self.anthemAudioFile
                    duration = self.anthemDuration
                }

                if (exists) {
                    this.newAlarm = ''
                    return
                }

                db.run("INSERT INTO alarms (alarm, type) VALUES ('" + alarm + "', " + type + ")");
            }
            


            let difference = ((h - H) * 60 + (m - M)) * 60 - S

            if (difference < 0) {
                difference += dayInMilliseconds
            }

            let t = setTimeout(() => {
                audio.play()
                setTimeout(() => {
                    audio.pause()
                    audio.currentTime = 0
                }, duration * 1000);
                let i = setInterval(() => {
                    audio.play()
                    setTimeout(() => {
                        audio.pause()
                        audio.currentTime = 0
                    }, duration * 1000);
                }, dayInMilliseconds);
                this.intervalSet[t] = i
            }, difference * 1000);

            if(type == 0) {
                this.alarms.push([alarm, t])
            } else {
                this.anthems.push([alarm, t])
            }

            this.newAlarm = ''
            
        },
        removeAlarm(index, type = 0) {
            

            if(type == 0) {
                clearInterval(this.intervalSet[this.alarms[index][1]]);
                clearTimeout(this.alarms[index][1]);

                db.run("DELETE FROM alarms WHERE alarm = '" + this.alarms[index][0] + "' AND type=0");
                this.$delete(this.alarms, index)
            } else {
                clearInterval(this.intervalSet[this.anthems[index][1]]);
                clearTimeout(this.anthems[index][1]);

                db.run("DELETE FROM alarms WHERE alarm = '" + this.anthems[index][0] + "' AND type=1");
                this.$delete(this.anthems, index)
            }
		},
		playAlarm() {
			this.anthemAudioFile.pause()
			this.alertAudioFile.pause()

			this.audioFile.currentTime = 0
			this.audioFile.play()
		},
		playAnthem() {
			this.audioFile.pause()
			this.alertAudioFile.pause()

			this.anthemAudioFile.currentTime = 0
			this.anthemAudioFile.play()
		},
		playAlert() {
			this.audioFile.pause()
			this.anthemAudioFile.pause()

			this.alertAudioFile.currentTime = 0
			this.alertAudioFile.play()
        },
        stopSound() {
            this.audioFile.pause()
			this.anthemAudioFile.pause()
			this.alertAudioFile.pause()
        },
		toggleMute() {
			this.muted = !this.muted
			this.audioFile.muted = this.muted
			this.anthemAudioFile.muted = this.muted
			this.alertAudioFile.muted = this.muted
        },
        quit() {
            let window = remote.getCurrentWindow();
            window.close()
        },
        minimize() {
            let window = remote.getCurrentWindow();
            window.minimize()
        },
        help(e) {
            e.preventDefault();
            shell.openExternal('https://amangeldiyev.github.io/alarm/');
        }
    }
}
</script>

<style>

body {
	margin: 0;
	padding: 0;
}

::-webkit-scrollbar {
  width: 0px;
  height: 0px;
}
::-webkit-scrollbar-button {
  width: 0px;
  height: 0px;
}
::-webkit-scrollbar-thumb {
  background: #e1e1e1;
  border: 0px none #ffffff;
  border-radius: 0px;
}
::-webkit-scrollbar-thumb:hover {
  background: #ffffff;
}
::-webkit-scrollbar-thumb:active {
  background: #575ed5;
}
::-webkit-scrollbar-track {
  background: #666666;
  border: 0px none #ffffff;
  border-radius: 0px;
}
::-webkit-scrollbar-track:hover {
  background: #666666;
}
::-webkit-scrollbar-track:active {
  background: #333333;
}
::-webkit-scrollbar-corner {
  background: transparent;
}

input[type=number]::-webkit-inner-spin-button, 
input[type=number]::-webkit-outer-spin-button { 
  -webkit-appearance: none; 
  margin: 0; 
}

.toggle.checkbox label::before {
    background: rgba(0,0,0,.3) !important;
}

.ui.button, .ui.header, .ui.menu {
    font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif !important;
}


#app {
    font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
	min-height: 100%;
	height: auto;
    padding-top: 45px;
	background-image: linear-gradient(to top, rgb(207, 217, 223) 0%, rgb(226, 235, 240) 100%);
}

#titlebar {
  display: block;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1;
  height: 32px;
  width: 100%;
  background-color: #42b983;
  margin-bottom: 20px;
}
#titlebar {
  padding: 4px;
}
#titlebar #drag-region {
  width: 100%;
  height: 100%;
  -webkit-app-region: drag;
}
#titlebar {
  color: #2c3e50;
}
#titlebar #drag-region {
  display: grid;
  grid-template-columns: auto 138px;
}
#window-title {
  grid-column: 1;
  display: flex;
  align-items: center;
  margin-left: 8px;
  overflow-x: hidden;
  font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
  font-size: 14px;
  font-weight: bold;
}
#window-title span {
  overflow: hidden;
  text-overflow: ellipsis;
  line-height: 1.5;
}
#window-controls {
  display: grid;
  grid-template-columns: repeat(3, 46px);
  position: absolute;
  top: 0;
  right: 0;
  height: 32px;
  font-size: 10px;
}
#window-controls {
  -webkit-app-region: no-drag;
}
#window-controls .button {
  grid-row: 1 / span 1;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
}
#window-controls .button {
  user-select: none;
  cursor: default;
  color: #2c3e50;
}
#window-controls .button:hover {
  background: rgba(255,255,255,0.2);
  color: #FFF;
}
#window-controls #min-button {
  grid-column: 1;
}
#window-controls #max-button, #window-controls #restore-button {
  grid-column: 2;
}
#window-controls #restore-button {
  display: none;
}
#window-controls #close-button {
  grid-column: 3;
}
#window-controls #close-button:hover {
  background: #E81123;
}


.col {
    padding: 4px 0;
    -moz-border-radius: 4px;
    -webkit-border-radius: 4px;
    border-radius: 4px;
    /* -webkit-box-shadow: 0px 10px 10px rgba(47, 40, 39, 0.9), 0px 35px 20px rgba(47, 40, 39, 0.4), 0px 50px 15px rgba(69, 57, 62, 0.6); */
}

#nav {
    padding: 30px;
}

#nav a {
    font-weight: bold;
    color: #2c3e50;
}

#nav a.router-link-exact-active {
    color: #42b983;
}

.inputfile {
	width: 0.1px;
	height: 0.1px;
	opacity: 0;
	overflow: hidden;
	position: absolute;
	z-index: -1;
}

.delete-icon:hover {
	color: red;
	cursor: pointer;
}
</style>
