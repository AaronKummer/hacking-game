<script setup>
</script>

<template>
  <div class="terminal">
    <div class="terminal-body" ref="terminalBody">
      <div v-for="(output, index) in outputs" v-bind:key="index" class="terminal-output">
        {{ output }}
      </div>
    </div>
    <div class="terminal-input-container">
      <span class="terminal-prompt">C:\></span>
      <input v-model="input" @keyup.enter="processInput" class="terminal-input" />
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
      hood: null,
      location: "matrix",
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
          ipAddress: `192.168.${Math.floor(Math.random() * 256)}.${Math.floor(
            Math.random() * 256
          )}`,
          utilities: [
            "powerOn()",
            "powerOff()",
            "changeChannel(channel:number)",
            "changeVolume(volume:number)",
          ],
        },
        {
          name: "Garage Door",
          defense: 3,
          poweredOn: true,
          ipAddress: `192.168.${Math.floor(Math.random() * 256)}.${Math.floor(
            Math.random() * 256
          )}`,
          utilities: ["openDoor()", "closeDoor()"],
        },
        {
          name: "Webcam",
          defense: 2,
          poweredOn: true,
          ipAddress: `192.168.${Math.floor(Math.random() * 256)}.${Math.floor(
            Math.random() * 256
          )}`,
          utilities: [
            "startStreaming()",
            "stopStreaming()",
            "changeResolution(resolution:string)",
          ],
        },
        {
          name: "Kitchen Light",
          defense: 1,
          poweredOn: true,
          ipAddress: `192.168.${Math.floor(Math.random() * 256)}.${Math.floor(
            Math.random() * 256
          )}`,
          utilities: [
            "lightOn()",
            "lightOff()",
            "changeBrightness(brightness:number)",
          ],
        },
        {
          name: "Gun Safe",
          defense: 8,
          poweredOn: false,
          ipAddress: `192.168.${Math.floor(Math.random() * 256)}.${Math.floor(
            Math.random() * 256
          )}`,
          utilities: ["unlock()", "lock()", "changeCode(code:string)"],
        },
      ],
    };
  },
  created() {
    this.outputs.push("Welcome to the matrix");
    this.outputs.push(
      "Try issuing some commands such as " + this.keywords.join(", ")
    );
    this.outputs.push("");
    document.addEventListener("keyup", (event) => {
      if (event.code === "Enter") {
        this.processInput();
      }
    });
  },
  mounted() {
    this.createGrid();
    this.createHood();
  },
  updated() { },
  methods: {
    updateCell(updatedCell) {
      for (let i = 0; i < this.hood.length; i++) {
        for (let j = 0; j < this.hood[i].length; j++) {
          if (this.hood[i][j].ipAddress === updatedCell.ipAddress) {
            this.hood[i][j] = updatedCell;
            return;
          }
        }
      }
    },
    startAnimation() {
      this.animationId = requestAnimationFrame(() => {
        // check if any cells in this.hood are hacked
        let hackedCells = this.hood.flat().filter((cell) => cell.hacked);
        // update the appearance of the hacked cells on the canvas
        // ...
        // call startAnimation again
        this.startAnimation();
      });
    },
    stopAnimation() {
      cancelAnimationFrame(this.animationId);
    },
    createHood() {
      // Initialize hood as a 2D array of objects with type "filler"
      this.hood = new Array(18);
      for (let i = 0; i < 18; i++) {
        this.hood[i] = new Array(18);
        for (let j = 0; j < 18; j++) {
          this.hood[i][j] = { type: "filler" };
        }
      }

      // Pick random axis for street
      let xAxis = Math.floor(Math.random() * 18);
      let yAxis = Math.floor(Math.random() * 18);
      // Label cells on chosen axis as "street"
      for (let i = 0; i < 18; i++) {
        this.hood[xAxis][i].type = "street";
        this.hood[i][yAxis].type = "street";
      }

      // Label cells adjacent to street cells as "residential" or "industrial"
      for (let i = 0; i < 18; i++) {
        for (let j = 0; j < 18; j++) {
          if (this.hood[i][j].type === "filler") {
            if (
              (i === xAxis - 1 ||
                i === xAxis + 1 ||
                j === yAxis - 1 ||
                j === yAxis + 1) &&
              Math.random() < 0.875
            ) {
              this.hood[i][j].type = "residential";
              this.hood[i][j].hackAttempts = 0;
              this.hood[i][j].hacked = false;
              this.hood[i][j].defense = Math.floor(Math.random() * (10 - 0 + 1));
              this.hood[i][j].ipAddress =
                Math.floor(Math.random() * 256) +
                "." +
                Math.floor(Math.random() * 256) +
                "." +
                Math.floor(Math.random() * 256) +
                "." +
                Math.floor(Math.random() * 256);
            } else if (
              (i === xAxis - 1 ||
                i === xAxis + 1 ||
                j === yAxis - 1 ||
                j === yAxis + 1) &&
              Math.random() >= 0.875
            ) {
              this.hood[i][j].type = "industrial";
              this.hood[i][j].hackAttempts = 0;
              this.hood[i][j].hacked = false;
              this.hood[i][j].defense = Math.floor(Math.random() * (20 - 5 + 1)) + 5;
              this.hood[i][j].ipAddress =
                Math.floor(Math.random() * 256) +
                "." +
                Math.floor(Math.random() * 256) +
                "." +
                Math.floor(Math.random() * 256) +
                "." +
                Math.floor(Math.random() * 256);
            }
          }
        }
      }
    },

    createGrid() {
      this.$nextTick(() => {
        const canvas = document.getElementById("grid");
        const ctx = canvas.getContext("2d");
        const boxSize = 35;
        const rows = 18;
        const cols = 18;
        ctx.canvas.width = canvas.offsetWidth;
        ctx.canvas.height = canvas.offsetHeight;

        for (let i = 0; i < rows; i++) {
          for (let j = 0; j < cols; j++) {
            if (this.hood[i][j].type === "filler") {
              ctx.strokeStyle = "green";
              ctx.strokeRect(j * boxSize, i * boxSize, boxSize, boxSize);
            } else if (this.hood[i][j].type === "residential") {
              ctx.fillStyle = "green";
              ctx.fillRect(j * boxSize, i * boxSize, boxSize, boxSize);
            } else if (this.hood[i][j].type === "industrial") {
              ctx.fillStyle = "#9ACD32";
              ctx.fillRect(j * boxSize, i * boxSize, boxSize, boxSize);
            } else {
              ctx.fillStyle = "black";
              ctx.fillRect(j * boxSize, i * boxSize, boxSize, boxSize);
            }
            ctx.strokeStyle = "black";
            ctx.strokeRect(j * boxSize, i * boxSize, boxSize, boxSize);
          }
        }
      });
    },
    scrollToBottom() {
      this.$refs.terminalBody.scrollTop = this.$refs.terminalBody.scrollHeight;
    },
    processInput() {
      let targetFound = false;
      if (this.input) {
        this.outputs.push("> " + this.input);
        this.inputArray = this.input.split(" ");

        // message += ".";
        // this.outputs.pop();
        // this.outputs.push("Processing...");
        this.processing = false;
        switch (this.inputArray[0]) {
          case "ping":
            if (this.inputArray.length === 1) {
              this.outputs.push("What would you like to ping?");
            } else if (this.location === "matrix") {
              let targetIp = this.inputArray.slice(1).join(" ");
              let target;
              for (let i = 0; i < this.hood.length; i++) {
                for (let j = 0; j < this.hood[i].length; j++) {
                  if (
                    this.hood[i][j].type === "residential" ||
                    this.hood[i][j].type === "industrial"
                  ) {
                    if (this.hood[i][j].ipAddress === targetIp) {
                      target = this.hood[i][j];
                      break;
                    }
                  }
                }
              }
              if (target) {
                this.outputs.push(target)
              } else {
                this.outputs.push("Invalid IP address: " + targetIp);
              }
            } else {
              let targetName = this.inputArray.slice(1).join(" ").toLowerCase();
              let target = this.targets.find(
                (t) => targetName === t.name.toLowerCase()
              );
              if (target) {
                let jsonString = JSON.stringify(
                  target,
                  function (key, value) {
                    if (key === "utilities") {
                      return undefined;
                    }
                    return value;
                  },
                  4
                );
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
              if (this.location === "matrix") {
                for (let i = 0; i < this.hood.length; i++) {
                  for (let j = 0; j < this.hood[i].length; j++) {
                    if (
                      this.hood[i][j].ipAddress === targetIp &&
                      (this.hood[i][j].type === "industrial" ||
                        this.hood[i][j].type === "residential")
                    ) {
                      targetFound = true;
                      target = this.hood[i][j];
                      break;
                    }
                  }
                }
              } else {
                for (let i = 0; i < this.targets.length; i++) {
                  if (this.targets[i].ipAddress === targetIp) {
                    targetFound = true;
                    target = this.targets[i];
                    break;
                  }
                }
              }
              if (targetFound) {
                this.outputs.push("Attempting hack on: " + target.ipAddress);
                target.hackAttempts += 1
                this.updateCell(target)

                let attack = Math.floor(Math.random() * 10) + 5;
                let die = Math.floor(Math.random() * 4) + 1;
                if (attack - die > target.defense) {
                  this.outputs.push("You have successfully hacked " + target.ipAddress);
                  if (this.location !== "matrix") {
                    this.outputs.push("Utilities: " + target.utilities.join(", "));
                  } else {
                    target.hacked = true
                    this.updateCell(target)
                    this.startAnimation()
                  }
                } else {
                  this.outputs.push("You failed to hack " + target.ipAddress);
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
            this.outputs = [];
            break;
          case "ls":
            if (this.location === "matrix") {
              let hoodCells = this.hood.map((row) =>
                row.filter(
                  (cell) =>
                    cell.type === "residential" || cell.type === "industrial"
                )
              ).flat();
              let jsonString = JSON.stringify(
                hoodCells,
                function (key, value) {
                  if (key === "defense") {
                    return undefined;
                  }
                  return value;
                },
                4
              );
              this.outputs.push(jsonString);
            } else {
              this.outputs.push("Invalid location: " + this.location);
            }
            break;
            this.outputs.push(`Invalid Command: ${this.input}`);
            break;
        }
        this.input = "";
      }
      this.scrollToBottom();
    },
  },
};
</script>