<template>
    <input type="text" class="form-control" placeholder="Enter something..." v-model="userInput" debounce="{{update_threshold}}">
    <div class="dropdown open " v-show="dropdownList">
        <ul class="dropdown-menu">
            <li v-for="name in dropdownList">
                <a v-on:click="clickDropdown($index)">
                    {{name}}</a>
            </li>
        </ul>
    </div>
</template>

<script>
    export default{
        components:{
        },

        data(){
            return{
                // user input content
                userInput:'',

                //debounce: delay the v-model update frequency,in ms
                update_threshold:500,

                // save the dropdown content
                dropdownList:null,
            }
        },

        methods:{
            //when click on item in the dropdown list ,then send the item to the input box
            clickDropdown(index){
                this.userInput = this.dropdownList[index];
                this.dropdownList = null;
            }
        },

        ready:function () {
            //watch the userInput change and request latest dropdownList
            this.$watch(function () {
                return this.userInput
            }, function () {
                if(typeof this.userInput=='undefined' || this.userInput.length<1){
                    this.dropdownList = null
                    return;
                }
                this.$http.post('/api/queryDropdownList', {userinput:this.userInput}).then((response) => {
                    this.dropdownList = JSON.parse(response.data);
                    if(this.dropdownList.length<1) this.dropdownList = null;
                }, (response) => {});
            }, {deep:true})
        }
    }
</script>

<style>
</style>
