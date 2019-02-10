<template>
  <div>
    <!-- As a link -->
    <nav class="nav navbar navbar-dark bg-primary">
      <a class="navbar-brand" href="#">PetStore</a>
    </nav>

      <div class="login container">
        <div class="row">
            <div class="col col-md-6 offset-md-3">

                <div class="card text-white mt-4">
                  <div class="card-header">
                    <h2 class="card-title mt-4">Log In</h2>
                  </div>
                  <div class="card-body">
                    <form v-on:submit.prevent="onSubmit">
                      <div v-if="error" class="alert alert-danger" role="alert">
                        {{ error }}
                      </div>
                      <div class="form-group">
                        <label for="InputEmail1">Email address</label>
                        <input type="email" class="form-control" v-model=email id="exampleInputEmail1" required="required" aria-describedby="emailHelp" placeholder="Enter email">
                      </div>
                      <div class="form-group">
                        <label for="InputPassword">Password</label>
                        <input type="password" class="form-control" v-model=password id="InputPassword" required="required" placeholder="Password">
                      </div>
                      <button type="submit" class="btn btn-primary">Submit</button>
                    </form>
                  </div>
                </div>
            </div>
        </div>
      </div>
  </div>
</template>

<script>

import axios from 'axios';
import router from '../router'

export default {
    data: function() {
        return {
            email: '',
            password: '',
            error: ''
          }
    },
    created () {
        // reset login status
        this.logout();
    },
    methods: {
      onSubmit: function() {
        this.error = '';
        const headers = {
            'Content-Type': 'application/json',
        }

        const data = {
            'email': this.email,
            'password' : this.password
        }

        axios.post('https://petshop.systemic.earth/api/v1/login', data, {headers: headers} ).then( response => {
            // Save Token to local storage
            localStorage.setItem('token', response.data.token);
            // push router change to home
            router.push({ path: 'home' });
        }).catch(error => {
            this.error = error.response.data.message;
        });
      },
      logout: function() {
        localStorage.removeItem('token');
      }
    }
}


</script>