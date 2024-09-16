<template>
    <v-otp-input :model-value="otp1" :length="3"></v-otp-input>
    <v-otp-input :model-value="otp2" :length="3"></v-otp-input>
    <v-otp-input :model-value="otp3" :length="3"></v-otp-input>
</template>   
  
<script>
    export default{
        data(){
            return{
                otp1: "111",
                otp2: "111",
                otp3: "111"
            }
        },
        mounted(){

        },
    }
</script>