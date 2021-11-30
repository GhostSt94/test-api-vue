<template>
  <!-- Modal -->
        <div class="modal fade" :id="loginModal?'login':'register'" tabindex="-1" aria-labelledby="label" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content">
                <div class="modal-header">
                    <h5 v-if="loginModal" class="modal-title" id="label">Se Connecter</h5>
                    <h5 v-else class="modal-title" id="label">S'inscrire</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body">
                    <div v-if="message!==''" class="text-center alert alert-warning">
                        {{message}}
                    </div>
                    <div class="form-group">
                        <label>Email </label>
                        <input v-model="user.email" type="email" class="form-control">
                    </div>
                    <div v-if="!loginModal" class="form-group">
                        <label>Name </label>
                        <input v-model="user.name" type="text" class="form-control">
                    </div>
                    <div class="form-group">
                        <label>Password</label>
                        <input v-model="user.password" type="password" class="form-control">
                    </div>
                </div>
                <div class="modal-footer">
                    <button v-if="loginModal" @click="login" class="btn btn-dark">Login</button>
                    <button v-else @click="register" class="btn btn-secondary">S'inscrire</button>
                </div>
                </div>
            </div>
        </div>
</template>

<script>
export default {
    name:'Login',
    props:{
        loginModal:{
            type:Boolean,
            required:true
        },
    },
    data(){
        return{
            user:{email:'',name:'',password:''},
            message:''
        }
    },
    methods:{
        login(){
            axios.post('/api/login',this.user)
                .then(res=>{
                    if(res.data.token){
                        this.message=res.data.message
                        this.user={email:'',password:''}
                        localStorage.setItem('token',JSON.stringify(res.data.token))
                        this.$emit('logged')
                        setTimeout(()=>{
                            this.message=''
                        },2000)
                    }else{
                        this.message=res.data.message
                        this.user={email:'',password:''}
                        setTimeout(()=>{
                            this.message=''
                        },2000)
                    }
                })
                .catch(err=>console.log(err))
        },
        register(){
            axios.post('/api/register',this.user)
                .then(res=>{
                    this.message='Inscription effectuer avec succés'
                    this.user={email:'',name:'',password:''},
                    setTimeout(()=>{
                        this.message=''
                    },2000)
                })
                .catch(err=>console.log(err))
        }
    }
}
</script>

<style>

</style>