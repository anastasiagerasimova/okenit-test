<template>
    <form class="form">
		<label class="custom-select-label" for="type">Выберите тип маркера:</label>
		<select class="custom-select" id="type" v-on:change="changeValue" >
			<option value="square">Квадрат</option>
			<option value="triangle">Треугольник</option>
			<option value="circle">Круг</option>
			<option value="none">Нет</option>
		</select>
    </form>
</template>

<script>

    export default {
        name: 'SelectPanel',
        data(){
            return{
            }
        },
        methods: {
			changeValue(e){
				this.$emit('change-marker-type', e.target.value)
			}
        },
        mounted() {
          this.$emit('change-marker-type', "square")
        }

    }
</script>

<style scoped>
	.custom-select-label{
		display: block;
		margin-bottom: 1rem;
		font-weight: 500;
		font-size: 1.25rem;
		cursor: pointer;
	}
	.custom-select{
		display: inline-block;
		width: 100%;
		height: calc(1.5em + .75rem + 2px);
		padding: .375rem 1.75rem .375rem .75rem;
		font-size: 1rem;
		font-weight: 400;
		line-height: 1.5;
		color: #495057;
		vertical-align: middle;
		background: url(../assets/arrows-up-and-down.png) no-repeat right .75rem center/9px 14px;
		background-color: #fff;
		border: 1px solid #ced4da;
		border-radius: .25rem;
		-webkit-appearance: none;
		-moz-appearance: none;
		appearance: none;
		cursor: pointer;
	}
	.custom-select:focus {
		border-color: #80bdff;
		outline: 0;
		box-shadow: 0 0 0 0.2rem rgb(0 123 255 / 25%);
	}
</style> 