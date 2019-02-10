<template>
  <div class="home">

    <nav class="nav navbar navbar-dark bg-primary">
      <a class="navbar-brand" href="#">PetStore</a>
      <ul class="navbar-nav">
        <li class="nav-item">
            <router-link class="nav-link" to="/login">Logout</router-link>
        </li>
      </ul>
    </nav>

    <div class="container">

        <div v-if='flash' class="alert alert-info mt-4" role="alert">
            {{ flash }}
        </div>

        <div class="row mt-4">

            <div class="col-12 col-md-8">

                <div class="row">
                    <div v-for="(pet, index) in pets" v-bind:key="pet.id" class="col col-md-4">
                        <div class="card mb-4 text-white">
                          <img v-bind:src="'https://loremflickr.com/200/200/' + pet.type.toLowerCase() + '?random=' + pet.id" class="card-img-top" alt="...">
                          <div class="card-body">
                            <h5 class="card-title">{{ pet.name }}</h5>
                            <p class="card-text">{{ pet.type }}</p>
                            <p class="card-text">£{{ pet.price }}</p>
                            <button v-on:click="remove(index)" type="button" class="btn btn-danger">Remove</button>
                          </div>
                        </div>
                    </div>
                </div>

            </div>

            <div class="col-12 col-md-4">

                <div class="card text-white mb-4">

                    <div class="card-header">
                        <h4 class="card-title mt-3">Add a New Pet</h4>
                    </div>
                    <div class="card-body">

                        <form v-on:submit.prevent="onSubmit">
                            <div v-if="errors">
                                <div v-for="value in errors" v-bind:key='value' class="alert alert-danger" role="alert">
                                    {{ value[0] }}
                                </div>
                            </div>

                          <div class="form-group">
                            <label for="inputType">Type</label>
                            <input type="text" required  v-model=pet.type class="form-control" id="inputType">
                            <small id="emailHelp" class="form-text text-muted">Example Dog, Cat, etc</small>
                          </div>
                          <div class="form-group">
                            <label for="inputName">Name</label>
                            <input type="text" required v-model=pet.name class="form-control" id="inputName">
                          </div>
                          <div class="form-group">
                            <label for="inputPrice">Price</label>
                            <input type="number" required v-model=pet.price class="form-control" id="inputPrice">
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
// @ is an alias to /src
import axios from 'axios';
import router from '../router'

export default {
    data: function() {
        return {
            pet: {type: "", name: "", price: ""},
            pets: null,
            flash: null,
            errors: null
          }
    },
    mounted: function() {
      axios.get('https://petshop.systemic.earth/api/v1/pets', {
        params: {
          token: localStorage.getItem('token')
        }
      }).then(
            response => (this.pets = response.data )
        );
    },
    methods: {
        onSubmit: function() {
            this.flash = null;
            const headers = {
                'Content-Type': 'application/json',
            }

            const data = {
                'type': this.pet.type,
                'name' : this.pet.name,
                'price' : this.pet.price
            }

            axios.post('https://petshop.systemic.earth/api/v1/pets/', data, {headers: headers, params: {
              token: localStorage.getItem('token')
            }}).then(
                response => {
                    this.flash = this.pet.name + " has been added"
                    this.pets.push(response.data);
                    this.pet = {type: "", name: "", price: ""}
                }
            ).catch(error => {
                if (error.response.data.error) {
                    router.push({ path: 'login' });
                }
                this.errors = error.response.data.errors;
            });
        },
        remove: function(index) {
            this.flash = null;
            const URL = "https://petshop.systemic.earth/api/v1/pets/" + this.pets[index].id;
            axios.delete(URL , { params: {
              token: localStorage.getItem('token')
            }}).then(
                () => {
                    this.flash = this.pets[index].name + " has been Removed!";
                    this.pets.splice(index, 1)
                }
            )
        }
    },
    name: 'home',
}
</script>

<style scoped>
    .form-group {
        text-align: left;
    }
</style>
