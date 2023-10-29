<script lang="ts" setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head } from '@inertiajs/vue3';
import { ref,reactive } from "vue";

type CounterType = {
	counter: number
}

const counter = ref(0);

const counterObj = reactive<CounterType>({
	counter: 0
})

const increment = () => {
	counter.value++;
	counterObj.counter = counterObj.counter + 1
};
</script>

<template>
    <Head title="Dashboard" />

    <AuthenticatedLayout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">Dashboard</h2>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
                    <div class="p-6 text-gray-900">You're logged in!</div>

					<button @click="increment">counter={{ counter }}</button>

					<p>{{ counterObj.counter }}</p>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>
