<template>
  <div class="todo" @click="closeAddTaskActive();closeAddProjectActive();">
    <div class="todo-search" >
      <div class="container">
        <div class="welcome">
          <h1 class="title"> {{ titleGenerator() }} </h1>
          <p class="subtitle">{{ subTitleGenerator() }}</p>

          <input v-if="this.projectSelected !=null" v-model="searchText" placeholder="Search Task here.." />
        </div>
        <div class="projects">
            <div class="title">
              <h3> PROJECTS </h3>
              <p>({{ this.projectsListData.length }})</p> 
            </div>
            <div class="projects-list">
            <div  v-for="project in getFirstProjects()" @click="setProjectId(project.id)" :key="project.id" class="project-item">
              <span :class="{active: this.projectSelected == project.id}">{{simpleProjectName(project.name)}}</span>
              <p>{{project.name}}</p>
              <div class="delete-project" @click="projectDelete(project.id)">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M20.25 7.5l-.625 10.632a2.25 2.25 0 01-2.247 2.118H6.622a2.25 2.25 0 01-2.247-2.118L3.75 7.5m6 4.125l2.25 2.25m0 0l2.25 2.25M12 13.875l2.25-2.25M12 13.875l-2.25 2.25M3.375 7.5h17.25c.621 0 1.125-.504 1.125-1.125v-1.5c0-.621-.504-1.125-1.125-1.125H3.375c-.621 0-1.125.504-1.125 1.125v1.5c0 .621.504 1.125 1.125 1.125z" />
                </svg>
              </div>
            </div>

            <div class="project-item" @click.stop="toggleAddProjectActive">
              <span >
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-3 h-3">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15" />
                </svg>
              </span>
              <p>New Project</p>
            </div>
            <div v-if="this.projectsListData.length > 7" class="project-item" >
              <span >
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M6.75 12a.75.75 0 11-1.5 0 .75.75 0 011.5 0zM12.75 12a.75.75 0 11-1.5 0 .75.75 0 011.5 0zM18.75 12a.75.75 0 11-1.5 0 .75.75 0 011.5 0z" />
                </svg>
              </span>
              <p>All Projects</p>
            </div>
          </div>
        </div>


      </div>
    </div>

    <div v-show="this.addActive" class="todo-add"  @click.stop>
      <div class="todo-add-title">

        <h3> NEW TASK </h3>
        <p > Fill in the blank spaces to add a new task.  </p>
      </div>
      <div class="todo-add-info">

        
        <div class="todo-add-form">
          <input type="text" v-model="taskAddData.title" placeholder="Write task title" >
          <textarea v-model="taskAddData.text" ></textarea>
          <button @click="addTask"> Add Task </button>
        </div>

      </div>
    </div>
    <div v-if="this.projectSelected !=null" class="todo-tasks">
      <h2 class="todo-tasks-title"> MY TASKS </h2>
      <div class="todo-tasks-select-filter">
         <p class="todo-tasks-select-filter-option" :class="{active:( this.statusFilter == 'all' ? true : false)}" @click="() => { this.statusFilter = 'all' }">All</p> 
         <p class="todo-tasks-select-filter-option" :class="{active:( this.statusFilter == 'completed' ? true : false)}" @click="() => { this.statusFilter = 'completed' }">Completed</p> 
      </div>
      <div v-if="!emptyCheck()"  class="todo-tasks-list">
        
        <div class="todo-tasks-list-card"  v-for="task in this.searchFilter()" v-bind:key="task.id" v-show="task.projectid == this.projectSelected"  >     
          <div class="todo-tasks-list-card-done">   
            <svg :class="{active: task.status == 'completed'}" @click="toggleCompleted(task.id)" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
              <path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75L11.25 15 15 9.75M21 12c0 1.268-.63 2.39-1.593 3.068a3.745 3.745 0 01-1.043 3.296 3.745 3.745 0 01-3.296 1.043A3.745 3.745 0 0112 21c-1.268 0-2.39-.63-3.068-1.593a3.746 3.746 0 01-3.296-1.043 3.745 3.745 0 01-1.043-3.296A3.745 3.745 0 013 12c0-1.268.63-2.39 1.593-3.068a3.745 3.745 0 011.043-3.296 3.746 3.746 0 013.296-1.043A3.746 3.746 0 0112 3c1.268 0 2.39.63 3.068 1.593a3.746 3.746 0 013.296 1.043 3.746 3.746 0 011.043 3.296A3.745 3.745 0 0121 12z" />
            </svg>
          </div>  
          <div class="todo-tasks-list-card-text">
            <h2>{{ task.title }}</h2>
            <p>{{ task.text }}</p>
          </div>
          <div v-if="this.statusFilter == 'completed'" class="todo-tasks-list-card-delete" @click="taskSetUncompleted(task.id)">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
              <path stroke-linecap="round" stroke-linejoin="round" d="M15 12H9m12 0a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
          </div>
          <div class="todo-tasks-list-card-delete" @click="taskDelete(task.id)">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
              <path stroke-linecap="round" stroke-linejoin="round" d="M20.25 7.5l-.625 10.632a2.25 2.25 0 01-2.247 2.118H6.622a2.25 2.25 0 01-2.247-2.118L3.75 7.5m6 4.125l2.25 2.25m0 0l2.25 2.25M12 13.875l2.25-2.25M12 13.875l-2.25 2.25M3.375 7.5h17.25c.621 0 1.125-.504 1.125-1.125v-1.5c0-.621-.504-1.125-1.125-1.125H3.375c-.621 0-1.125.504-1.125 1.125v1.5c0 .621.504 1.125 1.125 1.125z" />
            </svg>
          </div>


        </div>


      </div>
      <div v-if="emptyCheck()" class="todo-tasks-listempty"  >
          <p v-show="!isLoading">{{ this.emptyMessage }}</p>
          <span v-show="isLoading"></span>
      </div>
    </div>
    <div v-if="this.projectSelected !=null" class="todo-add-togglebutton" @click.stop="toggleAddActive">
      <svg v-show="!this.addActive" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
        <path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15" />
      </svg>
      <svg v-show="this.addActive"  xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
        <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
      </svg>

    </div>
    <div @click.stop v-show="this.addProjectActive" class="todo-add-projectinput" >
      <h3> NEW PROJECT </h3>
      <div class="todo-add-projectinput-container">
      <input v-model="this.projectAddData.name" placeholder="Escribe un nombre de proyecto" maxlength="18" minlength="4" ref="projectInputRef" />
      <span class="add-project" @click="addProject">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="3" stroke="currentColor" class="w-6 h-6">
          <path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" />
        </svg>
      </span>
      <span @click="toggleAddProjectActive" class="delete-project">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="3" stroke="currentColor" class="w-6 h-6">
          <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
        </svg>
      </span>
    </div>
    </div>
    <div v-if="this.errorCaptured" class="todo-add-form-error">{{ this.errorMessage }} </div>
    <div class="copyright">
      <h1>TASK MANAGER</h1>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TodoList',
  data() {
      return {
          taskListData: [],
          projectsListData: [],
          isLoading:true,
          addActive:false,
          statusFilter: 'all',
          emptyMessage:'No hay ninguna tarea',
          addProjectActive:false,
          errorCaptured:false,
          searchText: '',
          errorMessage: '',
          projectSelected:null,
          projectAddData:{
            id:0,
            name:'',
            color:'',
          },
          taskAddData: {
              id: 0,
              title: '',
              text:'',
              color: '#071E22',
              status: 'waiting',
              projectid: 0,
          }
      }
  },
  created(){

        fetch('https://63530f39d0bca53a8eb9fa65.mockapi.io/tasks')
        .then(res => res.json())
        .then(response => {
                this.taskListData = response;
                this.isLoading = false;
            })
        fetch('https://63530f39d0bca53a8eb9fa65.mockapi.io/proyectos')
        .then(res => res.json())
        .then(response => {
                this.projectsListData = response;
            })
  },
  methods:{
    focusProjectInput() {
      setTimeout(() => {
        
        this.$refs.projectInputRef.focus()
      }, 500);
    }, 
    searchFilter(){
      if(this.statusFilter == 'all'){
        return this.taskListData.filter( task => task.title.toLowerCase().startsWith(this.searchText.toLowerCase()) && task.status != 'completed');
      }
      if (this.statusFilter == 'completed'){
        return this.taskListData.filter( task => task.title.toLowerCase().startsWith(this.searchText.toLowerCase()) && task.status == 'completed');
      }
    },
    titleGenerator(){
      if(this.projectsListData.length < 1){
        return 'CREATE PROJECT'
        
      }
      if(this.projectSelected == null){
        return 'SELECT PROJECT'
      }
      return 'HELLO WORLD'
    },
    subTitleGenerator(){
      if(this.projectsListData.length < 1){
        return 'Start creating your first project now!'
        
      }
      if(this.projectSelected == null){
        return 'Select any project to continue!'
      }
      return 'Welcome back to the workspace!'
    },
    simpleProjectName(name){
      let nameSliced = name.split(" ").map(word => word.slice(0,1));
      let simplifiedName = nameSliced.reduce( function(a,b){
        return (a+b).toUpperCase();
      })
      return simplifiedName
    },
    getFirstProjects(){
      return this.projectsListData.length <= 7 ? this.projectsListData.slice(0,7):this.projectsListData.slice(0,6)    
    },
    showError(text){
      this.errorCaptured = true;
      this.errorMessage = text;
        setTimeout(() => {
          this.errorCaptured = false;
          this.errorMessage = '';
        }, 3000)
    },
    emptyCheck(){
      let result;
      if(this.projectsListData.length < 1){
        console.log(this.projectsListData.length)
        this.emptyMessage = 'No hay ningun proyecto';
        return true
      }
      else{
        if(this.statusFilter == 'all'){
          result = this.taskListData.filter(task => task.status != 'completed');
          this.emptyMessage = 'No hay ninguna tarea';
          return result.length < 1 ?  false : true
        }
        if(this.statusFilter == 'completed'){
          result = this.taskListData.filter(task => task.status == 'completed');
          this.emptyMessage = 'No hay ninguna tarea completada';
          return result.length < 1 ?  true: false
        }
      }
    },
    closeAddTaskActive(){
      this.taskAddData.title = "";
      this.taskAddData.text = "";
      this.addActive = false;

    },
    closeAddProjectActive(){
      this.projectAddData.name = "";
      this.addProjectActive = false;

    },
    toggleAddActive(){
      this.taskAddData.title = "";
      this.taskAddData.text = "";
      this.addActive = !this.addActive;
    },

    setProjectId(id){
      this.taskAddData.projectid = id;
      this.statusFilter = 'all';
      this.projectSelected = id;
    },
    toggleAddProjectActive(){
      this.projectAddData.name = '';
      this.addProjectActive = !this.addProjectActive;
      this.focusProjectInput();
    },
    taskDelete(id){
      fetch('https://63530f39d0bca53a8eb9fa65.mockapi.io/tasks/' + id,{
        method: 'DELETE',
        headers: {
          'Content-Type': 'application/json'
        }
      }).then(res => {
        if(res.status == 200){
          this.taskListData = this.taskListData.filter(task => task.id != id);
        }
      });
    },



    projectDelete(id){

      fetch('https://63530f39d0bca53a8eb9fa65.mockapi.io/proyectos/' + id,{
        method: 'DELETE',
        headers: {
          'Content-Type': 'application/json'
        }
      }).then(res => {
        let taskToDelete = this.taskListData.filter( task => task.projectid == id);
        taskToDelete.map(task => {
          fetch('https://63530f39d0bca53a8eb9fa65.mockapi.io/tasks/' + task.id,{
              method: 'DELETE',
              headers: {
                'Content-Type': 'application/json'
              }
            }).then(res => {
              if(res.status == 200){
                this.taskListData = this.taskListData.filter( task => task.projectid != id);
              }
          });
        })

        if(res.status == 200){
          this.projectSelected = null;
          this.showError("Project deleted successfully");
          this.projectsListData = this.projectsListData.filter(project => project.id != id);
        }
      });
    },
    

    toggleCompleted(id){
      fetch('https://63530f39d0bca53a8eb9fa65.mockapi.io/tasks/' + id,{
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json'
        },
      body: JSON.stringify({status:'completed'})
      }).then(res => {
        if(res.status == 200){
          console.log(this.taskListData);
          this.taskListData.map(task => task.id == id ? task.status = 'completed': 'waiting');
          console.log(this.taskListData);
        }
      });
    },

    taskSetUncompleted(id){
      fetch('https://63530f39d0bca53a8eb9fa65.mockapi.io/tasks/' + id,{
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json'
        },
      body: JSON.stringify({status:'waiting'})
      }).then(res => {
        if(res.status == 200){
          this.taskListData.map(task => task.id == id ? task.status = 'waiting': 'completed');
        }
      });
    },
    
    addTask() {
      if(this.projectSelected == null){
        return this.showError('Select any project to continue')
      }

      if(this.taskAddData.title.length <= 4){
        return this.showError('Title is to short')
      }

      if(this.taskAddData.text.length  <= 12){
        return this.showError('Description must be have more than 12 letters')
      }


      fetch('https://63530f39d0bca53a8eb9fa65.mockapi.io/tasks',{
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
        body: JSON.stringify(this.taskAddData)
        }).then(() => {
          
          fetch('https://63530f39d0bca53a8eb9fa65.mockapi.io/tasks')
          .then(res => res.json())
          .then(response => {
                  this.taskListData = response;
              })
          this.toggleAddActive();
          this.showError('Task added successfully')
        });
      
    },
    addProject() {

      if(this.projectAddData.name.length <= 4){
        return this.showError('Project name is to short')
      }

      if(this.projectAddData.name.length >= 18){
        return this.showError('Project name is to long')
      }

      fetch('https://63530f39d0bca53a8eb9fa65.mockapi.io/proyectos',{
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
        body: JSON.stringify(this.projectAddData)
        }).then(() => {
             
          fetch('https://63530f39d0bca53a8eb9fa65.mockapi.io/proyectos')
          .then(res => res.json())
          .then(response => {
                  this.projectsListData = response;
              })
          this.toggleAddProjectActive();
          this.showError('Project added successfully')
        });
      
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.todo{
  position: relative;
  display:flex;
  align-items: center;
  width:100%;
  height:100%;
  background-color: var(--color-blue);
}

.todo-search{
    width: 50%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    -webkit-user-select: none;
    -moz-user-select: none;
    user-select: none;
}
.todo-search .container {
  display:flex;
  flex-direction: column;
  justify-content: space-evenly;
  height: 100%;
  width: 50%;
  min-width: 300px;
}
.todo-search .container .welcome{
    display: flex;
    align-items: flex-start;
    flex-direction: column;
}

.todo-search .container .welcome .title{
  color:white
}

.todo-search .container .welcome .subtitle{
  margin-bottom:2rem;
  font-weight: bold;
  color:var(--color-blue-light);
}


.todo-search .container .welcome input{
  width: 100%;
  padding:15px;
  border-radius: 1rem;
  border:none;
  outline: none;
  box-sizing: border-box;
  color:var(--color-grey-dark);
  background-color: var(--color-blue-light);
}
.todo-search .container .projects{
  display:flex;
  flex-direction: column;
  align-items: flex-start;
  width: 100%;
}
.todo-search .container .projects .title{
  display:flex;
  align-items: center;
}
.todo-search .container .projects .title h3{
  margin-right: 0.5rem;
  color:white
}
.todo-search .container .projects .title p{
  color:rgb(72, 75, 94);
  font-weight: bold;
}
.todo-search .container .projects-list{
  display:grid;
  width: 100%;
  justify-content:center;
  grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
  grid-template-rows: repeat(auto-fill, 1fr);
  gap: 1rem;
  max-height: 500px;
  flex-direction: row;
}

.todo-search .container .projects .project-item{
  display:flex;
  position: relative;
  flex-direction: column;
  align-items: center;
  cursor: pointer;
}

.todo-search .container .projects .project-item .delete-project{
  position:absolute;
  top:10px;
  right: -10px;
}
.todo-search .container .projects .project-item .delete-project svg{

  width: 15px;
  height:15px;
  padding:5px;
  color:var(--color-grey);
  background-color:var(--color-blue);
  border-radius: 50px;
}


.todo-search .container .projects .project-item .delete-project svg:hover{
  color:red;
  background-color:white;
}



.todo-search .container .projects .project-item span{
  display:flex;
  align-items: center;
  justify-content: center;
  font-size: 2rem;
  font-weight: bold;
  color:var(--color-white);
  width: 100px;
  height: 100px;
  background-color: var(--color-blue-light);
	margin:1rem;
  border-radius: 1rem;
  border: 5px solid transparent;
}
.todo-search .container .projects .project-item span:hover{
  border: 5px solid var(--color-blue); 
  box-shadow: inset 0 -3em 3em rgba(0, 0, 0, 0.1), 0 0 0 3px var(--color-green),
    0.3em 0.3em 1em rgba(0, 0, 0, 0.3);
}

.todo-search .container .projects .project-item span.active{
  border: 5px solid var(--color-blue); 
  box-shadow: inset 0 -3em 3em rgba(0, 0, 0, 0.1), 0 0 0 3px var(--color-green),
    0.3em 0.3em 1em rgba(0, 0, 0, 0.3);
}


.todo-search .container .projects .project-item span svg{
  height: 2.5rem;
  width: 2.5rem;
}

.todo-search .container .projects .project-item p{
  font-weight: bold;
  color:var(--color-grey-dark);
}



.todo-add{    
    position:absolute;
    right:5rem;
    bottom:5rem;
    width: 25%;
    height: 50%;
    border-radius: 1rem;
    z-index: 1;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    background-color: rgb(38, 42, 65, 0.95);
    -webkit-user-select: none;
    -moz-user-select: none;
    user-select: none;
}

.todo-add-title{
  width: 80%;
  text-align: left;
  color:white
}

.todo-add-title p{
  color:var(--color-grey)
}
.todo-add-info{
  height: 80%;
  width: 80%
}
.todo-add-form{
  display:flex;
  height: 100%;
  width: 100%;
  position:relative;
  flex-direction: column;
  justify-content: space-evenly;
}

.todo-add-colorpick{
}

.todo-add-form input{
  width: 100%;
  height: 50px;
  border-radius: 1rem;
  background-color: var(--color-white);
  border: none;
  padding-left:1rem;
  box-sizing: border-box;
  
  outline: none;
}

.todo-add-form textarea{
  width: 100%;
  resize: none;
  min-height: 200px;
  border-radius: 1rem;
  background-color: var(--color-white);
  border: none;
  padding:1rem;
  box-sizing: border-box;
  outline: none;
}

.todo-add-form button{
  width: 100%;
  height: 50px;
  border-radius: 0.75rem;
  background-color: var(--color-white);
  border: none;
  padding-left:1rem;
  box-sizing: border-box;
  outline: none;
}

.todo-add-form button:hover{
  cursor: pointer;
  color:var(--color-white);
  background-color: var(--color-green);
}

.todo-add-form-error{
  position: absolute;
  height: fit-content;
  z-index: 1;
  width: 250px;
  top: 0;
  right:0;
  bottom:0;
  left:0;
  margin:auto;
  padding:2rem;
  font-size: 18px;
  font-weight: 600;
  border-radius: 1rem;
  background-color: var(--color-grey-light-transparent);
  color:var(--color-blue);

}
.todo-tasks{
  position: relative;
  width:50%;
  height:calc(100% - 1rem);
  display:flex;
  flex-direction: column;
  justify-content: space-evenly;
  background-color: var(--color-white);
  border-radius: 1rem;
  margin:0.5rem;
  user-select: none;
}

.todo-tasks.active{
}
.todo-tasks-title{
}
.todo-tasks-select-filter{
  display:flex;
  justify-content: center;
}
.todo-tasks-select-filter-option{
  cursor: pointer;
  margin:0rem 0.5rem;
  padding:0.25rem 1rem;
  border-radius: 1rem;
  color:var(--color-blue);
  background-color: var(--color-grey-light);
}
.todo-tasks-select-filter-option.active{
  color:var(--color-white);
  background-color: var(--color-blue);
  
}
.todo-tasks-list{
  display:flex;
  height: 80%;
  flex-direction: column;
  align-items: center;
  overflow-y: auto;
}
.todo-tasks-listempty{
  height: 80%;
  display:flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.todo-tasks-listempty span {  
  border: 4px solid #f3f3f3; /* Light grey */
  border-top: 4px solid #3498db; /* Blue */
  border-radius: 50%;
  width: 30px;
  height: 30px;
  animation: spin 2s linear infinite; 
}

.todo-tasks-list-card{
  width: 80%;
  display:flex;
  justify-content: space-between;
  align-items: center;
  border-radius: 1rem;
  margin: 1rem 0rem;
}


.todo-tasks-list-card svg{
  height:2rem;
  width: 2rem;
  color: var(--color-grey);
}

.todo-tasks-list-card svg:hover{
  color:var(--color-green);
  cursor: pointer;
}

.todo-tasks-list-card svg.active{
  color:var(--color-green);
}

.todo-tasks-list-card-delete svg{
  height:1.5rem;
  width: 1.5rem;
  color: var(--color-grey);
}
.todo-tasks-list-card-delete svg:hover{
  color: red;
  cursor: pointer;
}

.todo-tasks-list-card-status{
  font-size: 12px;
  padding:5px 10px;
  border-radius: 1rem;
  color: var(--color-white);
}
.todo-tasks-list-card-text{
  width: 80%;
  box-sizing: border-box;
  text-align: left;
  overflow-wrap: break-word;
  color:black;
  font-weight: 500;
}


.todo-add-togglebutton{
  position:absolute;
  bottom:2rem;
  right:2rem;
}

.todo-add-togglebutton svg {
  height:1.5rem;
  width:1.5rem;
  color:var(--color-white);
  padding:1rem;
  border-radius: 1rem;
  background-color: var(--color-blue-light);
}
.todo-add-togglebutton svg:hover{
  background-color: var(--color-blue);
  cursor: pointer;

}

.todo-add-projectinput{
  position:absolute;
  display:flex;
  height: 80px;
  flex-direction: column;
  justify-content: space-between;
  text-align: left;
  transform: translateY(-2rem);
  animation:spawn;
  animation-duration: 500ms;
  width:25%;
  bottom:0rem;
  left:12.5%;
}

.todo-add-projectinput h3{
  color:white;
}

.todo-add-projectinput-container{
  display:flex;
  justify-content: center;
}

.todo-add-projectinput-container input{
  width: 80%;
  height: 40px;
  border-radius: 1rem;
  color:var(--color-grey-dark);
  background-color: var(--color-blue-light);
  border: none;
  padding-left:1rem;
  box-sizing: border-box;
  
  outline: none;
}

.todo-add-projectinput-container svg{
  width: 40px;
  height: 40px;
  margin:0rem 0.2rem;
  padding: 0.5rem;
  border-radius: 1rem;
  box-sizing: border-box;
  color: var(--color-blue);
  background-color: var(--color-blue-light);
}


.todo-add-projectinput-container svg:hover{
  color:var(--color-blue);
  background-color: var(--color-white);
  cursor: pointer;
}



.todo-add-projectinput-container .delete-project svg:hover{
  color:var(--color-blue);
}

.copyright{
  z-index: 0;
  height:fit-content;
  width:fit-content;
  position: absolute;
  top:0;
  bottom:0;
  left: 0;
  right: 0;
  margin: auto;
  pointer-events: none;
}

.copyright h1{
  background-image:   linear-gradient( 
      white 0%,
      white 50%,
      var(--color-blue) 50%,
      var(--color-blue) 100%
    );
    background-size: 100%;
    -webkit-background-clip: text;
    -moz-background-clip: text;
    -webkit-text-fill-color: transparent; 
    -moz-text-fill-color: transparent;
}

@media screen and (max-width: 750px) {
  .todo{
    flex-direction: column;
  }
  .todo-add{    
    width: 80%;
  }
  .todo-tasks{
  width:calc(100% - 1rem);
  height: 100%
  }
  .todo-search{
      width: calc(100% - 1rem);
      height: 100%;
  }

  .todo-search .container .projects-list{
  grid-template-columns: repeat(auto-fill, minmax(68px, 1fr));
  grid-template-rows: repeat(auto-fill, 1fr);
  gap: 1rem;
  max-height: 300px;
  flex-direction: row;
}

.todo-search .container .projects .project-item span{
  width: 50px;
  height: 50px;
}
.todo-search .container .projects .project-item .delete-project{
  position:absolute;
  top:0px;
  right:0px;
}


.copyright{
    bottom:auto;
}


.copyright h1{
  background-image:   linear-gradient( 
      white 0%,
      white 50%,
      white 50%,
      white 100%
    );
}
  
}


@media screen and (max-width: 480px) {


  .copyright{
    bottom:auto;
  }


  .copyright h1{
    background-image:   linear-gradient( 
        white 0%,
        white 50%,
        white 50%,
        white 100%
      );
  }


  .todo-add{    
    width: 80%;
  }

  .todo-add-form input{
    height: 25px;
  }

  .todo-add-form textarea{
    min-height: 150px;
  }

  .todo-add-form button{
    height: 25px;
  }


  .todo-search .container .welcome input{
  width: 100%;
  padding:10px;
  border-radius: 1rem;
  }
  .todo-search .container .projects-list{
  grid-template-columns: repeat(auto-fill, minmax(60px, 1fr));
  grid-template-rows: repeat(auto-fill, 1fr);
  gap: 1rem;
  max-height: 300px;
  flex-direction: row;
  }

  .todo-search .container .projects .project-item span{
  width: 30px;
  height: 30px;
  }

  .todo-search .container .projects .project-item .delete-project{
    position:absolute;
    top:0px;
    right:0px;
  }

}


@keyframes spawn {
  0% { transform: translateY(0rem); }
  100% { bottom:2rem;transform: translateY(0rem);}
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

</style>
