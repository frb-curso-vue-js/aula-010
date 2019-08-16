<template>
	<div id="app">
        <div class="barra" v-if="tasks.length > 0">
            <div class="barra-progresso" :style="{width: barra+'%'}">
                <template v-if="barra != 0">{{ Math.floor(barra) }}%</template>
            </div>
        </div>
        <br>
        <input type="text" v-model="novo" @keypress.enter="newTask">
        <button @click="newTask">+</button>
        <br>
        <div v-if="tasks.length > 0" class="lista-tarefas">
        <div v-for="task in tasks" :key="task.id">
            <div :class="[{incomplete: !task.completed}, {complete: task.completed}]">
            <div @click="changeState(task)">
                <h5>Texto: {{ task.text }}</h5>
                <h6>ID: {{ task.id + 1 }}</h6>
            </div>
            <button @click="deleteTask(task)" class="tarefa-botao">x</button>
            </div>
            <br>
        </div>
        </div>
        <div v-else>
            Não existem tarefas.
        </div>
        <br>
	</div>
</template>

<script>

export default {
    data() {
        return {
            tasks: [],
            novo: '',
            counter: 0,
            barra: 0
        }
    },
    created() {
        const json = localStorage.getItem('tasks')
        this.tasks = JSON.parse(json) || []
    },
    methods: {
        newTask() {
            if (this.novo !== null && this.novo.trim() !== '') {
                let notExists = true
                this.tasks.forEach(task => {
                    if (task.text == this.novo) {
                        notExists = false
                    }
                });
                if (notExists) {
                    this.tasks.push({
                        id: this.counter,
                        completed: false,
                        text: this.novo
                    })
                    this.counter++
                    this.novo = ''
                } else {
                    alert("Uma tarefa com esse nome já existe!")
                }
            } else {
                alert("Deve inserir um texto para a tarefa!")
            }
        },
        updateBar(){
            let calc = 0
            if (this.tasks.length > 0) {
                this.tasks.forEach(task => {
                    task.completed ? calc++ : null
                });
            }
            this.barra = (calc / this.tasks.length) * 100
        },
        deleteTask(task) {
            let id_array = this.tasks.indexOf(task)
            this.tasks.splice(id_array, 1);
        },
        changeState(task) {
            let id_array = this.tasks.indexOf(task)
            this.tasks[id_array].completed = !this.tasks[id_array].completed
            this.updateBar();
        },
    },
    watch: {
        tasks: {
            deep: true,
            handler() {
                this.updateBar();
                localStorage.setItem('tasks', JSON.stringify(this.tasks))
        }
        }
    }
}
</script>

<style scoped>

    .tarefa-botao {
        border: 4px solid greenyellow;
        background-color: lime;
        width: 100%;
        border-radius: 5px;
    }

    .barra {
        width: 800px;
        height: 40px;
        border: 1px solid grey;
        border-radius: 6px;
    }

    .barra-progresso {
        height: 100%;
        background-color: green;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .incomplete {
        box-sizing: border-box;
        cursor: pointer;
        user-select: none;
        border: 10px solid red;
        border-radius: 5px;
        background-color: darkred;
        color: white;
        padding: 30px;
    }

    .complete {
        border: 10px solid lime;
        cursor: pointer;
        user-select: none;
        border-radius: 5px;
        background-color: darkgreen;
        color: white;
        text-decoration: line-through;
        padding: 30px;
    }

    .lista-tarefas {
        display: inline-flex;
    }

</style>