<template>
    <div class="col-3">
     
            <div class="card">
                <div class="card-header text-white bg-primary text-center font-weight-bold">
                    {{ category }}
                </div>
                <div class="card-body list-kanban-body" id="main-body">
                    <div v-if="tasks.length>0">
                        <kanbanCard v-for="task in tasks" :key="task.id" :task="task" @showEditForm="showEditForm" @fetchKanban="fetchKanban" @changeLogin="changeLogin" @checkLogin="checkLogin"></kanbanCard>
                    </div>
                </div>
                <!-- Add Button -->
                <div class="add-btn bg-primary" @click="showAddForm">
                    <i class="fas fa-plus"></i>
                </div>
            </div>

    </div>
</template>

<script>
import kanbanCard from './KanbanCard';

export default {
    name:'ListKanban',
    props: ['category', 'tasks'],
    components: {
        kanbanCard
    },
    data() {
        return {
            show: false,
            activity: ''
        }
    },
    methods: {
        // Menampilkan add kanban form
        showAddForm() {
            this.$emit('showAddForm', 'addKanban');
        },
        // Menampilkan edit kanban form
        showEditForm(input) {
            this.$emit('showEditForm', input);
        },
        // Parsing fetch kanban 
        fetchKanban() {
            this.$emit('fetchKanban');
        },
        // Parsing change login
        changeLogin() {
            this.$emit('changeLogin')
        },
        // Parsing check login
        checkLogin() {
            this.$emit('checkLogin');
        }
    }
}
</script>

<style>

.add-btn {
	position: fixed;
	bottom: 15px;
	right: 15px;
	background-color: #05d595;
	border-radius: 100%;
	width: 50px;
	height: 50px;
	display: flex;
	justify-content: center;
	align-items: center;
	transition: .5s;
	cursor: pointer;
}

.add-btn i {
	font-size: 30px;
	color: white;
}

.add-btn:hover {
	transform: scale(1.2);
	font-weight: bold;
}

.list-kanban-body {
    padding: 10px 0 !important;
    
}

</style>