<template>
    <div class="modalBlock">
		<span class="modalBlock-crystal"></span>
		<div class="modalBlock-title">
			Sign In
		</div>
		<!-- <form> -->
			<div class="formGroup formGroup-user">
				<span>Username</span>
				<div class="formGroup-flex">
					<div class="formGroup-icon">
						<i class="form-icon icon-user"></i>
					</div>
					<input type="text" v-model="username">
				</div>
			</div>
			<div class="formGroup formGroup-password">
				<p>(Min. 10 Characters)</p>
				<span>Password</span>
				<div class="formGroup-flex">
					<div class="formGroup-icon">
						<i class="form-icon icon-password"></i>
					</div>
					<input type="password" v-model="password">
				</div>
			</div>
			<div class="formGroup formGroup-email">
				<span>Choose Server</span>
				<div class="formGroup-flex">
					<div class="formGroup-icon">
						<i class="form-icon icon-question"></i>
					</div>
					<select v-model="netType">
						<option v-if="$store.state.config.global == 0" value="0">Global</option>
						<option v-if="$store.state.config.sea == 0" value="1">Sea</option>
					</select>
				</div>
			</div>
			<div class="formGroup-button">
				<button class="button-big" @click="userSignIn()">Sign In</button>
			</div>
			<div class="formGroup-text">
				<p>Forgot password? <a @click="$store.dispatch('setModalType', 'recover-now')">Recover Now</a></p>
				Don`t have a account? <a @click="clickRegisterNow()">Register Now</a>
			</div>
		<!-- </form> -->
		<a class="close-modal" @click="$store.commit('closeModal')"></a>
	</div>
</template>
<script>
	export default {
		data() {
			return {
				username: '',
				password: '',
				netType: 0
			}
		},
		mounted() {
			if (this.$store.state.config.global == 1) {
				this.netType = 1
			} else if (this.$store.state.config.sea == 1)
				this.netType = 0
		},
		methods: {
			clickRegisterNow() {
                if (this.$store.state.config.registration != 0) {
					this.$toast.error('The registration is disabled')
                    return
                }
                this.$store.dispatch('setModalType', 'sign-up')
            },
			userSignIn() {
				if (!this.username){
					this.$toast.error('Please insert your username.')
						return
				}
				if (!this.password){
					this.$toast.error('Please insert your password.')
						return
				}
				let data = {}
				data.username =this.username
				data.password = this.password
				data.netType = this.netType
				this.$api.request.userSignIn(data, (res => {
					if (res.data.type == 3) {
						this.$toast.error(`The wrong password.`)
					} else if (res.data.type == 2) {
						this.$toast.error(`Please check out your login info again.`)
					} else if (res.data.user) {
						console.log(res.data.token)
						// window.localStorage.token = res.data.token
						window.sessionStorage.setItem("token", res.data.token)
						this.$store.dispatch('account/setAccount', res.data.user)
						this.$store.commit('closeModal')
					}
				}), err =>{
					this.$toast.error('Server is disconnected.')
					console.log(err)
				})

			}
		}
	}
</script>
<style lang="scss" scoped>
	.formGroup-text {
		a {
			color: #91a8f8;
			&:hover {
				text-decoration: underline;
			}
		}
	}
</style>