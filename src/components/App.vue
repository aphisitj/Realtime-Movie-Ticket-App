<template>
        <div class="box">
            <h3 class="title">App {{ movieId }}</h3>
            <p>Count : {{ status.count }} 
               Price : {{ status.price }}
            </p>
            <Movie @chooseMovie="handleChooseMovie" :movieId="movieId" />
            <Seat 
            :movieId="movieId" 
            @chooseSeat="handleChooseSeat" 
            :selectSeats="selectSeats" 
            :firebaseSeats="firebaseSeats"
            />
        </div>
</template>

<script>
import firebase from 'firebase'
import Movie from 'Components/Movie.vue'
import Seat from 'Components/Seat.vue'
import  _ from 'lodash'
import {pushToArray} from 'Others/lib'

const config = {
    databaseURL: "https://movieseat-c9fa8.firebaseio.com"
}

firebase.initializeApp(config)

const db = firebase.database()

// const dbRef = db.ref('/').child('avatar2')
// dbRef.push('xxxd')
// const dbRef1 = db.ref('/').child('avatar')
// dbRef1.push('yyy')

export default{
    components: { Movie,Seat },
    data(){
        return{
            movieId: '',
            selectSeats: [],
            firebaseSeats: [],
            status: { count: 0, price: 0 }
        }
    },
    methods:{
        handleChooseMovie(movieId){
            if(this.status.count){
                if(confirm('Data will be lost ?')){
                    this.status = { count: 0, price: 0 }
                    this.selectSeats = []
                }else{
                    return false
                }
            }
           
            this.movieId = movieId

            const movieRef = db.ref('/').child(this.movieId)
            movieRef.on('value', snapshot => {
                // console.log(snapshot.val())           
                const seats = snapshot.val()
                this.firebaseSeats = []
                _.forOwn(seats ,s => {
                    pushToArray(s , this.firebaseSeats)
                })
            console.log(this.firebaseSeats.length) 
            })
        },
        handleChooseSeat(seat){
            // const ids = this.selectSeats.map(s => s.id)
            // const idx = ids.indexOf(seat.id)
            
            // if(idx === -1 ){
            //     this.selectSeats.push(seat)
            // } else{
            //     this.selectSeats.splice(idx,1)
            // }
            pushToArray(seat , this.selectSeats)
            const movieRef = db.ref('/').child(this.movieId)
            movieRef.push(seat)

            this.status = this.selectSeats.reduce((summary, s) => {
                summary.count++
                summary.price += s.price
                return summary
            },{count: 0 , price: 0 })
        }
    }

}
</script>