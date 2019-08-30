<template>
    <div class="home">
        <input type="text" v-model="newAlarm">
        <button @click="addAlarm">Add</button>
		
		<input type="number" v-model="alarmDuration">

		<input type="file" @change="setAudio($event)">

        <button @click="muteSound()">Mute</button>
        <button @click="muteSound(false)">Unmute</button>

        <ul>
            <li v-for="(alarm, index) in alarms" v-bind:key="index">
                Alarm: {{alarm[0]}} <span @click="removeAlarm(index)">(x)</span>
            </li>
        </ul>
		<ul>
			<li v-for="(item, index) in list" :key="index">{{index}}:{{item}}</li>
		</ul>
    </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue'
// import alarmAudio from '@/assets/alarm.mp3'
let sqlite3 = require('sqlite3').verbose();
let db = new sqlite3.Database('sqlite.db');

export default {
    name: 'home',
    components: {
        // HelloWorld
    },
    data() {
        return {
			audioFile: new Audio(),
			newAlarm: '',
			alarmDuration: 30,
			alarms: [],
			alarmSet: [],
			intervalSet: [],
			list: []
        }
    },
    mounted() {
		let self = this

		db.run("CREATE TABLE if not exists alarms (id INTEGER PRIMARY KEY AUTOINCREMENT, alarm TEXT UNIQUE)")

		db.each("SELECT id, alarm FROM alarms", function(err, row) {
			self.newAlarm = row.alarm
			self.addAlarm(false)
		})

		self.newAlarm = ''
		
		db.get("SELECT * FROM sounds where type = 0", function(err, row){
			if(typeof row != "undefined") {
				self.audioFile = new Audio(row.file)
			}
		})
    },
    methods: {
		setAudio(e) {
			let files = e.target.files || e.dataTransfer.files;
			if (!files.length)
				return;
			this.audioFile = new Audio(files[0].path)

			db.run("CREATE TABLE if not exists sounds (id INTEGER PRIMARY KEY AUTOINCREMENT, type INT NOT NULL, file TEXT UNIQUE)")
			
			db.get("SELECT * FROM sounds where type = 0", function(err, row){
				if(typeof row == "undefined") {
					db.run("INSERT INTO sounds (type, file) VALUES (0, '"+files[0].path+"')")
				} else {
					db.run("UPDATE sounds SET file = '"+files[0].path+"' WHERE type=0")
				}
			})
			
			

			// let self = this
			// db.each("SELECT id, alarm FROM alarms", function(err, row) {
			// 	console.log(row.id + ": " + row.alarm);
			// 	self.list.push(row.alarm)
			// });

			// db.serialize(function() {
			// 	db.run("CREATE TABLE if not exists alarms (id INT PRIMARY KEY NOT NULL, alarm TEXT NOT NULL)");
			// 	db.run("INSERT INTO alarms VALUES (0, 'First')");

			// 	// var stmt = db.prepare("INSERT INTO alarms VALUES (?)");
			// 	// for (var i = 0; i < 3; i++) {
			// 	// 	stmt.run("Ipsum " + i);
			// 	// }
			// 	// stmt.finalize();

			// 	db.each("SELECT id, alarm FROM alarms", function(err, row) {
			// 		console.log(row.id + ": " + row.alarm);
			// 		self.list.push(row.alarm)
			// 	});
			// });

			// db.close();
		},
        addAlarm(newAlarm = true) {
			if(this.newAlarm != '') {

				let self = this

				if(newAlarm) {

					let exists = this.alarms.find(function(element) {
						return element[0] == self.newAlarm;
					});

					if(exists) {
						this.newAlarm = ''
						return
					}

					db.run("INSERT INTO alarms (alarm) VALUES ('"+this.newAlarm+"')");
				}

				let today = new Date()
				let H = today.getHours()
				let M = today.getMinutes()
				let S = today.getSeconds()
				let dayInMilliseconds = 1000 * 60 * 60 * 24
				let alarm

				
				alarm = this.newAlarm.split('-')

				let h = alarm[0]
				let m = alarm[1]
				let difference = ((h - H) * 60 + (m - M)) * 60 - S

				if(difference < 0) {
					difference += dayInMilliseconds
				}

				let t = setTimeout(() => {
					self.audioFile.play()
					setTimeout(() => {
						self.audioFile.pause()
						self.audioFile.currentTime = 0
					}, self.alarmDuration * 1000);
					let i = setInterval(() => {
						self.audioFile.play()
						setTimeout(() => {
							self.audioFile.pause()
							self.audioFile.currentTime = 0
						}, self.alarmDuration * 1000);
					}, dayInMilliseconds);
					this.intervalSet[t] = i
				}, difference * 1000);

				this.alarms.push([this.newAlarm, t])

				this.newAlarm = ''
			}
        },
        removeAlarm(index) {
			clearInterval(this.intervalSet[this.alarms[index][1]]);
			clearTimeout(this.alarms[index][1]);

			db.run("DELETE FROM alarms WHERE alarm = '" + this.alarms[index][0]+"'");
            this.$delete(this.alarms, index)
        },
		muteSound(muted = true) {
			this.audioFile.muted = muted
		}
    }
}
</script>
