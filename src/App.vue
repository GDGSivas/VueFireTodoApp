<template>

    <div class="card col-md-6 offset-md-3">
        <div class="card-header h1">Yapılacaklar Listesi</div>
        <div class="card-body">
            <form class="form-inline" @submit.prevent="addTodo">
                <div class="input-group w-100">
                    <input class="form-control-lg"
                           type="text"
                           autofocus
                           placeholder="Yaz..."
                           v-model="input"
                           style="width: 85%">
                    <span class="input-group-btn" style="width: 15%;">
                            <button class="btn btn-primary btn-lg" type="submit">Ekle</button>
                        </span>
                </div>
            </form>
            <div class="btn-group mt-4 text-center w-100">
                <button class="btn"
                        v-for="(button,id) in buttons"
                        :key="id"
                        :class="getButtonClass(id)"
                        @click="setActiveButton(id)"
                >
                    {{button}}
                </button>
            </div>
            <div class="mt-4">
                <div class="row" v-for="todo in filteredTodos" :key="todo.id" @click="toggle(todo)">
                    <del class="h3 col-10" v-if="todo.done">
                        {{todo.text}}
                    </del>
                    <span class="h3 col-10" v-else>
                       {{todo.text}}
                    </span>
                    <div class="col-2">
                        <button class="btn btn-danger" @click="deleteTodo(todo)">X</button>
                    </div>
                </div>
            </div>
        </div>
    </div>

</template>

<script>
	import firebase from 'firebase/app'
	import 'firebase/firestore'
	// Get a Firestore instance
	const db = firebase
	.initializeApp({projectId: 'vuefiretodo-c633c'})
	.firestore()
	const todosCollection = db.collection('todos');

	export default {
		data: () => ({
			todos: [],
			input: "",
			activeButton: 0,
			buttons: [
				"HEPSİ",
				"YAPILMIŞ",
				"YAPILACAK"
			]
		}),
		methods: {
			addTodo() {
				todosCollection.add({
					"text": this.input,
					"done": false,
					"date": new Date()
				});
				this.input = "";
			},
			toggle(todo) {
				todosCollection.doc(todo.id).update({
					"done": !todo.done
				});
			},
			deleteTodo(todo) {
				todosCollection.doc(todo.id).delete();
			},
			getButtonClass(id) {
				return id == this.activeButton ? "btn-primary" : "btn-outline-primary";
			},
			setActiveButton(id) {
				this.activeButton = id;
			}
		},
		firestore: {
			todos: todosCollection.orderBy("date", "desc"),
		},
		computed: {
			filteredTodos: function () {
				if (this.activeButton == 1) return this.todos.filter(todo => todo.done);
				else if (this.activeButton == 2) return this.todos.filter(todo => !todo.done);
				return this.todos;
			}
		}
	}
</script>
