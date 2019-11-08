<template>
    <div class="question-box-container mt-2">
        <b-jumbotron>
            <template class="text-center">
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4" />

            <b-list-group class="mb-4">
                <b-list-group-item
                  v-for="(answer, index) in shuffledAnswers"
                  :key="index"
                  @click="selectAnswer(index)"
                  :class="answerClass(index)"
                >
                    {{ answer }}
                </b-list-group-item>
            </b-list-group>

            <b-button variant="primary"
              class="mr-2"
              @click="submitAnswer"
              :disabled="selectedIndex===null || answered"
            >Submit
            </b-button>
            <b-button @click="next" variant="success"
                :disabled="!answered"
            >
                Next
            </b-button>
        </b-jumbotron>
    </div>
</template>

<script>
    import _ from 'lodash';
    export default {
        name: "QuestionBox",
        props: {
            currentQuestion: Object,
            next: Function,
            increment: Function
        },
        data() {
            return {
                correctIndex: null,
                selectedIndex: null,
                shuffledAnswers: [],
                answered: false
            }
        },
        computed: {
            answers() {
                let answers = [...this.currentQuestion.incorrect_answers];
                answers.push(this.currentQuestion.correct_answer);
                return answers;
            }
        },
        watch: {
            currentQuestion: {
                immediate: true,
                handler()
                {
                    this.correctIndex = null;
                    this.selectedIndex = null;
                    this.answered = false;
                    this.shuffleAnswers();
                }
            }
        },
        methods: {
            selectAnswer(index) {
                this.selectedIndex = index;
            },
            submitAnswer() {
                let isCorrect = false;
                if(this.selectedIndex===this.correctIndex) {
                    isCorrect = true;
                }
                this.answered = true;
                this.increment(isCorrect);
            },
            shuffleAnswers() {
                let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
                this.shuffledAnswers = _.shuffle(answers);
                this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer);
            },
            answerClass(index) {
                let answerClass = '';
                if(!this.answered && this.selectedIndex === index) {
                    answerClass = 'selected';
                } else if (this.answered && this.correctIndex === index) {
                    answerClass = 'correct';
                } else if(this.answered && this.selectedIndex === index && this.correctIndex !== index) {
                    answerClass = 'incorrect';
                }
                return answerClass;
            }
        }
    }
</script>

<style scoped>
    .list-group-item:hover {
        background: #eee;
        cursor: pointer;
    }

    .selected {
        background-color: rgba(0, 123, 255, 0.34);
    }

    .correct {
        background-color: rgba(40, 167, 69, 0.43);
    }

    .incorrect {
        background-color: rgba(220, 53, 69, 0.4);
    }
</style>