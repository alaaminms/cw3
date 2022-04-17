<template>
<!-- eslint-disable   -->
	<div class="row">
		<div
			class="col-12 d-flex justify-content-center mt-2"
			v-if="allCourses.length <= 0"
		>
			<div class="loader"></div>
		</div>
		
		<div class="col-4 mt-4" v-for="lesson in allCourses" :key="lesson.id">
			<div class="card" style="width: 18rem;">
				<div class="text-center">
					<img :src="lesson.thumbnail" class="card-img-top" alt="logo" style="height: 128px; object-fit: cover;">
				</div>
				<div class="card-title text-center mt-1">
					<strong>{{ lesson.course }}</strong>
				</div>
				<div class="card-body">
					<strong>City: </strong>{{lesson.city}} <hr>
					<strong>Price: </strong>{{lesson.price}} <hr>
					{{lesson.description.substr(0, 50) + '...'}}
				</div>
				
				<div class="row card-footer justify-content-center">
					<div class="col-6">
						<button class="btn btn-primary" v-on:click="addToCart(lesson)" :disabled="lesson.space <= 0">Add to cart</button>
					</div>
					<div class="col-6 mt-1">
						<strong> Space: {{lesson.space}} </strong>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
/* eslint-disable */ 
export default {
  name: 'Lessons',
  props: {
    allCourses: Array
  },
  data(){
    return{
		
    }
  },
  methods: {
		addToCart(lesson){
			this.$emit('addCart', lesson)
			// console.log(JSON.stringify(this.cart));
		},
  }
}
</script>
