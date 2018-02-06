<template>
    <div class="container">    
        <h1>Menu</h1>   
        <table class="table" >   
            <thead>
                <tr>        
                    <th scope="col">Aliment</th>
                    <th scope="col">Calorii</th>
                    <th scope="col">Proteine</th>
                    <th scope="col">Carbohidrati</th>
                    <th scope="col">Fibre</th>
                    <th scope="col">Grame</th>
                </tr>
            </thead>
            <!--<transition-group name="fade" tag="tbody">-->
                <tr  v-for="ingredient in ingredientsMenu" :key="ingredient.index">
                    <td>{{ ingredient.nume }}</td>
                    <td>{{ ingredient.totalCalorii}}</td>
                    <td>{{ ingredient.totalProteine }}</td>
                    <td>{{ ingredient.totalCarbohidrati}}</td>
                    <td>{{ ingredient.totalFibre }}</td>  
                    <td>{{ ingredient.totalGrame }} g</td> 
                </tr>

            <!--</transition-group>-->
            <br>
            <tbody>
                <tr>
                    <td>Total</td>
                    <td>{{ totalCalorii }}</td>
                    <td>{{ totalProteine }}</td>
                    <td>{{ totalCarbohidrati }}</td>
                    <td>{{ totalFibre }}</td>  
                    <td>{{ totalGrame }}g</td> 
                </tr>
            </tbody>
            
        </table>
        <h1>Alimente</h1>
        <div class="input-group input-group-lg">
            <div class="input-group-prepend">
                <span class="input-group-text" id="inputGroup-sizing-lg">Cauta un aliment</span>
            </div>
            <input  v-model="getQuestion" type="text" class="form-control" aria-label="Large" aria-describedby="inputGroup-sizing-sm">
        </div>
        <div class="filter row">
            <div class="col col-lg-3">
                <div class="filter_calorii input-group mb-3">
                    <div class="input-group-prepend">
                        <label class="input-group-text" for="inputGroupSelect01"> Items on page:</label>
                    </div>
                    <select class="custom-select custon-width" v-model="changeLimit">
                        <option v-for="val in values" :value="val">{{ val }}</option>
                    </select>
                </div>
            </div>
            <div class="col col-lg-3">
                <div class="filter_calorii input-group mb-3">
                    <div class="input-group-prepend">
                        <label class="input-group-text" for="inputGroupSelect01">Sort by:</label>
                    </div>
                    <select class="custom-select custon-width" id="inputGroupSelect01" v-model="changeSort">
                        <option v-for="sortName in sortNames" :value="sortName">{{ sortName }}</option>
                    </select>
                </div>
            </div>
        </div>
   
        <table class="table table-hover">
            <thead>
                <tr>                 
                    <th scope="col">Aliment</th>
                    <th scope="col">Calorii</th>
                    <th scope="col">Proteine</th>
                    <th scope="col">Carbohidrati</th>
                    <th scope="col">Fibre</th>
                    <th scope="col">Grame</th>
                    <th scope="col">Cantitate/g</th>
                </tr>    
            </thead>

            <tbody>
                <tr v-bind:data-id="ingredient._id" v-for="(ingredient, index) in ingredients" :key="ingredient._id">
                    <td>{{ ingredient.nume }}</td>
                    <td>{{ ingredient.totalCalorii}}</td>
                    <td>{{ ingredient.totalProteine }}</td>
                    <td>{{ ingredient.totalCarbohidrati}}</td>
                    <td>{{ ingredient.totalFibre }}</td>  
                    <td>{{ ingredient.totalGrame }} g</td>
                    <td>
                        <div class="input-group mb-3 input-fix inpuut">
                            <input  type="number" class="form-control" aria-describedby="basic-addon1"
                                    v-model="ingredient.grame"
                                    @change="calculate(index)" >
                            <br>
                            <button class="btn" 
                                    @click="addMenu(ingredient)" 
                                    @click.once="show = !show"><strong>+</strong>
                            </button>
                        </div>    
                    </td>  
                </tr>
            </tbody>
        </table>
        <nav class="myPagination" aria-label="pagination">
            <ul class="pagination justify-content-center">

                <li class="page-item">
                    <a href="#" class="page-link" aria-label="Previous"
                        v-if="pagination.current_page > 1"
                        @click.prevent="changePagePrev">
                        <span aria-hidden="true">&laquo;</span>
                        <span class="sr-only">Previous</span>
                    </a>
                </li>
                <li class="page-item" 
                        v-bind:key="page"
                        v-for="page in pagination.page_number" 
                        v-if="Math.abs(page - pagination.current_page) < 5 || page == pagination.page_number || page == 1">
                    <a class="page-link" 
                            v-bind:key="page"  
                            @click.prevent="changePage(page)" 
                            :class="{ current: pagination.current_page === page, 
                                    last: (page == pagination.page_number && Math.abs(page - pagination.current_page) > 3), 
                                    first: (page == 1 && Math.abs(page - pagination.current_page) > 3) }" 
                            href="#">{{ page }}
                    </a>
                </li>

                <li class="page-item">
                  <a href="#" class="page-link" aria-label="Next"
                    v-if="pagination.current_page < pagination.page_number"
                    @click.prevent="changePageNext">
                    <span aria-hidden="true">&raquo;</span>
                    <span class="sr-only">Next</span>
                  </a>
                </li>
            </ul>
        </nav>
    </div>
