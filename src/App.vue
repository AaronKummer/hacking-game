<script setup>
</script>

<template>
  <div class="terminal">
    <div class="terminal-body" ref="terminalBody">
      <div v-for="(output, index) in outputs" v-bind:key="index" class="terminal-output">{{ output }}</div>
    </div>
    <div class="terminal-input-container">
      <span class="terminal-prompt">C:\></span>
      <input v-model="input" @keyup.enter="processInput" class="terminal-input">
    </div>
  </div>

  <div class="graphicalui">
    <canvas id="grid" width="100%" height="100%"></canvas>
  </div>


</template>
<style scoped>
#canvas {
  background-color: black;
}

.graphicalui {
  background-color: black;
  height: 98%;
  width: 47%;
  overflow: hidden;
  position: fixed;
  left: 53%;
  bottom: 10px;
  box-sizing: border-box;
  border: 2px solid #39ff14;
}

#grid {
  width: 100%;
  height: 100%;
  border: 1px solid green;
}

.terminal-body::-webkit-scrollbar {
  display: none;
}

.terminal {
  background-color: black;
  height: 98%;
  width: 50%;
  /* take up 1/2 of the screen's width */
  padding: 20px;
  overflow: hidden;
  position: fixed;
  left: 2%;
  /* position to the left of the screen */
  bottom: 10px;
  box-sizing: border-box;
  border: 2px solid #39ff14;
}

.terminal-input-container {
  position: relative;
  border: none;
  width: 100%;
  display: flex;
}

.terminal-prompt {
  margin-right: 10px;
}

.terminal-input {
  border: none;
  background-color: black;
  width: 100%;
  outline: none;
  flex-grow: 1;
}

.terminal-body::-webkit-scrollbar {
  width: 0;
}

.terminal-body {
  overflow-y: scroll;
  /* added to make the output scrollable */
  height: calc(100% - 40px);
  /* adjust as desired, 40px is the combined height of the input and prompt */
}

.terminal-input-container {
  position: absolute;
  bottom: 0;
  /* this sticks the input container to the bottom of the terminal */
}

.terminal-input {
  border: none;
  background-color: black;
  width: 100%;
}

.terminal-prompt,
.terminal-input,
.terminal-output {
  font-size: 20px;
  color: #39ff14;
  white-space: pre-wrap;
  /* This allows the <br> tags to be recognized */
}
</style>

