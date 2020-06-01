<template>
    <div class="app">
        <!-- <date-picker :date="date" @choose-date="changeDate"/> -->
        <date-picker v-model="date"/>
    </div>
    
</template>

<script>
import datePicker from "./datePicker.vue";

export default {
    components:{
        datePicker,
    },
    data(){
        return {
            date:new Date()
        }
    },
    
}
</script>

<style scoped>

</style>