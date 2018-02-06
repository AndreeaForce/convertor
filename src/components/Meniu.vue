<template>
    <div class="container">
        <h1>Menu</h1>
    
        <table class="table table-hover">
            <thead>
                <tr>
                    <th scope="col">Aliment</th>
                    <th scope="col">Calorii</th>
                    <th scope="col">Proteine</th>
                    <th scope="col">Carbohidrati</th>
                    <th scope="col">Grame</th>
                </tr>    
            </thead>
            <tbody>
                <tr>
                    <td>{{ ingredients.name }}</td>
                    <td>{{ ingredients.calorii}}</td>
                    <td>{{ ingredients.proteine }}</td>
                    <td>{{ ingredients.carbohidrati}}</td>
                    <td>{{ ingredients.grame }}</td>
                </tr>
            </tbody>
        </table>    

    </div>
</template>

<script>
    import {bus} from '../main';

    export default {
        data() {
            return {
                ingredients: [
                    //{name: '', calorii: '', proteine: '', carbohidrati: '', grame: ''}
                ]
            }
        },
        created() {
           bus.$on('sendIngredient', (data) => {
               this.ingredients = data;
               this.ingredients.name = data.name;
               this.ingredients.calorii = data.calorii;
               this.ingredients.proteine = data.proteine;
               this.ingredients.carbohidrati = data.carbohidrati;
               this.ingredients.grame = data.grame;
           }); 
        }
    }
</script>

<style>
    table {
        width: 100%;
        margin-top: 50px;
    }
</style>

