<template>
  <div style="height:100%; width: 100%; position:absolute; background-color: #302f2d;">
    <div class="content">

    <div class="container-fluid">  
      <div class="row">
        </div>
            <div class="row">
              <div class = "col">
                <h2 style="font:bolder !important; font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif; color:white">My reports</h2>
              </div>

              <div v-if="!projectDeletion">
                  <div style="right:0 !important; margin: 30px;position: absolute;">
                    <button v-on:click="projectDeletion = true" class="delete-btn">Delete projects</button>
                  </div>
              </div>
              <div v-else>
                <div style="right:0 !important; margin: 30px;position: absolute;">
                    <button v-on:click="projectDeletion = false" class="delete-btn">Done?</button>
                </div>
              </div>

          </div>

        <div class="row">
          <div class = col-md-4>
            <stats-card  style="max-width: 600px; max-height: 600px;">
              <div slot="header" class="icon-success">
                <h4 class="card-title" > <b>New project</b></h4>
              </div>

              <div slot="content">
                <a class="button_plus" href="#project-create"></a>
              </div>

              <div slot = "footer">
              </div>
            </stats-card>
          </div>

          <div id="project-create" class="modal">
                <div class="modal__content" style = "background-color: #302f2d;">
                  <input v-model="projectTitle" placeholder = "Project title" class = "input_small"/>
                  <br/>
                  <input v-model="projectDescription" placeholder = "Project description" class = "input_small"/>
                  <br/>
                  <button class="button-9" role="button" v-on:click="createProject(false)" style = "background-color: !important;">Create</button>
                  <br/>
                  <h4 style = "font-family: system-ui !important; color:white" > or use a ready template for your next document</h4>
                  
                  <div class = "row">
                    <div class = "col-md-6" v-for="(item,index) in templates" :key="index" v-on:click="openProject(item.id)">
                        <stats-card  class = "projectCard" style="max-width: 600px; max-height: 600px;
                          align-items: center !important; align-content: center !important; text-align: center !important;
                          ">

                          <div slot="footer">
                            <p style = "font-weight: bold; color:black !important">{{ item.templateName }}</p>
   
                          </div>

                      </stats-card>
                    </div>
                  </div>

                  <h4 style = "font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;color: white;"> Want to add your own? Upload your md file here:</h4>
                  <label for="file-upload" class="custom-file-upload" style = "color: white;">
                      Upload Template
                  </label>
                  <input v-on:change="onFileChange" id="file-upload" type="file"/>
                  

                  <br>
                  <div class="modal__footer">
                    
                  </div>


                      <a href="#" class="modal__close">&times;</a>
                  </div>
          </div>

          <div class = "col-md-4" v-for="(item,index) in items" :key="index" v-on:click="openProject(item.id)">
                <div v-if="!projectDeletion">
                    <stats-card  class = "projectCard" style="max-width: 600px; max-height: 600px;">
                      <div slot="header" class="icon-success">
                        <h4 class="card-title">{{ item.projectName }}</h4>
                      </div>
                      <div slot="content">
                        <p style = "color:black">{{ item.projectDescription }}</p>
                        <!-- <button v-on:click="openProject(item.listing_name)"></button> -->
                      </div>
                      <div slot = "footer">

                          <div class = "updateTime">
                          <i class="fa fa-history"></i> {{ calculateLastUpdated(item.lastUpdated) }}
                          </div>

                        
                      </div>
                    </stats-card>            
                </div>
                <div v-else>
                  <stats-card  style="max-width: 600px; max-height: 600px;">
                      <div slot="header" class="icon-success">
                        <h4 class="card-title">{{ item.projectName }}</h4>
                      </div>
                      <div slot="content">
                        <button class = "projectDelBtn"
                        @focus="handleFocus()"
                        @focusout="handleFocusOut()"
                        tabindex="-1"
                        >
                          <div>
                            <span class = "spanDel" style = "font-size: 10 !important; text-align: center !important;" v-on:click="deleteProject(item.id)">Confirm</span>
                            <svg xmlns="http://www.w3.org/2000/svg" width="25" height="25" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                            </svg>

                          </div>

                        </button>
                      </div>
                      <div slot = "footer">

                          <div class = "updateTime">
                          <i class="fa fa-history"></i> Updated 3 minutes ago
                          </div>

                        
                      </div>
                    </stats-card>
                </div>

          </div>
        </div>

      </div>
    </div>

  </div>
  
