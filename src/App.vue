<template>
<!-- eslint-disable  -->
  <div class="container">
	  <div class="row" v-if="!checkout">
			<div class="col-1">
				<div class="form text-white" style="margin-left: -50px; margin-top: 25px;">
					<h3>Sort By:</h3>
					<div class="form-group mt-5">
						<select v-model="filter" class="form-control" v-on:change="sortCourse">
							<option value="">Choose Any..</option>
							<option value="subject">Subject</option>
							<option value="city">City</option>
							<option value="price">Price</option>
							<option value="availability">Availability</option>
						</select>
					</div>
					<hr>
					<div class="form-check">
						<input type="radio" v-model="sort" value="assending" id="assending" class="form-check-input" v-on:change="sortCourse">
						<label for="assending" class="form-check-label ml-3">Ascending </label>
					</div>
					<div class="form-check">
						<input type="radio" v-model="sort" value="descending" id="descending" class="form-check-input" v-on:change="sortCourse">
						<label for="descending" class="form-check-label ml-3">Descending </label>
					</div>

				</div>
			</div>
			<div class="col-10">
				<h3 class="text-white mt-4 mb-3">Available Lessons</h3>
				<div>
					<input type="text" class="form-control" placeholder="Search by course name, city..." v-model="searchData" v-on:input="searchLessons">
				</div>
				<Lessons :allCourses="allCourses" @addCart="addToCart"/>
			</div>
			<div class="col-1 mt-4">
				<button class="btn btn-primary" v-on:click="checkout=true" :disabled="cart.length <= 0">Cart ({{cart.length}})</button>
			</div>
		</div>
		  <Cart v-if="checkout" :cart="cart" @removeFromCart="removeFromCart"/>
	</div>
</template>

<script>
import Lessons from './components/Lessons.vue'
import Cart from './components/Cart'
export default {
	/* eslint-disable */ 
	name: 'App',
	components: {
		Lessons,
		Cart
	},
  	data() {
		return{
			projectName: 'Here will be the project name.',
			allCourses: [],
			cart: [],
			// errors: {
			// 	phone: '',
			// 	name: ''
			// },
			filter: '',
			sort: '',
			searchData: '',
			checkout: false,
			// name: '',
			// phone: '',
			// successAlert: false,
			// isValid: false,
		}
	},
	mounted() {
		this.getAllCourses();
	},
	computed: {},
	methods: {
		getAllCourses(){
			fetch("https://git.heroku.com/cw3w.git/lessons")
			.then(response => response.json())
			.then(data => {
				this.allCourses = data;
			});
		},
		addToCart(lesson){
			let exists = this.cart.includes(lesson)
			lesson.space -= 1;
			if (exists) {
				lesson.reserved += 1;
			}else{
				lesson.reserved += 1;
				this.cart.push(lesson)
			}
		},
		removeFromCart(subject){
			if (this.cart.includes(subject)) {
				subject.space += 1;
				
				if (subject.reserved > 0) {
					subject.reserved -= 1;
				}
				if (subject.reserved == 0) {
					this.cart = this.cart.filter(obj => {
						return obj.id != subject.id
					})
					console.log(subject)
				}
			}
		},

		sortCourse() {
			if (this.sort == 'assending') {
				switch (this.filter) {
					case 'subject':
							this.allCourses.sort((a, b) => (a.course > b.course) ? 1 : -1)
						break;
					case 'city':
							this.allCourses.sort((a, b) => (a.city > b.city) ? 1 : -1)
						break;
					case 'price':
							this.allCourses.sort((a, b) => (a.price > b.price) ? 1 : -1)
						break;
					case 'availability':
							this.allCourses.sort((a, b) => (a.space > b.space) ? -1 : 1)
						break;
				
					default:
						break;
			}
			}else if (this.sort == 'descending'){
				switch (this.filter) {
					case 'subject':
						this.allCourses.reverse((a, b) => (a.course > b.course) ? 1 : -1)
						break;
					case 'city':
						this.allCourses.reverse((a, b) => (a.city > b.city) ? 1 : -1)
						break;
					case 'price':
						this.allCourses.reverse((a, b) => (a.price > b.price) ? 1 : -1)
						break;
					case 'availability':
						this.allCourses.reverse((a, b) => (a.space > b.space) ? -1 : 1)
						break;
				
					default:
						break;
				}
			}
		},
		searchLessons(){

			console.log('called search');

			const requestOptions = {
				method: "POST",
				headers: {"Content-Type": "application/json",'Accept': 'application/json' },
				body: JSON.stringify({keyword: this.searchData})
			};
			fetch(`http://localhost:5000/search-lessons`, requestOptions)
				.then(response => response.json())
				.then(data => {
					this.allCourses = data;
				});

		},
	},
}
</script>

