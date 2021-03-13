<script>
  import {
    Tabs,Tab,MaterialApp,Button,WindowItem,Window,TextField,ListItem,Tooltip,
  } from "svelte-materialify";
  import { tasks } from "./utils/dummy-tasks.js";
  let todos = [...tasks];
  let done = todos.filter((task) => task.done === true);
  let haveToDo = todos.filter((task) => task.done === false);
  let modifyHaveToDoList = (param) => {
    todos = todos.map((task) =>
      task.id === param.id ? { ...task, done: false } : task
    );
    done = done.filter((task) => task.id !== param.id);
    haveToDo = [...haveToDo, { ...param, done: false }];
  };
  let modifyDoneList = (param) => {
    todos = todos.map((task) =>
      task.id === param.id ? { ...task, done: true } : task
    );
    haveToDo = haveToDo.filter((task) => task.id !== param.id);
    done = [...done, { ...param, done: true }];
  };
  let tabTitles = ["Have to do", "Completed", "All"];
  let newTodoTitle = "";
  const addNewTodDo = () => {
    let len = todos.length + 1;
    todos = [
      ...todos,
      {
        id: len,
        title: newTodoTitle.trim(),
        done: false,
      },
    ];
    haveToDo = [
      ...haveToDo,
      {
        id: len,
        title: newTodoTitle.trim(),
        done: false,
      },
    ];
    newTodoTitle = "";
  };
  let tabIndex = 0;
</script>

<MaterialApp>
  <TextField placeholder="I have to do ..." bind:value={newTodoTitle} />
  <Button
    on:click={addNewTodDo}
    class={newTodoTitle.trim().length > 0 ? "primary-text" : ""}
    disabled={newTodoTitle.trim().length === 0}
  >
    Add
  </Button>
  <Tabs grow class="blue-text" bind:value={tabIndex}>
    <div slot="tabs">
      {#each tabTitles as title}
        <Tab>{title}</Tab>
      {/each}
    </div>
    <Window bind:value={tabIndex} class="ma-4">
      <WindowItem>
        {haveToDo.length === 0 ? "Nothing added!" : ""}

        {#each haveToDo.sort((a, b) => (a.id > b.id ? 1 : -1)) as task}
          <ListItem on:click={() => modifyDoneList(task)}>
            <Tooltip top>
              {task.title}
              <span slot="tip">Move to Completed</span>
            </Tooltip>
          </ListItem>
        {/each}
      </WindowItem>
      <WindowItem>
        {done.length === 0 ? "Nothing added!" : ""}

        {#each done.sort((a, b) => (a.id > b.id ? 1 : -1)) as task}
          <ListItem on:click={() => modifyHaveToDoList(task)}>
            <Tooltip top>
              {task.title}
              <span slot="tip">Move to HaveToDo</span>
            </Tooltip>
          </ListItem>
        {/each}
      </WindowItem>
      <WindowItem>
        {todos.length === 0 ? "Nothing added!" : ""}
        {#each todos.sort((a, b) => (a.id > b.id ? 1 : -1)) as task}
          <ListItem
            on:click={() => {
              if (task.done) {
                modifyHaveToDoList(task);
              } else {
                modifyDoneList(task);
              }
            }}
          >
           <Tooltip top>
              <span class={task.done ? "done" : ""}>{task.title}</span>
              <span slot="tip">Change State</span>
            </Tooltip>
          </ListItem>
        {/each}
      </WindowItem>
    </Window>
  </Tabs>
</MaterialApp>

<style>
  .done {
    text-decoration: line-through;
  }
</style>
