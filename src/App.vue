<template>
  <header class="container">
    <h1>Decision Helper</h1>
    <input
      type="text"
      name="decisionText"
      id="decisionText"
      placeholder="Enter your choice and press enter."
      v-model="decisionText"
      @keydown.enter="addChoice"
    />
  </header>
  <main>
    <div class="choices">
      <div v-for="(choice, index) in choices" :key="index" class="choice">
        <h2>
          {{ choice.name }}
        </h2>
        <section>
          <h3>Pros</h3>
          <ul>
            <li v-for="(pro, index) in choice.pros" :key="index">
              {{ pro }}
            </li>
          </ul>
          <input
            type="text"
            name="pros"
            id="pros"
            :placeholder="`Enter a pro of ${choice.name} and press enter.`"
            @keydown.enter="addItem('pros', $event, choice)"
          />
        </section>
        <section>
          <h3>Cons</h3>
          <ul>
            <li v-for="(con, index) in choice.cons" :key="index">
              {{ con }}
            </li>
          </ul>
          <input
            type="text"
            name="cons"
            id="cons"
            :placeholder="`Enter a con of ${choice.name} and press enter.`"
            @keydown.enter="addItem('cons', $event, choice)"
          />
        </section>
      </div>
    </div>
  </main>
  <footer>
    <div class="container">
      <p>Transfer Code (Double Click to import a code)</p>
      <input
        type="text"
        :value.="transfercode"
        readonly
        @dblclick="toggleReadonlyTransfer"
      />
      <p v-if="readonlyTransfer">Import Code</p>
      <input
        type="text"
        v-if="readonlyTransfer"
        v-model.trim="importCode"
        placeholder="Paste the transfer code here and click import."
        @keydown.enter="importTransferCode"
      />
    </div>
  </footer>
</template>

<script>
import { ref, computed, onMounted, watch } from "vue";

export default {
  name: "decisions",
  setup() {
    onMounted(() => {
      importCode.value = localStorage.getItem("choices") || "W10=";
      importTransferCode();
      readonlyTransfer.value = false;
    });
    let decisionText = ref();
    let readonlyTransfer = ref(false);
    let importCode = ref();
    let transfercode = computed(() => btoa(JSON.stringify(choices.value)));
    let choices = ref([]);
    let addChoice = () => {
      choices.value.push({ name: decisionText.value, pros: [], cons: [] });
      decisionText.value = "";
    };

    let addItem = (item, e, choice) => {
      choice[item].push(event.target.value);
      event.target.value = "";
    };
    let toggleReadonlyTransfer = () =>
      (readonlyTransfer.value = !readonlyTransfer.value);

    let importTransferCode = () => {
      let items = JSON.parse(atob(importCode.value));
      choices.value = items;
      importCode.value = "";
    };

    watch(transfercode, (transfercode) => {
      localStorage.setItem("choices", transfercode);
    });

    return {
      decisionText,
      choices,
      addChoice,
      importCode,
      addItem,
      transfercode,
      readonlyTransfer,
      toggleReadonlyTransfer,
      importTransferCode,
    };
  },
};
</script>

<style>
body {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;

  color: #2c3e50;
  margin: 0;
}
* {
  box-sizing: border-box;
}

input,
textarea {
  padding: 0.5em 1em;
  width: 100%;
  opacity: 0.4;
  border-color: #4d4d4d7e;
  border: 1px solid;
  transition: padding, opacity cubic-bezier(0.075, 0.82, 0.165, 1) 0.8s;
}
input:focus,
textarea:focus {
  opacity: 1;
  border-color: #2c3e50;npm 
  padding: 1em 2em;
}
main,
.container {
  display: block;
  max-width: 1200px;
  width: 80%;
  margin: 0 auto;
}
main {
  margin: 60px auto;
}
header {
  text-align: center;
}
footer {
  background-color: #2c3e50;
  padding: 2em 0;
  color: #fff;
}

div.choices {
  display: flex;
  flex-wrap: wrap;
  align-content: space-between;
  justify-content: space-between;
}

div.choice {
  min-width: 480px;
}
</style>
