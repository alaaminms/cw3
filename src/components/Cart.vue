<template>
<!--  eslint-disable  -->
    <div>
        <!-- Checkout -->
		<div>
			<button class="btn btn-primary" v-on:click="$parent.checkout = false">Back</button>
				<ul class="list-group mt-4">
				<div class="row">
					<div class="col-4" v-for="(subject, index) in cart" :key="subject.id">
						<div class="card" style="width: 18rem;">
							<div class="text-center">
								<img class="card-img-top" :src="subject.thumbnail" style="width: 128px; height: 128px;" alt="Card image cap">
							</div>
							<div class="card-body">
								<h5 class="card-title">{{subject.course}}</h5>
								<p class="card-text">{{subject.description}}</p>
								<div class="d-flex justify-content-between">
									<strong>Qty: {{subject.reserved}} </strong>
									<button class="btn btn-md btn-danger" v-on:click="removeFromCart(subject)"> Delete</button>
								</div>
								
							</div>
						</div>
					</div>
				</div>
				
			</ul>
			<div class="text-white mt-5">
				<form class="form" v-on:submit="buyCourse">
					<div class="form-group">
						<label for="name">Full Name</label>
						<input type="name" v-model="name" class="form-control" id="name" v-on:input="validate">
						<div class="text-danger">{{errors.name}}</div>
					</div>
					<div class="form-group">
						<label for="phone">Phone: </label>
						<input type="number" v-model="phone" class="form-control" id="phone" v-on:input="validate">
						<div class="text-danger">{{errors.phone}}</div>
					</div>
					<button type="submit" class="btn btn-primary" :disabled="successAlert || !isValid">Checkout</button>
				</form>
			</div>
		</div>
			
		<!-- Modal -->
		<div class="modal fade" id="successModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
			<div class="modal-dialog" role="document">
				<div class="modal-content">
					<div class="modal-body">
						<!-- <div class="alert alert-success" role="alert" v-if="successAlert"> -->
							<h3>Your form is successfully submitted! </h3>
						<!-- </div> -->
					</div>
					<div class="modal-footer">
						<button type="button" class="btn btn-success" data-dismiss="modal">Thanks!</button>
					</div>
				</div>
			</div>
		</div>
    </div>
</template>

<script>
/* eslint-disable */ 

export default{
    name: 'cart',
    props:{
        cart: {
            type: Array,
            required: true
        }
    },
    data() {
        return{
            checkout: false,
            errors: {
				phone: '',
				name: ''
			},
			name: '',
			phone: '',
			successAlert: false,
			isValid: false,
        }
    },
    methods:{
		removeFromCart(subject){
			this.$emit('removeFromCart', subject)
		},

		validate(){
			if (this.name.length == 0) {
				this.errors.name = "Name number is required.",
				this.isValid = false
			}
			else if (/[^a-zA-Z]/.test(this.name)) {
				this.errors.name = "You must have enter a valid name."
				this.isValid = false
			}
			if (this.phone.length == 0) {
				this.errors.phone = "Phone number is required."
				this.isValid = false
			}
			else if (this.phone.length < 10) {
				this.errors.phone = "You must have enter 10 digits."
				this.isValid = false
			}
			
			else if (!/[^a-zA-Z]/.test(this.name) && this.phone.length == 10){
				this.isValid = true;
				this.errors = {phone: '', name: ''}
			}
		},
		buyCourse(){
			if (this.name.length == 0) {
				this.errors.name = "Name number is required."
				return;
			}
			else if (/[^a-zA-Z]/.test(this.name)) {
				this.errors.name = "You must have enter a valid name."
				console.log('here')
				return;
			}
			if (this.phone.length == 0) {
				this.errors.phone = "Phone number is required."
				return;
			}
			else if (this.phone.length < 10) {
				this.errors.phone = "You must have enter 10 digits."
				return;
			}else{
				this.isValid = true;
			}
			let user = {full_name: this.name, phone: this.phone}
			const requestOptions = {
				method: "PUT",
				headers: {"Content-Type": "application/json",'Accept': 'application/json' },
				body: JSON.stringify({cart: this.cart, user: user} )
			};
			fetch(`http://localhost:5000/buy-lesson`, requestOptions)
				.then(response => response.json())
				.then(data => {
					$('#successModal').modal('show');
					console.log(this.cart);
					setTimeout(() => {
						this.successAlert = false;
						this.$parent.checkout = false;
						$('#successModal').modal('hide');
					}, 3000);
				});

			this.successAlert = true;
			this.cart = [];
			this.errors = {
				phone: '',
				name: ''
			}
		},
    }
}
</script>