</template>
<script>
  import ChartCard from 'src/components/Cards/ChartCard.vue'
  import StatsCard from 'src/components/Cards/StatsCard.vue'
  import LTable from 'src/components/Table.vue'
  import axios from 'axios';
  import SidepanelComponent from '../components/SidepanelComponent.vue';
  import { ref } from "vue";
  export default {
    components: {
      LTable,
      ChartCard,
      StatsCard,
      SidepanelComponent
    },
    data () {
      return {
        editTooltip: 'Edit Task',
        deleteTooltip: 'Remove',
        hovered: false,
        items: [],
        projectTitle: "",
        projectDescription: "",
        createdProject: "",
        templates: [{templateName: "Code4rena Report Style", templateContent: "<h4> Remove all the bugs! </h4>"},
                    {templateName: "Sherloc Report Style", templateContent: "<h4> Remove all the bugs! </h4>"},
                   ],
        fileToUpload: "",
        projectDeletion: false,
        projectDeletionConfirmation: false,
        projectDeletionConfirmCount:0,
        lastUpdatedTime: ""
      }
    },
    async mounted(){
      await this.fetchAllProjects();
    },
    methods: {
      async createProject(withTemplate){
        try {
          let project = {"projectName":this.projectTitle, "projectDescription":this.projectDescription,"projectCreator":""}
          const response = await axios.post('https://copycloud.work/api/createproject', project, { withCredentials: true, headers: { Accept: '*/*' } });
          this.createdProject = response.data;
          var baseUrl = window.location.origin;

          if (!withTemplate) {
            window.location = baseUrl+`/project/${this.createdProject}`
          }
        }
        catch {
          console.log("Error creating a project");

        }
      },
      calculateLastUpdated(lastUpdatedDateTime) {
        var d = new Date();

        let dateToString = lastUpdatedDateTime.toString()
        const date = new Date(dateToString);

        const differenceInMilliseconds = Math.abs(d - date); // Absolute difference to handle negative values
        const millisecondsInSecond = 1000;
        const millisecondsInMinute = millisecondsInSecond * 60;
        const millisecondsInHour = millisecondsInMinute * 60;
        const millisecondsInDay = millisecondsInHour * 24;

        const days = Math.floor(differenceInMilliseconds / millisecondsInDay);
        const hours = Math.floor((differenceInMilliseconds % millisecondsInDay) / millisecondsInHour);
        const minutes = Math.floor((differenceInMilliseconds % millisecondsInHour) / millisecondsInMinute);
        const seconds = Math.floor((differenceInMilliseconds % millisecondsInMinute) / millisecondsInSecond);
        console.log(`Difference: ${days} days, ${hours} hours, ${minutes} minutes, ${seconds} seconds`);

        let updateTime = ""

        if (days > 0) {
          updateTime = "Last updated: " + days + " days ago"
          return updateTime;
        }
        else if (hours > 0){
          updateTime = "Last updated: " + hours + " hours ago"
          return updateTime;
        }
        else if (minutes > 0){
          updateTime = "Last updated: " + minutes + " minutes ago"
          return updateTime;
        }
        else if (seconds > 0) {
          updateTime = "Last updated: " + seconds + " seconds ago"
          return updateTime;
        }
      },
      async fetchAllProjects() {
        let data = []
        
        try {
          let projects = await axios.get("https://copycloud.work/api/getallprojects",{ withCredentials: true});
          this.items = projects.data;
          console.log(this.items)
        }
        catch {
          console.log("There was an error loading the projects")
          var baseUrl = window.location.origin;
          window.location = baseUrl+`/auth/`
        }
      },
      openProject(selectedproject){

        if (!this.projectDeletion) {
          console.log("Selected project")
          console.log(this.items)
          this.$router.push({ path: `/project/${selectedproject}`})
          // this.$router.push(`/app/project/${selectedproject}`)
        }

      },
      async deleteProject(item) {
        if (this.projectDeletionConfirmation && this.projectDeletionConfirmCount === 1) {
          console.log(item);
          const response = await axios.post('https://copycloud.work/api/deleteproject?projectId='+ item, {}, { withCredentials: true, headers: { Accept: '*/*' } });

          console.log(response.data);
          if (response.data) {
            this.projectDeletionConfirmCount = 0;
            this.fetchAllProjects();
          }

        }
        else {
          this.projectDeletionConfirmCount++;
        }

      },
      handleFocus() {
        this.projectDeletionConfirmation = true;
      },
      handleFocusOut(){
        this.projectDeletionConfirmCount = 0;
        this.projectDeletionConfirmation = false;
      },
      async createProjectWithTemplate() {
        if (this.projectTitle === "") {
          console.log("Project title can't be null");
          return;
        }
        console.log("broken0")
        const FormData = require('form-data');
        
        console.log("broken")
        const uploadData = new FormData();
        uploadData.append('ProjectName', this.projectTitle);
        uploadData.append('ProjectDescription', this.projectDescription);
        uploadData.append('ProjectCreator', "remove");
        uploadData.append('Template', this.fileToUpload);

        console.log("broken2")
        let response = await axios.post('https://copycloud.work/api/createprojectwithtemplate', uploadData, { withCredentials: true, headers: { 'Content-Type': 'multipart/form-data' } });
        
        console.log(response.data + "is response")

        if (response.data.item1) {
          this.$router.push({ path: `/project/${response.data.item2}`})  
        }

      },
      async onFileChange(e) {
        const selectedFile = e.target.files[0]; // accessing file
        this.fileToUpload = selectedFile;
        console.log(this.fileToUpload)
        this.createProjectWithTemplate();

      },
    }
  }
