<template>
<div class="otp" id="otp">
    <input v-model="filterAlg[0]" type="text" class="otp__input" maxlength="2" />
    <input v-model="filterAlg[1]" type="text" class="otp__input" maxlength="2" />
    <input v-model="filterAlg[2]" type="text" class="otp__input" maxlength="2" />
</div>
<div class="otp" id="otp">
    <input v-model="filterAlg[3]" type="text" class="otp__input" maxlength="2" />
    <input v-model="filterAlg[4]" type="text" class="otp__input" maxlength="2" />
    <input v-model="filterAlg[5]" type="text" class="otp__input" maxlength="2" />
</div>
<div class="otp" id="otp">
    <input v-model="filterAlg[6]" type="text" class="otp__input" maxlength="2" />
    <input v-model="filterAlg[7]" type="text" class="otp__input" maxlength="2" />
    <input v-model="filterAlg[8]" type="text" class="otp__input" maxlength="2" />
</div>
</template>   

<style lang="css">
    .otp {
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 1rem;
    }
    .otp__input {
        border: none;
        border: 2px solid rgb(203 213 225);
        font-size: 2rem;
        outline: none;
        padding: 0.25rem 0.5rem;
        text-align: center;
        width: 5rem;
    }
</style>

<script>
    export default{
        props: {
            filterAlg: {
                type: Object,
                }
            },
        data(){
            return{

            }
        },
        mounted(){

        },
        methods:{

        }
    }
</script>