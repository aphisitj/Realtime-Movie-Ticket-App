<template>
    <div class="box">
        <div class="columns">
          <template v-for="s in seats">
          <button :class="className(s)"
                  :disabled="s.seated"
                  @click="chooseSeat(s)"
          >{{ s.id }} {{ s.price }}</button>
          <span>&nbsp;</span>
          </template>
         
        </div>
    </div>
</template>

<script>
import  movie  from 'Others/movie.json'

   export default{
        props: ['movieId','selectSeats','firebaseSeats'],
        methods:{
            chooseSeat(seat){
                this.$emit('chooseSeat',seat)
            },
            className(seat){
            const ids = this.selectSeats.map(s => s.id)
            const idx = ids.indexOf(seat.id)

            const firebaseIds = this.firebaseSeats.map(s => s.id)
            const firebaseIdx = firebaseIds.indexOf(seat.id)

                return [
                    'button',
                    {
                        'is-danger': seat.seated, 
                        'is-primary': idx != -1,
                        'is-warning': firebaseIdx != -1 && idx === -1
                    }
                ]

            }
        },
        computed:{
            seats(){
                return movie[this.movieId]
            }
        }

    }
</script>