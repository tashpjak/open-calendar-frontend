	<template>
	<v-dialog v-model="active" max-width="500" @click:outside="close">
		<v-tabs
			v-model="tab"
			class="elevation-2"
			:dark="dark"
			centered
			grow
		>
			<v-tabs-slider></v-tabs-slider>
			<v-tab>
				{{$t('app.event')}}
			</v-tab>
			<v-tab-item>
				<TypeEvent :cal="cal" v-on:update="update()" v-on:close="close" :type="event._id === undefined ? 'new' : 'edit'" :eventOld="event"/>
			</v-tab-item>
<!--			<v-tab>-->
<!--				{{$t('app.reminder')}}-->
<!--			</v-tab>-->
<!--			<v-tab-item>-->
<!--				<TypeReminder />-->
<!--			</v-tab-item>-->
		</v-tabs>
	</v-dialog>

</template>

<script>
	import TypeEvent from "./TypeEvent";
	import TypeReminder from "./TypeReminder";
	export default {
		name: "NewEvent",
		components: {TypeReminder, TypeEvent},
		props: {
			cal: String,
			dark: Boolean,
			active: Boolean,
			event: Object
		},
		data: () => ({
			tab: null,
			tabs: ['Event', 'Reminder', 'Task']
		}),
		methods: {
			close: function () {
				this.$emit('update')
				this.$emit('close')
			},
			update: function () {
				return this.$emit('update')
			}
		}
	}
</script>

<style scoped>

</style>