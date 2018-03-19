<template>
  <div>
    <input type="text" name="form.field.extra_questions" v-bind:value="vv">
    <h1>DAS IST EIN TEST</h1>
    {{questions}}
    <form class="form-inline" @submit.prevent="save_question">
      <div class="form-group">
        <label for="exampleInputName2">Frage</label>
        <input v-model="question.question"
               type="text" class="form-control"
               id="exampleInputName2" placeholder="Jane Doe" />
      </div>
      <div class="form-group">
        <label for="exampleInputEmail2">Type</label>
        <select v-model="question.type" v-on:change="may_need_answers" class="form-control" id="exampleInputEmail2">
    <option value="choice" selected="selected">Select in values</option>
    <option value="multi">Select several in values</option>
    <option value="bool">True or False</option>
</select>
    </div>
      <div class="form-group" v-if="question.need_answers">
        <label for="exampleInputEmail2">Answers</label>
          <span v-for="answer in question.answers" v-bind:key="answer">
    <input v-model="answer.value" />
    <button class="btn btn-default"
         v-on:click.prevent="remove_answer(answer)" >remove</button>
    </span>
        
        <button type="submit" class="btn btn-default"
		v-on:click.prevent="add_answer" >Add answer</button>
      </div>
      <hr />
      <div>
	<button type="submit" class="btn btn-default">Add Question</button>
	<button type="submit" class="btn btn-default"
		v-on:click.prevent="save_all">Save</button>
      </div>
    </form>
  </div>
</template>

<script>
/* eslint-disable */
let QUESTION = {
    question: '',
    type: '',
    need_answers: false,
    answers: []
}

function check_answer(answer) {
    console.log(answer);
    if (answer.replace(/^\s+|\s+$/g, '')) {
	return answer.includes('::');
    }
    return false;
}

export default {
    methods: {
        save_all() {
            console.log('SAVE A-ll')
            var ss = ""
            this.questions.forEach(
		element => {
		    if (element.need_answers) {
			let choices = []
			element.answers.forEach(function (answer) {
			    let value = answer.value.trim();
			    if (value.length) {
				choices.push(value);
			    }
			});
			ss = (element.question.trim() + ' => ' +
			      element.type.trim() + '::' +
			      choices.join('::'))
		    }
		    else {
			ss = (element.question.trim() + ' => ' +
			      element.type.trim())
		    }
		    console.log(ss)
		    this.vv = ss
		    console.log(this.vv)
		}
            )
        },
	remove_answer(answer) {
	    this.question.answers = this.question.answers.filter(
		(a) => a != answer)
	},
	may_need_answers(e) {
	    console.log(e);
	    this.question.need_answers = ['choice', 'multi'].includes(
		this.question.type)
	    if (!this.question.need_answers) {
		this.question.answers = []
	    }
	},
        save_question() {
	    var error = false;
	    if (!this.question.question.trim()) {
		error = true;
		alert('Please fill the question.');
	    }
	    if (!this.question.type.trim()) {
		error = true;
		alert('Please select a type of question.');
	    }
	    else if (this.question.need_answers) {
		let choices = []
		this.question.answers.forEach(function (answer) {
		    let value = answer.value.trim();
		    if (value.length) {
			choices.push(value);
		    }
		});
		if (choices.length < 2) {
		    alert('Please add at least 2 non-empty choices.');
		    error = true;
		}
	    }
	    if (!error) {
		this.questions.push(
		    Object.assign({}, this.question)
		)
		this.question.question=''
		this.question.type=''
		this.question.answers=[]
	    }
        },
        add_answer() {
            this.question.answers.push(Object({value: ''}));
        }
    },
    data() {
	return {
            vv: '',
            questions: [],
            question: QUESTION
	}
    }
}
</script>

<style scoped>

</style>
