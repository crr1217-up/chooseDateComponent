<template>
    <div class="app">
        <date-picker :date="date"/>
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
    }
}
</script>

<style scoped>

</style>