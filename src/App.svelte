<!-- This is the main component of the to-do app, which was built following the tutorial from 
Joy of Code found at https://joyofcode.xyz/svelte-todo-app#global-styles. The concepts defined
in this app are COMPONENT, REACTIVE, CONTROLFLOW, PROPERTIES, and LOCALSTYLE  -->
<script lang="ts">
  import "$root/styles/global.css";

  //Example 1 of 5: #COMPONENT
  import Todos from "$root/components/Todos.svelte";
</script>

<Todos />
