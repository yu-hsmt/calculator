<template>
    <div class="bg">
        <Calculator />
    </div>
</template>

<script setup>
import Calculator from './components/Calculator'
</script>

<style lang="scss">
	html,
	body {
		font-family: 'Sora', sans-serif;
	}
    .bg {
		background-color: #3C1687;
		width: 100vw;
		height: 100vh;
		padding: 2rem;
	}

</style>
