<template>
    <div>
        <v-textarea
          outline
          v-model="memo"
          label="할일을 입력해주세요."
          value=""
        ></v-textarea>
        <v-btn @click="listAdd">리스트 추가</v-btn>
    </div>
</template>


<script>
export default {
    data(){
        return {
            memo: null
        }
    },
    methods: {
        listAdd(){            
            console.log("this.memo : ", this.memo);
            if(this.memo === null || this.memo == ''){
                alert("할일을 입력해주세요~!")
            }else{
                this.$emit("listAdd", this.memo)
            }
            this.memo = ''
        }
    }
}
</script>
