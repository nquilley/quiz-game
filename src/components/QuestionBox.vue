<template>
    <div class="question-box-container">
        <b-jumbotron>

        <template slot="lead">
            
            {{currentQuestion.question}} <!-- for the question to display properly we reference it in the HTML here, but also need to do reference it in props below in the script tag -->
        </template>

        <hr class="my-4">

        <b-list-group>
            <b-list-group-item 
            v-for="(answer, index) in answers"
            :key="index" 
            @click.prevent="selectAnswer(index)"
            :class= "answerClass(index)" 
            >
                {{answer}}
            </b-list-group-item>
        </b-list-group>
    <!--1. using a List Group from Vue bootstrap instead of paragraph to make it look like options.
        2. For doing a v-for you need to add a Key in for it to work 
        3. (condition) ? expression on true : expression on false-->
      

        <b-button 
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered"
        >
        Submit
        </b-button>

        <b-button @click="next" variant="success" >Next Question</b-button>
        </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'

export default {
    props: {
        currentQuestion: Object,
        next: Function, //needs to be capital F!
        increment: Function // this function now needs to be added to the parent component 
    },
    data(){
        return{
        selectedIndex: null,
        correctIndex: null,
        shuffledAnswers: [],
        answered: false     
        }
    },
    computed: {
        answers() {
            let answers = [...this.currentQuestion.incorrect_answers]
             answers.push(this.currentQuestion.correct_answer)
             return answers
        }
    },
    watch:{ //This is watch method. It is just like computed or methods in that it takes an object of functions, but this object watches for changes in the props (currentQuestion). It will run this function when it changes.
       currentQuestion: { //instead of being a function (as below in comments) it can also be an object
           immediate:true, // this means that  handler will run when currentQuestion first gets passed as a prop
           handler() {
               this.selectedIndex = null //  the selectedIndex (0-3 for the questions) gets reset every time an answer is submitted
               this.answered = false
               this.suffleAnswers()
           } 
       }
       // currentQuestion() { 
        //     this.selectedIndex = null
        //     this.suffleAnswers()
        // }
    },
    methods: {
        selectAnswer (index) {
            this.selectedIndex = index
            //this means that when an answer is clicked the index of that answer is passed to this.selectedIndex
        },
        submitAnswer (){
            let isCorrect = false

            if (this.selectedIndex === this.correctIndex){
                isCorrect = true
            }
            this.answered = true //this means the user can't press submit more than once (see disbaled button above)
            this.increment(isCorrect)
        },
        suffleAnswers() {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer] //get all the answers in one array
            this.shuffledAnswers = _.shuffle(answers) //it is convention to use underscore for lowdash. Passing the answers array through the means that it will shuffle the order of the answers array
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
        },
        answerClass(index){
            let answerClass = ''

            if (!this.answered && this.selectedIndex === index){
                answerClass = 'selected'
            } else if (this.answered && this.correctIndex === index){
                answerClass = 'correct'
            } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index){
                answerClass = 'incorrect'
            }
            return answerClass
        }
    }
}
// any variables, be it data properties or methods that you are passing from app -> questions, in order for it to display in the HTML you have to also refrence it in props to say that you're receivng a particular named variable from the partent
</script>

<style scoped> 
.list-group {
    margin-bottom: 15px
}

.list-group-item:hover {
    background:#EEE;
    cursor: pointer; 
}

.btn {
    margin: 0 5px
}

/* Scoped means that it only applies to this component */
/* .btn means that there's a gap between the buttons */

.selected{
    background-color: lightblue
}

.correct{
    background-color: lightgreen
}

.incorrect{
    background-color: red
}

</style>