<script>
export default {
  data() {
    return {
      attack: Math.floor(Math.random() * (10 - 5 + 1) + 5),
      inputs: [],
      input: "",
      outputs: [],
      attack: 10,
      processing: false,
      inputArray: [],
      keywords: ["ping", "mark", "exit", "hack", "ls"],
      targets: [
        {
          name: "Television",
          defense: 5,
          poweredOn: true,
          ipAddress: `192.168.${Math.floor(Math.random() * 256)}.${Math.floor(Math.random() * 256)}`,
          utilities: ["powerOn()", "powerOff()", "changeChannel(channel:number)", "changeVolume(volume:number)"]
        },
        {
          name: "Garage Door",
          defense: 3,
          poweredOn: true,
          ipAddress: `192.168.${Math.floor(Math.random() * 256)}.${Math.floor(Math.random() * 256)}`,
          utilities: ["openDoor()", "closeDoor()"]
        },
        {
          name: "Webcam",
          defense: 2,
          poweredOn: true,
          ipAddress: `192.168.${Math.floor(Math.random() * 256)}.${Math.floor(Math.random() * 256)}`,
          utilities: ["startStreaming()", "stopStreaming()", "changeResolution(resolution:string)"]
        },
        {
          name: "Kitchen Light",
          defense: 1,
          poweredOn: true,
          ipAddress: `192.168.${Math.floor(Math.random() * 256)}.${Math.floor(Math.random() * 256)}`,
          utilities: ["lightOn()", "lightOff()", "changeBrightness(brightness:number)"]
        },
        {
          name: "Gun Safe",
          defense: 8,
          poweredOn: false,
          ipAddress: `192.168.${Math.floor(Math.random() * 256)}.${Math.floor(Math.random() * 256)}`,
          utilities: ["unlock()", "lock()", "changeCode(code:string)"]
        }
      ]
    };
  },
  created() {
    this.outputs.push("Welcome to the matrix");
    this.outputs.push("Try issuing some commands such as " + this.keywords.join(", "));
    this.outputs.push("");
    document.addEventListener("keyup", event => {
      if (event.code === "Enter") {
        this.processInput();
      }
    });
  },
  mounted() {
    this.createGrid();
  },
  updated() {
  },
  methods: {
    createGrid() {
      this.$nextTick(() => {
        const canvas = document.getElementById("grid");
        const ctx = canvas.getContext("2d");
        const boxSize = 35;
        const rows = 18;
        const cols = 18;
        ctx.canvas.width = canvas.offsetWidth;
        ctx.canvas.height = canvas.offsetHeight;
        ctx.fillStyle = "green";

        // randomly select 1-3 rows and 1-3 columns to not have boxes drawn on
        const emptyRows = new Set();
        const emptyCols = new Set();
        while (emptyRows.size < Math.floor(Math.random() * 3) + 1) {
          emptyRows.add(Math.floor(Math.random() * rows));
        }
        while (emptyCols.size < Math.floor(Math.random() * 3) + 1) {
          emptyCols.add(Math.floor(Math.random() * cols));
        }

        for (let i = 0; i < rows; i++) {
          for (let j = 0; j < cols; j++) {
            if (!emptyRows.has(i) && !emptyCols.has(j)) {
              ctx.fillRect(j * boxSize, i * boxSize, boxSize, boxSize);
              ctx.strokeRect(j * boxSize, i * boxSize, boxSize, boxSize);
            }
          }
        }
        ctx.strokeStyle = "green";
      });
    },
    scrollToBottom() {
      this.$refs.terminalBody.scrollTop = this.$refs.terminalBody.scrollHeight
    },
    processInput() {
      let targetFound = false;
      if (this.input) {
        this.outputs.push("> " + this.input)
        this.inputArray = this.input.split(" ");

        // message += ".";
        // this.outputs.pop();
        // this.outputs.push("Processing...");
        this.processing = false;
        switch (this.inputArray[0]) {
          case "ping":
            if (this.inputArray.length === 1) {
              this.outputs.push("What would you like to ping?");
            } else {
              let targetName = this.inputArray.slice(1).join(" ").toLowerCase();
              let target = this.targets.find(t => targetName === t.name.toLowerCase());
              if (target) {
                let jsonString = JSON.stringify(target, function (key, value) {
                  if (key === 'utilities') {
                    return undefined;
                  }
                  return value;
                });
                jsonString = jsonString.replace(/{/g, '{\n').replace(/}/g, '\n}').replace(/,/g, ',\n').replace(/":"/g, '": ');
                this.outputs.push("Processing.");
                setTimeout(() => {
                  this.$nextTick(() => {
                    this.outputs.pop();
                    this.outputs.push(jsonString);
                  });
                }, 1000);
              } else {
                this.outputs.push("Invalid Command: " + this.input);
              }
            }
            break;
          case "hack":
            if (this.inputArray.length === 1) {
              this.outputs.push("Which IP would you like to hack?");
            } else {
              let targetFound = false;
              let target;
              let targetIp = this.inputArray[1];
              for (let i = 0; i < this.targets.length; i++) {
                if (this.targets[i].ipAddress === targetIp) {
                  targetFound = true;
                  target = this.targets[i];
                  break;
                }
              }
              if (targetFound) {
                let attack = Math.floor(Math.random() * 10) + 5;
                let die = Math.floor(Math.random() * 4) + 1;
                if (attack - die > target.defense) {
                  this.outputs.push("You have successfully hacked " + target.name);
                  this.outputs.push("Utilities: " + target.utilities.join(", "));
                } else {
                  this.outputs.push("You failed to hack " + target.name);
                }
              } else {
                this.outputs.push("Invalid IP: " + targetIp);
              }
            }
            break;

          case "mark":
            targetMarked = false;
            for (let i = 0; i < this.targets.length; i++) {
              if (this.inputArray[1] === this.targets[i].name) {
                targetMarked = true;
                this.outputs.push(`Marked ${this.targets[i].name}`);
              }
            }
            if (!targetMarked) {
              this.outputs.push(`Cannot find target ${this.inputArray[1]}`);
            }
            break;

          case "exit":
            this.outputs.push("Exiting the Matrix...");
            this.outputs = []
            break;

          default:
            if (this.input === "ls") {
              let targetList = "";
              for (let i = 0; i < this.targets.length; i++) {
                targetList += this.targets[i].name + ", ";
              }
              this.outputs.push(targetList);
            } else {
              this.outputs.push(`Invalid Command: ${this.input}`);
            }
            break;
        }
        this.input = "";
      }
      this.scrollToBottom();
    }
  }
}

</script>