</template>

<script>
    import {bus} from '../main';
    import axios from 'axios';


    export default {
        data() {
            return {
                show: true,
                pagination: {
                    total: 0,
                    per_page: 50,
                    current_page: 1,
                    page_number: [],
                    searchString:'',
                    sort: 'calorii'                
                },              
                values: [10, 25, 50, 75, 100],
                sortNames: ['-calorii', 'calorii'],
                index: '',
                getQuestion: '',
                ingredients: [],
                ingredientsMenu: [],
                grame: 100,
                grameIngredient: []
            }
        },
        created() {
            var _this = this                                                                                                                                                                                                                                                    
                axios.post('http://localhost:8081/ingredients/count') // get all ingredients
                .then(function (response) {
                    _this.pagination.total = response.data.count
                    _this.pagination.page_number = Math.ceil(_this.pagination.total / _this.pagination.per_page) //total number of pages = all ingredients / nr of ing. per page
                })

                var params = new URLSearchParams();
                params.append('limit', this.pagination.per_page);
                axios.post('http://localhost:8081/ingredients/get/1', params, { // get first page
                     "Access-Control-Allow-Origin": "*",
                })
                .then(function (response) {
                    _this.ingredients = [];
                
                    for(var i = 0; i < response.data.data.length; i++) {
                        var ingredient;
                        ingredient = response.data.data[i];
                        ingredient.grame = _this.grame;
                        ingredient.totalCalorii = ingredient.calorii
                        ingredient.totalProteine = ingredient.proteine
                        ingredient.totalCarbohidrati = ingredient.carbohidrati
                        ingredient.totalFibre = ingredient.fibre
                        ingredient.totalGrame = ingredient.grame
                        _this.ingredients.push(ingredient);
                    }
                })
                .catch(function (error) {                                               
                  console.log(error);
                });
        },  
        computed: {   
            totalCalorii: function() {
                return this.ingredientsMenu.reduce((total, ingredient) => {
                    return total + ingredient.totalCalorii;
                    }, 0);       
            },
            totalProteine: function() {
                return this.ingredientsMenu.reduce((total, ingredient) => {
                    return total + ingredient.totalProteine;
                    }, 0);       
            },
            totalCarbohidrati: function() {
                return this.ingredientsMenu.reduce((total, ingredient) => {
                    return total + ingredient.totalCarbohidrati;
                    }, 0);       
            },
            totalFibre: function() {
                return this.ingredientsMenu.reduce((total, ingredient) => {
                    return total + ingredient.totalFibre;
                    }, 0);       
            },
            totalGrame: function() {
                return this.ingredientsMenu.reduce((total, ingredient) => {
                    return total + ingredient.totalGrame;
                    }, 0);       
            },
            changeLimit: {
                get: function() { return this.pagination.per_page; },
                set: function(value) {
                    this.pagination.per_page = value;
                    this.changeLimitPerPage()
                }     
            },
            changeSort: {
                get: function() { return this.pagination.sort; },
                set: function(value) {
                    this.pagination.sort = value;
                    this.changeLimitPerPage()
                } 
            } 
        },
        methods: {
            calculate(index) {
                //alert(index)
                //const index2 = this.ingredients.indexOf(index); //Instead of using the index from the filtered ingredients,use the index from the ingredients list
                console.log(this.ingredients[index].calorii);
                this.ingredients[index].totalCalorii = Math.round((this.ingredients[index].grame * this.ingredients[index].calorii) / 100);    
                this.ingredients[index].totalProteine = Math.round((this.ingredients[index].grame * this.ingredients[index].proteine) / 100) ;
                this.ingredients[index].totalCarbohidrati = Math.round((this.ingredients[index].grame * this.ingredients[index].carbohidrati) / 100) ;
                this.ingredients[index].totalFibre = Math.round((this.ingredients[index].grame * this.ingredients[index].fibre) / 100) ;
                this.ingredients[index].totalGrame = Math.round((this.ingredients[index].grame * this.ingredients[index].grame) / 100) ;   
                                
            },
            addMenu(ingredient) {
                const index = this.ingredients.indexOf(ingredient);
                this.ingredientsMenu.push(this.ingredients[index]);
                console.log(this.ingredientsMenu);    
            },
            changeLimitPerPage() {
                var params = new URLSearchParams();
                var _this = this;
                this.pagination.current_page = 1
                params.append('limit', this.pagination.per_page);
                params.append('sort', this.pagination.sort);
                if (this.pagination.searchString != '') {
                    params.append('ingredientName', this.pagination.searchString);
                }
                axios.post('http://localhost:8081/ingredients/get/1', params, {
                     "Access-Control-Allow-Origin": "*",
                })
                .then(function (response) { 
                    _this.ingredients = [];
                    for(var i = 0; i < response.data.data.length; i++) {
                        var ingredient;
                    ingredient = response.data.data[i];
                    ingredient.grame = _this.grame;
                    ingredient.totalCalorii = ingredient.calorii
                    ingredient.totalProteine = ingredient.proteine
                    ingredient.totalCarbohidrati = ingredient.carbohidrati
                    ingredient.totalFibre = ingredient.fibre
                    ingredient.totalGrame = ingredient.grame
                    _this.ingredients.push(ingredient);
                  }
                    _this.pagination.total = response.data.count
                    _this.pagination.page_number = Math.ceil(_this.pagination.total / _this.pagination.per_page) 
                })
                .catch(function (error) {
                  console.log(error);
                });
            },
            changePage(page) {
                var params = new URLSearchParams();
                var _this = this;
                this.pagination.current_page = page
                params.append('limit', this.pagination.per_page);
                if (this.pagination.searchString != '') {
                    params.append('ingredientName', this.pagination.searchString);
                }
                axios.post('http://localhost:8081/ingredients/get/' + page, params, {
                     "Access-Control-Allow-Origin": "*",
                })
                .then(function (response) {
                   _this.ingredients = [];
                    for(var i = 0; i < response.data.data.length; i++) {
                        var ingredient;
                    ingredient = response.data.data[i];
                    ingredient.grame = _this.grame;
                    ingredient.totalCalorii = ingredient.calorii
                    ingredient.totalProteine = ingredient.proteine
                    ingredient.totalCarbohidrati = ingredient.carbohidrati
                    ingredient.totalFibre = ingredient.fibre
                    ingredient.totalGrame = ingredient.grame
                    _this.ingredients.push(ingredient);
                  }
                })
                .catch(function (error) {
                    console.log(error);
                });
            },
            changePagePrev() {
                var page = this.pagination.current_page - 1;
                this.pagination.current_page = page;
                
                var params = new URLSearchParams();
                var _this = this;
           
                params.append('limit', this.pagination.per_page);
                if (this.pagination.searchString != '') {
                    params.append('ingredientName', this.pagination.searchString);
                }
                axios.post('http://localhost:8081/ingredients/get/' + page, params, {
                     "Access-Control-Allow-Origin": "*",
                })
                .then(function (response) {
                   _this.ingredients = [];
                  for(var i = 0; i < response.data.data.length; i++) {
                      var ingredient;
                    ingredient = response.data.data[i];
                    ingredient.grame = _this.grame;
                    ingredient.totalCalorii = ingredient.calorii
                    ingredient.totalProteine = ingredient.proteine
                    ingredient.totalCarbohidrati = ingredient.carbohidrati
                    ingredient.totalFibre = ingredient.fibre
                    ingredient.totalGrame = ingredient.grame
                    _this.ingredients.push(ingredient);
                  }  
                })
                .catch(function (error) {
                    console.log(error);
                });
            },
            changePageNext() {
                var params = new URLSearchParams();
                var _this = this;
                var page = this.pagination.current_page + 1;
                this.pagination.current_page = page;
    
                params.append('limit', this.pagination.per_page);
                if (this.pagination.searchString != '') {
                    params.append('ingredientName', this.pagination.searchString);
                }
                axios.post('http://localhost:8081/ingredients/get/' + page, params, {
                     "Access-Control-Allow-Origin": "*",
                })
                .then(function (response) {
                    _this.ingredients = [];
                  for(var i = 0; i < response.data.data.length; i++) {
                      var ingredient;
                    ingredient = response.data.data[i];
                    ingredient.grame = _this.grame;
                    ingredient.totalCalorii = ingredient.calorii
                    ingredient.totalProteine = ingredient.proteine
                    ingredient.totalCarbohidrati = ingredient.carbohidrati
                    ingredient.totalFibre = ingredient.fibre
                    ingredient.totalGrame = ingredient.grame
                    _this.ingredients.push(ingredient);
                  }
                })
                .catch(function (error) {
                    console.log(error);
                });
            }
        },

        watch: {
            getQuestion: function(searchSrting) {
                var _this = this
                var params = new URLSearchParams();
                var _this = this;
                this.pagination.searchString = searchSrting
                this.pagination.current_page = 1
                params.append('limit', this.pagination.per_page);
                params.append('ingredientName', searchSrting);
                axios.post('http://localhost:8081/ingredients/get/1', params, {
                        "Access-Control-Allow-Origin": "*",
            })
            .then(function (response) {
                _this.ingredients = [];
                  for(var i = 0; i < response.data.data.length; i++) {
                      var ingredient;
                    ingredient = response.data.data[i];
                    ingredient.grame = _this.grame;
                    ingredient.totalCalorii = ingredient.calorii
                    ingredient.totalProteine = ingredient.proteine
                    ingredient.totalCarbohidrati = ingredient.carbohidrati
                    ingredient.totalFibre = ingredient.fibre
                    ingredient.totalGrame = ingredient.grame
                    _this.ingredients.push(ingredient);
                  }
                _this.pagination.total = response.data.count
                _this.pagination.page_number = Math.ceil(_this.pagination.total / _this.pagination.per_page)
            })
            .catch(function (error) {
                console.log(error);
            });
        }}
    }
</script>

<style>
    table {
        width: 100%;
        margin-top: 50px;
    }
    .inpuut {
        width: 120px;
        margin-bottom: 0 !important;
    }
    .fade-enter {
        opacity: 0;
    }
    .fade-enter-active {
        transition: opacity 1s;
    }
    .fade-leave {
        
    }
    .fade-leave-active {
        transition: opacity 1s;
        opacity: 0;
    }
    a {
  color: #999;
}
.current {
  color: red;
}
ul {
  padding: 0;
  list-style-type: none;
}
li {
  display: inline;
  margin: 0;
}

a.first::after {
  content:'...'
}

a.last::before {
  content:'...'
}
.myPagination {
    margin: 40px;
}
.custon-width {
    max-width: 100px;
}
.filter {
    margin-top: 20px;
}
</style>

