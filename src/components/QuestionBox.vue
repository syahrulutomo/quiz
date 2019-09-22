<template>
    <div class="question-box-container">
        <b-jumbotron>      
            <template slot="lead">
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4" />

            <b-list-group>
                <b-list-group-item
                    v-for="(answer,index) in shuffledAnswers" 
                    :key="index"
                    @click="selectAnswer(index)"
                    :class="answerClass(index)"
                >
                    {{ answer }}
                </b-list-group-item>
            </b-list-group>

            <b-button 
                variant="primary" 
                @click="submitAnswer"
                :disabled="selectedIndex === null || answered"
                >
                    Submit
            </b-button>
            <b-button 
                @click="next" 
                variant="success" 
                >
                    Next
            </b-button>
        </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'

export default {
    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function,

    },
    data(){
        return {
            selectedIndex: null,
            correctIndex: null,
            shuffledAnswers: [],
            answered: false
        }
    },
    computed: {
        answers(){
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            return answers
        }
    },
    watch: {
        currentQuestion:{
            immediate: true,
            handler() {
                this.selectedIndex = null 
                this.answered = false
                this.shuffleAnswers()
            }
        }
    },
    methods: {
        selectAnswer(index) {
            this.selectedIndex = index 
        },
        shuffleAnswers() {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
        },
        submitAnswer() {
            let isCorrect = false

            if(this.selectedIndex === this.correctIndex){
                isCorrect = true
            }
            this.answered = true


            this.increment(isCorrect)
        },
        answerClass(index){
            let answerClass = ''
                if (!this.answered && this.selectedIndex === index ){
                    answerClass = 'selected'
                } else if (this.answered && this.correctIndex === index){
                    answerClass = 'correct'
                } else if (this.answered && this.selectedIndex === index && this.correcIndex !== index){
                    answerClass = 'incorrect'
                }
                    
            return answerClass      
        }
    }
}
</script>

<style scoped>
    .btn-success {
        margin-left: 1rem;
    }

    .list-group{
        margin-bottom: 1rem;
    }

    .list-group-item:hover{
        background-color: #EEE;
        cursor: pointer;
    }

    .selected{
        background-color: lightblue;
    }

    .correct{
        background-color: lightgreen;
    }

    .incorrect{
        background-color: red;
    }
</style>