</script>
<style>
    .projectDelBtn {
      position: relative;
      width: 50px;
      height: 50px;
      border-radius: 25px;
      border: 2px solid rgb(231, 50, 50);
      background-color: #fff;
      cursor: pointer;
      box-shadow: 0 0 10px #333;
      overflow: hidden;
      transition: .3s;
      text-align: center !important;
      align-content:center !important;
      align-items:center !important;

  }

  .spanDel {
    position:absolute;
    top:50%;
    left:50%;
    transform: translate(-50%, -50%);
  }

  .projectDelBtn:hover {
    background-color: rgb(245, 207, 207);
    transform: scale(1.2);
    box-shadow: 0 0 4px #111;
    transition: .3s;
  }

  .projectDelBtn svg {
    color: rgb(231, 50, 50);
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    transition: .3s;
  }

  .projectDelBtn:focus svg {
    opacity: 0;
    transition: .3s;
  }

  .projectDelBtn span {
    position: absolute;
    opacity: 0;
    transform: translate(-50%, -50%);
    color: rgb(231, 50, 50);
    font-weight: 600;
    transition: .3s;  
    font-size: 15px !important;
  }

  .projectDelBtn:focus {
    width: 100px;
    height: 30px;
    transition: .3s;
  }
  .projectDelBtn:focus span {
    opacity: 1;
    transition: .3s;
  }

    .delete-btn {
      background-color: #3d3e3f;
      border: none;
      border-radius: 3px;
      box-shadow: 0 -3px 0 rgba(0, 0, 0, 0.15) inset;
      color: #fff;
      letter-spacing: 0.1em;
      text-align: center;
      text-transform: uppercase;
      vertical-align: middle;
      white-space: nowrap;
      
      font-family: "Futura PT", "Futura", sans-serif;
      height: 50px;
      width: 200px;
      margin:1px;
      display:flex;
      justify-content: center;
      align-items: center;
    }


    ;;;;;;;;
    input[type="file"] {
        display: none;
    }

    .custom-file-upload {
      border: 1px solid #ccc !important;
      display: inline-block !important;
      padding: 6px 12px !important; 
      cursor: pointer !important;
    }

    .button-9{  padding: 1rem 3rem;
    text-align: center;
    font-size: 16px;
    text-transform: uppercase;
    cursor: pointer;
    background: linear-gradient(90deg, #288eec 0%, rgba(229, 20, 247, 0.63) 100%);
    border-radius: 53px;
    border: none;
    color: #fff;
    font-weight: bold;
    letter-spacing: 1px;}

    .button_plus {
    position: absolute;
    width: 35px;
    height: 35px;
    background: #fff;
    cursor: pointer;
    border: 2px solid #095776;

    /* Mittig */
    top: 50%;
    left: 50%;
  }

.button_plus:after {
  content: '';
  position: absolute;
  transform: translate(-50%, -50%);
  height: 4px;
  width: 50%;
  background: #095776;
  top: 50%;
  left: 50%;
}

.button_plus:before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background: #095776;
  height: 50%;
  width: 4px;
}

