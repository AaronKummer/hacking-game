<script setup>
</script>

<template>
  <div class="terminal">
    <div class="terminal-header">
      <span class="terminal-prompt">C:\></span>
    </div>
    <div class="terminal-body">
      <div v-for="output in outputs" class="terminal-output">
        {{ output }}
      </div>
    </div>
    <div class="terminal-input-container">
      <input v-model="input" @keyup.enter="processInput" class="terminal-input" placeholder="Enter command here">
    </div>
  </div>
</template>

<style scoped>
.terminal {
  background-color: black;
  color: #39ff14;
  font-family: "Courier New", monospace;
  padding: 20px;
}

asdasdÉÉÈÈ .terminal-header {
  display: flex;
  align-items: center;
}

.terminal-prompt {
  font-weight: bold;
}

.terminal-input-container {
  display: flex;
  align-items: center;
}

.terminal-input {
  background-color: black;
  color: #39ff14;
  font-family: "Courier New", monospace;
  padding: 5px;
  border: none;
  width: 100%;
}
</style>

<script>
export default {
  data() {
    return {
      input: "",
      outputs: [],
      keywords: ["ping", "mark", "exit", "hack", "ls"],
      targets: [
        {
          name: "Television",
          defense: 5,
          poweredOn: true
        },
        {
          name: "Garage Door",
          defense: 3,
          poweredOn: true
        },
        {
          name: "Webcam",
          defense: 2,
          poweredOn: true
        },
        {
          name: "Kitchen Light",
          defense: 1,
          poweredOn: true
        },
        {
          name: "Gun Safe",
          defense: 8,
          poweredOn: false
        }
      ]
    };
  },
  methods: {
    created() {
      document.addEventListener("keyup", event => {
        if (event.code === "Enter") {
          this.processInput();
        }
      });
    },
    processInput() {
      if (this.keywords.includes(this.input)) {
        switch (this.input) {
          case "ping":
            this.outputs.push("Pinging...");
            break;
          case "hack":
            if (this.input.split(" ").length < 2) {
              this.outputs.push("Sure which target would you like to hack?");
              break;
            }
            this.outputs.push("Hacking...");
            break;
          case "mark":
            if (this.input.split(" ").length < 2) {
              this.outputs.push("Sure which target would you like to mark?");
              break;
            }
            this.outputs.push("Marking...");
            break;
          case "wipe":
            if (this.input.split(" ").length < 2) {
              this.outputs.push("Sure which target would you like to wipe?");
              break;
            }
            this.outputs.push("Wiping...");
            break;
          case "dom":
            if (this.input.split(" ").length < 2) {
              this.outputs.push("Sure which target would you like to access the DOM of?");
              break;
            }
            this.outputs.push("Accessing the DOM...");
            break;
          case "exit":
            this.outputs.push("Exiting...");
            break;
          case "ls":
            this.outputs.push("Targets: ");
            this.targets.forEach(target => {
              this.outputs.push(`- ${target.name}`);
            });
            break;
          default:
            this.outputs.push("Command not recognized.");
        }
      } else {
        this.outputs.push("Command not recognized.");
      }
      this.outputs.push(`C:\> ${this.input}`)
      this.input = "";
    }


  }
};
</script>