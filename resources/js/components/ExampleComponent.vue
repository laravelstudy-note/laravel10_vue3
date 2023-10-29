<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">Example Component</div>

                    <div class="card-body">
                        I'm an example component.

						{{ counter }}

						<button @click="increment">increment</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script lang="ts" setup>
	import { ref } from "vue";

	const counter = ref(0)
	const increment = () => {
		counter.value += 1;
	}
</script>