.button_plus:hover:before,
.button_plus:hover:after {
  background: #fff;
  transition: 0.2s;
}

.button_plus:hover {
  background-color: #095776;
  transition: 0.2s;
}

;;;;

.projectCard:after {
  content: 'Open';
  position: absolute;
  transform: translate(-50%, -50%);
  height: 4px;
  width: 50%;
  background: #8bbabd;
  top: 50%;
  left: 50%;
}

.projectCard:before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  height: 100%;
  width: 100%;
}

.projectCard:hover:before,
.projectCard:hover:after {
  background: #6f7676;
  transition: 0.2s;
}

.projectCard:hover {
  background-color: #095776;
  transition: 0.2s;
}

.updateTime {
  color: black;
}

/* If you like this, be sure to ❤️ it. */
.wrapper-modal {
  height: 100vh;
  /* This part is important for centering the content */
  display: flex;
  align-items: center;
  justify-content: center;
  /* End center */
  background: -webkit-linear-gradient(to right, #834d9b, #d04ed6);
  background: linear-gradient(to right, #834d9b, #d04ed6);
}

.wrapper-modal a {
  display: inline-block;
  text-decoration: none;
  padding: 15px;
  background-color: #fff;
  border-radius: 3px;
  text-transform: uppercase;
  color: #585858;
  font-family: 'Roboto', sans-serif;
}

.modal {
  visibility: hidden;
  opacity: 0;
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(77, 77, 77, .7);
  transition: all .4s;
}

.modal:target {
  visibility: visible;
  opacity: 1;
}

.modal__content {
  border-radius: 4px;
  position: relative;
  width: 700px !important;
  height: 700px !important;
  background: #fff;
  padding: 1em 2em;
}

.modal__footer {
  text-align: left !important;
}

.modal__footer i {
  color: #d02d2c;
}

.modal__footer a {
  color: #585858;
}
.modal__close {
  position: absolute;
  top: 10px;
  right: 10px;
  color: #585858;
  text-decoration: none;
}

.input_small {
	 display: inline-block;
	 border: none;
	 padding: 0;
	 width: 38ch;
	 background: repeating-linear-gradient(90deg, dimgrey 0, dimgrey 1ch, transparent 0, transparent 0ch) 0 100%/ 58ch 1px no-repeat;
	 font: 2.4ch droid sans mono, consolas, monospace;
	 letter-spacing: 0.15ch;
   font-family: system-ui !important;
}



  p {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }

  h1 {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }

  h2 {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }

  h3 {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }

  h4 {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }


</style>
