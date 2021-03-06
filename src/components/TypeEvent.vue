<template>
	<v-card flat tile class="card">
		<v-alert type="error" v-if="error.length > 0">
			{{error}}
		</v-alert>
		<v-btn text disabled v-if="type === 'new'">Create New Event</v-btn>
		<v-btn text disabled v-if="type === 'edit'">Edit Event</v-btn>
		<v-row>
			<v-col>
				
				<v-text-field v-model="event.title" placeholder="Title" label="Title" outlined :rules="text_rule"/>
				
				<v-row>
					
					<v-col cols="3">
						
						<v-menu
							ref="menu"
							v-model="menu"
							:close-on-content-click="false"
							:return-value.sync="date"
							transition="scale-transition"
							offset-y
							min-width="290px"
						>
							<template v-slot:activator="{ on }">
								<v-text-field
									v-model="event.startDate"
									label="Start Date"
									readonly
									v-on="on"
								></v-text-field>
							</template>
							<v-date-picker v-model="event.startDate" no-title scrollable>
								<v-spacer></v-spacer>
								<v-btn text color="primary" @click="menu = false">Cancel</v-btn>
								<v-btn text color="primary" @click="$refs.menu.save(date)">OK</v-btn>
							</v-date-picker>
						</v-menu>
					</v-col>
					<v-col cols="3">
						
						<v-menu
							ref="menu2"
							v-model="menu2"
							:close-on-content-click="false"
							:nudge-right="40"
							:return-value.sync="event.startTime"
							transition="scale-transition"
							offset-y
							max-width="290px"
							min-width="290px"
						>
							<template v-slot:activator="{ on }">
								<v-text-field
									v-model="event.startTime"
									label="Start Time"
									readonly
									v-on="on"
								></v-text-field>
							</template>
							<v-time-picker
								format="24h"
								v-if="menu2"
								v-model="event.startTime"
								full-width
								@click:minute="$refs.menu2.save(event.startTime)"
							></v-time-picker>
						</v-menu>
					</v-col>
					<v-col cols="3">
						
						<v-menu
							ref="menu3"
							v-model="menu3"
							:close-on-content-click="false"
							:nudge-right="40"
							:return-value.sync="event.endTime"
							transition="scale-transition"
							offset-y
							max-width="290px"
							min-width="290px"
						>
							<template v-slot:activator="{ on }">
								<v-text-field
									v-model="event.endTime"
									label="End Time"
									readonly
									v-on="on"
								></v-text-field>
							</template>
							<v-time-picker
								format="24h"
								v-if="menu3"
								v-model="event.endTime"
								full-width
								@click:minute="$refs.menu3.save(event.endTime)"
							></v-time-picker>
						</v-menu>
					</v-col>
					<v-col cols="3">
						<v-menu
							ref="menu4"
							v-model="menu4"
							:close-on-content-click="false"
							:return-value.sync="date"
							transition="scale-transition"
							offset-y
							min-width="290px"
						>
							<template v-slot:activator="{ on }">
								<v-text-field
									v-model="event.endDate"
									label="End Date"
									readonly
									v-on="on"
								></v-text-field>
							</template>
							<v-date-picker v-model="event.endDate" no-title scrollable>
								<v-spacer></v-spacer>
								<v-btn text color="primary" @click="menu4 = false">Cancel</v-btn>
								<v-btn text color="primary" @click="$refs.menu4.save(event.endDate)">OK</v-btn>
							</v-date-picker>
						</v-menu>
					</v-col>
				</v-row>
				
				
				<v-text-field outlined placeholder="Location" label="Location" prepend-icon="mdi-map" v-model="event.location"/>
				<div class="notes">
					<v-btn text disabled>Notes</v-btn>
					<v-textarea
						outlined
						label="Note"
						no-resize
						v-model="event.notes"
					></v-textarea>
				</div>
				<v-btn text disabled>Color</v-btn>
				<v-color-picker v-model="event.color" class="ma-auto" width="100%" hide-canvas></v-color-picker>
				
				<v-card-actions>
					
					<v-btn depressed color="primary" @click="close()">Close</v-btn>
					<v-spacer></v-spacer>
					<v-btn depressed color="primary" @click="save()">Save</v-btn>
				</v-card-actions>
			
			</v-col>
		</v-row>
	</v-card>
</template>

<script>
	import {API} from "../conf";

	export default {
		name: "TypeEvent",
		props: {
			cal: String,
			type: String,
			eventOld: Object
		},
		data: () => ({
			text_rule: [
				v => !!v || 'Name is required',
			],
			error: '',
			menu: false,
			menu2: false,
			menu3: false,
			menu4: false,
			event: {
				color: '',
				title: 'My Event',
				startDate: new Date().toISOString().substr(0, 10),
				endDate: new Date().toISOString().substr(0, 10),
				startTime: new Date().toISOString().substr(11, 5),
				endTime: new Date().toISOString().substr(11, 5),
				location: '',
				notes: ''
			}
		}),
		mounted() {
			if (this.eventOld.color !== undefined) this.event.color = this.eventOld.color;
			if (this.eventOld.title !== undefined) this.event.title = this.eventOld.title;
			if (this.eventOld.startDate !== undefined) this.event.startDate = this.eventOld.startDate;
			if (this.eventOld.endDate !== undefined) this.event.endDate = this.eventOld.endDate;
			if (this.eventOld.startTime !== undefined) this.event.startTime = this.eventOld.startTime;
			if (this.eventOld.endTime !== undefined) this.event.endTime = this.eventOld.endTime;
			if (this.eventOld.location !== undefined) this.event.location = this.eventOld.location;
			if (this.eventOld.notes !== undefined) this.event.notes = this.eventOld.notes;
		},
		methods: {
			save: async function () {
				if (this.type === 'edit') {
					let event = {
						color: this.event.color,
						title: this.event.title,
						location: this.event.location,
						notes: [this.event.notes],
						start: this.event.startDate + 'T' + this.event.startTime + ':00.000Z',
						end: this.event.endDate + 'T' + this.event.endTime + ':00.000Z',
					}
					const app = window.location.pathname.split(':')[1]
					let res = await fetch(API + '/api/event/' + app + '' + this.eventOld._id,
						{
							method: 'PUT', mode: 'cors',
							headers: {
								'Accept': 'application/json',
								'Content-Type': 'application/json'
							},
							body: JSON.stringify(event)
						})
					res = await res.json();
					console.log(res)
					if(res.error) {
						this.error = res.error || '';
					} else {
						this.$emit('close')
					}
					return;
				}
				let event = {
					color: this.event.color,
					title: this.event.title,
					location: this.event.location,
					notes: [this.event.notes],
					start: this.event.startDate + 'T' + this.event.startTime + ':00.000Z',
					end: this.event.endDate + 'T' + this.event.endTime + ':00.000Z',
				}
				if (new Date(event.end) < new Date(event.start)) {
					this.error = 'Start Date cannot be later than End Date'
					return;
				}
				console.log(event)
				
				fetch(API + '/api/event/' + this.cal, {
						method: 'POST',
						mode: 'cors',
						headers: {
							'Accept': 'application/json',
							'Content-Type': 'application/json'
						},
						body: JSON.stringify(event)
					}).then(response => {
						if(!response.ok) {
							return response.json();
						}
						return response.json();
				}).then((res) => {
					if (res.error) {
						this.error = res.error;
					}
				})
				
				this.$emit('update')
				this.$emit('close')

			},
			close: function () {
				this.$emit('close')
			}
		}
	}
</script>

<style scoped>
	.card {
		padding: 4px 16px !important;
	}
</style>