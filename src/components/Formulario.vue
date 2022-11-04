<template>
	<section>
		<div class="jumbotron">
			<h1>Formulario</h1>
			<vue-form :state="formState" @submit.prevent="enviar">
				<validate tag="div">
					<label for="nombre">Nombre</label>
					<input
						type="text"
						id="nombre"
						class="form-control"
						autocomplete="off"
						v-model.trim="formData.nombre"
						name="nombre"
						required
						:minlength="nombreMinLength"
						:maxlength="nombreMaxLength"
						no-espacios
					/>

					<field-messages name="nombre" show="$dirty">
						<div slot="required" class="alert alert-danger mt-1">
							El nombre es un campo requerido
						</div>
						<div slot="minlength" class="alert alert-danger mt-1">
							El nombre tiene que tener minimo
							{{ this.nombreMinLength }} caracteres.
						</div>
						<div slot="maxlength" class="alert alert-danger mt-1">
							El nombre tiene que tener maximo
							{{ this.nombreMaxLength }} caracteres.
						</div>
						<div slot="no-espacios" class="alert alert-danger mt-1">
							Ingrese su nombre sin espacios.
						</div>
					</field-messages>
				</validate>

				<br/>

				<validate tag="div">
					<label for="descripcion">Descripcion</label>
					<input
						type="text"
						id="descripcion"
						class="form-control"
						v-model="formData.descripcion"
						name="descripcion"
						required
					/>

					<field-messages name="descripcion" show="$dirty">
						<div slot="required" class="alert alert-danger mt-1">
							La descripcion es un campo requerido
						</div>
					</field-messages>
				</validate>

				<br/>

				<validate tag="div">
					<label for="importe">Importe</label>
					<input
						type="number"
						id="importe"
						class="form-control"
						autocomplete="off"
						v-model.number="formData.importe"
						name="importe"
						required
					/>

					<field-messages name="importe" show="$dirty">
						<div slot="required" class="alert alert-danger mt-1">
							El importe a pagar es un campo requerido
						</div>
					</field-messages>
				</validate>

				<button
					class="btn btn-success my-3"
					type="submit"
					:disabled="formState.$invalid"
					@click="this.enviar"
				>
					Enviar
				</button>

				<hr>
				<hr>
				<h1 class="text-xl-left">Detalle de Gasto</h1>
				<hr>

				<h3 :style="{background: colorearPresupuesto}" class="text-center">Presupuesto</h3>
					<label for="presupuesto" >Importe</label>
					<input
						type="number"
						id="presupuesto"
						class="form-control mb-4"
						autocomplete="off"
						v-model.number="formData.presupuesto"
						name="presupuesto"
					/>
			</vue-form>


			<div v-if="posts.length">
				<div class="table-responsive">
					<table class="table table-dark table-striped">
						<tr>
							<th>Nombre</th>
							<th>Descripcion</th>
							<th>Importe</th>
						</tr>
						<tr v-for="post in posts" :key="post.id">
							<td>{{ post.nombre }}</td>
							<td>{{ post.descripcion }}</td>
							<td>${{ post.importe }}.-</td>
							<td>{{ post.fecha }}</td>
						</tr>
						<tr aria-colspan="3">
							<td :style="{color: colorear}">Importe Acumulado: ${{ this.acumuladorGasto }}.-</td>
						</tr>
					</table>
				</div>
			</div>
			<div v-else class="alert alert-danger text-center">
				No hay datos
			</div>
		</div>
	</section>
</template>

<script>
export default {
	name: "src-components-formulario",
	props: [],
	mounted() {
	},
	data() {
		return {
			formState: {},
			formData: this.getInitialData(),
			nombreMinLength: 3,
			nombreMaxLength: 15,
			posts: []
		};
	},
	methods: {
		getInitialData() {
			return {
				nombre: null,
				descripcion: null,
				importe: null,
				fecha: null,
				presupuesto: null
			};
		},
		getDateFormatted() {
			let d = new Date()
			return `${ d.getDate() }/${ d.getMonth() + 1 }/${ d.getFullYear() } ${ d.getHours() }:${ d.getMinutes() }:${ d.getSeconds() }`
		},
		enviar() {
			this.formData.fecha = this.getDateFormatted()
			this.posts.push( this.formData );
			this.formData = this.getInitialData();
			this.formState._reset();
		},
	},
	computed: {
		acumuladorGasto() {
			let acum = 0
			for ( let post of this.posts ) {
				acum += post.importe
			}
			return acum
		},
		colorear() {
			const gastoMedio = this.acumuladorGasto > 1000 && this.acumuladorGasto < 5000
			const gastoBajo = this.acumuladorGasto < 1000

			return gastoBajo ? '#19f505' : gastoMedio ? '#dd02fd' : '#ff5900'
		},
		colorearPresupuesto() {
			return this.acumuladorGasto > this.formData.presupuesto ? "#f80505" : "000"
		},
	},

};
</script>

<style scoped lang="css"></style>
