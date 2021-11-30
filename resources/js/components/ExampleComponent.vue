<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header d-flex justify-content-between">
                        <h5>Contacts</h5>  
                        <div v-if="logged">
                            <button class="btn btn-sm btn-primary" data-toggle="modal" data-target="#exampleModal"><i class="fas fa-plus"></i></button>
                            <button class="btn btn-sm btn-dark" @click="logout"><i class="fas fa-sign-out-alt"></i></button>
                        </div>
                        <div v-if="!logged">
                            <button class="btn btn-sm btn-dark" data-toggle="modal" data-target="#login">Se Connecter</button>
                            <button class="btn btn-sm btn-secondary" data-toggle="modal" data-target="#register">S'inscrire</button>
                        </div>
                    </div>

                    <div class="card-body">
                        <table class="table table-striped text-center">
                            <thead>
                                <tr>
                                    <th scope="col">Nom</th>
                                    <th scope="col">Tel</th>
                                    <th scope="col">Ville</th>
                                    <th v-if="logged" scope="col"></th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="contact in contacts" :key="contact.id">
                                    <td>{{contact.name}}</td>
                                    <td>{{contact.tel}}</td>
                                    <td>{{contact.ville}}</td>
                                    <td v-if="logged">
                                        <button class="btn btn-sm btn-warning mr-1" @click="getContact(contact.id)" data-toggle="modal" data-target="#exampleModal"><i class="fas fa-user-edit"></i></button>
                                        <button class="btn btn-sm btn-danger" @click="deleteContact(contact.id)"><i class="fas fa-trash-alt"></i></button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
        <Login @logged='login' :loginModal="true"/>
        <Login @logged='login' :loginModal="false"/>
        <!-- Modal -->
        <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="exampleModalLabel">Ajouter contact</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body">
                    
                        <div class="form-group">
                            <label for="inp1">Nom</label>
                            <input v-model="contact.name" type="text" class="form-control" id="inp1">
                        </div>
                        <div class="form-group">
                            <label for="inp2">Tel</label>
                            <input v-model="contact.tel" type="text" class="form-control" id="inp2">
                        </div>
                        <div class="form-group">
                            <label for="inp">Ville</label>
                            <input v-model="contact.ville" type="text" class="form-control" id="inp">
                        </div>
                    
                </div>
                <div class="modal-footer">
                    <button @click="resetUpdate" type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                    <button v-if="!updating" @click="addContact" class="btn btn-primary" data-dismiss="modal">Ajouter</button>
                    <button v-else @click="updateContact(contact.id)" class="btn btn-warning" data-dismiss="modal">Modifier</button>
                </div>
                </div>
            </div>
        </div>
        
    </div>
</template>

<script>
    import Login from './Login.vue'
    export default {
        data(){
            return{
                contacts:[],
                contact:{
                    name:'',
                    tel:'',
                    ville:'',
                },
                updating:false,
                logged:JSON.parse(localStorage.getItem('token'))?true:false,
                token:JSON.parse(localStorage.getItem('token'))||''
                
            }
        },
        components:{
            Login
        },
        methods:{
            getContacts(){
                axios.get('/api/contacts')
                .then(res=>{
                    this.contacts=res.data.data
                })
                .catch(err=>console.log(err));
            },
            addContact(){
                const config={
                    headers:{
                        Authorization:"Bearer "+this.token
                    }
                }
                console.log(config)
                axios.post('/api/contacts',this.contact,config)
                .then(res=>{
                    this.getContacts()
                })
                .catch(err=>console.log(err));
            },
            updateContact(id){
                const config={
                    headers:{
                        Authorization:"Bearer "+this.token
                    }
                }
                axios.put('/api/contacts/'+id,this.contact,config)
                .then(res=>{
                    this.getContacts()
                    this.contact={name:'',tel:"",ville:""}
                    this.updating=false
                })
                .catch(err=>console.log(err));
            },
            getContact(id){
                const config={
                    headers:{
                        Authorization:"Bearer "+this.token
                    }
                }
                this.updating=true
                axios.get('/api/contacts/'+id,config)
                .then(res=>{
                    this.contact=res.data.data
                })
                .catch(err=>console.log(err));
            },
            deleteContact(id){
                const config={
                    headers:{
                        Authorization:"Bearer "+this.token
                    }
                }
                Swal.fire({
                    title: 'Are you sure?',
                    text: "You won't be able to revert this!",
                    icon: 'warning',
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    confirmButtonText: 'Yes, delete it!'
                    }).then((result) => {
                    if (result.isConfirmed) {
                        Swal.fire(
                            axios.delete('/api/contacts/'+id,config)
                                .then(res=>{
                                    this.getContacts()
                                })
                                .catch(err=>console.log(err))
                        )
                    }
                })
                
            },
            logout(){
                localStorage.removeItem('token')
                this.logged=false
                this.token=''
            },
            login(){
                this.logged=true
                this.token=JSON.parse(localStorage.getItem('token'))
            },
            resetUpdate(){
                this.contact={name:'',tel:"",ville:""}
                this.updating=false
            }
        },
        mounted() {
            this.getContacts()
        }
    }
</script>
