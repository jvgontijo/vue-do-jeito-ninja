<template>
    <div class="container">
        <h1>Vue.js + Github</h1>
        <p class="lead">
            Página que lista issues de um repositório do Github, usando Vue.js.
        </p>

        <div class="row">
            <div class="col">
                <div class="form-group">
                    <input v-model="username" type="text" class="form-control" placeholder="github username">
                </div>
            </div>

            <div class="col">
                <div class="form-group">
                    <input v-model="repository" type="text" class="form-control" placeholder="github repositório">
                </div>
            </div>

            <div class="col-3">
                <div class="form-group">
                    <button @click.prevent.stop="getIssues()" class="btn btn-success">GO</button>
                    <button @click.prevent.stop="reset()" class="btn btn-danger">LIMPAR</button>
                </div>
            </div>
        </div>

        <br><hr><br>

        <table class="table table-sm table-bordered">
            <thead>
            <tr>
                <th width="100">Número</th>
                <th>Título</th>
            </tr>
            </thead>

            <tbody>
                <tr v-if="loader.getIssues">
                <td colspan="2">
                        <div class="text-center">
                        <div class="spinner-border" role="status">
                                <span class="sr-only"></span>
                        </div>
                        </div>
                    </td>
                </tr>

                <tr v-if="issues.length && !loader.getIssues" 
                v-for="issue in issues" :key="issue.number">
                    <td> 
                        <router-link :to="{name: 'GitHubIssue', params: { name: username, repo: repository, issue: issue.number}}">{{issue.number}}</router-link>
                        
                    </td>
                    <td>{{issue.title}}</td>
                </tr>
                <tr v-if="!issues.length && !loader.getIssues">
                    <td class="text-center" colspan="2">Nenhuma issues encontrada!</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>

import axios from "axios";

export default {
    name: 'GitHubIssues',
    data(){
        return{
            username: '',
            repository: '',
            issues: [],
            loader: {
                getIssues: false,
                getIssue: false
            }
        };
    },
    created() {
        this.getLocalData();
    },
    methods:{
        reset(){
           this.username = '';
           this.repository = ''; 
        },
        getIssues(){
            if(this.username && this.repository){
                localStorage.setItem('gitHubIssues', JSON.stringify({username: this.username, repository: this.repository}));
                this.loader.getIssues = true;
                const url = `https://api.github.com/repos/${this.username}/${this.repository}/issues`;
                axios.get(url).then((response) => {
                    this.issues = response.data;
                }).finally(() => {
                    this.loader.getIssues = false;
                });
            }
        },
        getLocalData(){    
            const localData = JSON.parse(localStorage.getItem('gitHubIssues'));
            if(localData.username && localData.repository){
                this.username = localData.username;
                this.repository = localData.repository;
                this.getIssues();
            }
        }
    },
}
</script>

<style>

</style>
