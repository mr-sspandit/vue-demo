<script setup lang="ts">
import { ref, reactive } from 'vue'

//<p>Additional Ref Unwrapping Details</p>
const refObj = ref('origRefValue')
const reactiveObj = reactive({
	refObj
})

reactiveObj.refObj = 'newRefValue'
</script>

<template>
	<div class="greetings">
		<h2>ID:</h2>
		<p>75fe51f9c6760084f3460cefc5d77c2e</p>
		<h2>Topic:</h2>
		<p>Additional Ref Unwrapping Details</p>
		<h2>Description:</h2>
		<p>Ref automatically unwrapped when access/mutated as property of reactive object</p>
		<h2>Code:</h2>
		<pre>
		  <code>
		  import { ref, reactive } from 'vue'

		  const refObj = ref('origRefValue')
		  const reactiveObj = reactive({
			  refObj
		  })

		  reactiveObj.refObj = 'newRefValue'
		  </code>
		  </pre>

		<h2>Output:</h2>
		<div>{{ refObj }}</div>
	</div>
</template>

<style scoped>
h2 {
	font-weight: bold;
	font-size: 2rem;
}
</style>
