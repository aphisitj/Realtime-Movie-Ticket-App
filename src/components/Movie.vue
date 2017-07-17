<template>
    <div class="box">
        <div class="columns">
            <div v-for="m in movies" :class="className(m.id)" @click="chooseMovie(m.id)">
                <figure class="image">
                    <img :src="'/movies/'+m.id+'.jpg'" />           
                </figure>
            </div>
        </div>
    </div>
</template>

<script>
import { movies } from 'Others/movie.json'

export default{
    props: ['movieId'],
    data() {
        return{
            movies
        }
    },
    methods:{
        chooseMovie(movieId){
            this.$emit('chooseMovie',movieId)
        },
        className(movieId){
            return [
                'column','pointer',
                {'chosen': this.movieId === movieId}
            ]
        }
    },
    mounted(){
        this.chooseMovie(movies[0].id)
    }
}
</script>

<style>
    .pointer{
        cursor:pointer;
    }
    .chosen {
        border-style:solid;
    }
</style>