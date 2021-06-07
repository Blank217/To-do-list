<template>
    <button @click="click"> Show Deleted items </button>
    <p id="demo"></p>
</template>

<script>
export default {
    name: 'ShowDelete',
    props: {
        DeletedTasks: Array,
    },
    methods: {
        click() {
            
            var text = "";
            var i;
            for (i = 0; i < DeletedTasks.length; i++) {
                text += DeletedTasks[i] + "<br>";
                }
             document.getElementById("demo").innerHTML = text;
        }
    }

}
</script>