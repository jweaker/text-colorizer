<script lang="ts">
  type Rule = { match: string; color: string };

  const storedValue = window.localStorage.getItem("rules");
  let rules: Rule[] = storedValue ? JSON.parse(storedValue) : [];
  $: window.localStorage.setItem("rules", JSON.stringify(rules));

  let addMatch = "";
  let addColor = "#000000";

  const handleAdd = () => {
    rules = [
      ...rules.filter((v) => v.match !== addMatch),
      { match: addMatch.trim(), color: addColor },
    ];
    addMatch = "";
  };
  const handleDel = (match: string) => {
    rules = rules.filter((v) => v.match !== match);
  };

  let input = "";
  let output: HTMLDivElement;

  $: {
    let outputText = input;
    for (const rule of rules) {
      if (!rule.match) continue;
      let reg;
      try {
        reg = new RegExp(rule.match, "g");
      } catch {}
      if (!reg) continue;
      const matches = outputText.matchAll(reg);
      let addedLength = 0;
      for (const match of matches) {
        const text = match[0];
        const index = (match.index ?? 0) + addedLength;
        console.log(text, index, rule.color);
        const colorized = `<span style="color:${rule.color};">${text}</span>`;
        addedLength += colorized.length - text.length;
        outputText =
          outputText.substring(0, index) +
          colorized +
          outputText.substring(index + text.length);
      }
    }
    if (output) output.innerHTML = outputText;
    console.log(output);
  }
</script>

<main>
  <div class="container">
    <h1 class="title">القواعد</h1>
    {#each rules as rule}
      <div class="hContainer">
        <button on:click={() => handleDel(rule.match)}>احذف</button>
        <input bind:value={rule.match} />
        <input bind:value={rule.color} type="color" />
      </div>
    {/each}
    <div class="hContainer addContainer">
      <button on:click={handleAdd}>اضف</button>
      <input bind:value={addMatch} placeholder="النص" />
      <input bind:value={addColor} type="color" />
    </div>
  </div>

  <div class="container">
    <textarea class="textarea" bind:value={input} placeholder="المدخل" />
  </div>
  <div bind:this={output} class="container output">output</div>
</main>

<style>
  .title {
    text-align: center;
    width: 100%;
    color: black;
    font-size: 2rem;
    align-self: flex-start;
    margin-block: 0.5rem;
  }
  .container {
    margin: 1rem;
    max-width: 600px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    border: white solid 2px;
    border-radius: 0.2rem;
    background: darkgray;
    padding: 0.3rem;
  }

  .output {
    color: black;
    display: block;
    text-align: right;
    background-color: white;
  }
  .addContainer {
    border-top: black solid 2px;
    padding-top: 1rem;
    margin-top: 1rem;
  }
  .hContainer {
    display: flex;
    align-items: center;
    flex-direction: row-reverse;
    justify-content: center;
  }
  input {
    margin: 0.2rem;
    border-radius: 8px;
    border: 1px solid transparent;
    padding: 0.6em 1.2em;
    text-align: right;
    font-size: 1.2rem;
    font-weight: 500;
    font-family: inherit;
    background-color: #1a1a1a;
    transition: border-color 0.25s;
  }
  input[type="color"] {
    height: 44px;
    padding: 0.2rem;
  }
  input:hover {
    border-color: #646cff;
  }
  .textarea {
    max-width: 600px;
    min-height: 100px;
    font-family: inherit;
    padding: 1rem;
    text-align: right;
  }
</style>
