<script setup>
</script>

<template>
  <div class="terminal">
    <div class="terminal-body">
      <div v-for="(output, index) in outputs" v-bind:key="index" class="terminal-output">{{ output }}</div>
    </div>
    <div class="terminal-input-container">
      <span class="terminal-prompt">C:\></span>
      <input v-model="input" @keyup.enter="processInput" class="terminal-input" placeholder="Enter command here">
    </div>
  </div>
</template>

<style scoped>
.terminal {
  /* set the background to black */
  background-color: black;
  /* make the terminal take up the full screen */
  height: 100vh;
  width: 100vw;
  /* set padding for the terminal */
  padding: 20px;
  /* use grid layout for the terminal body */
  display: grid;
  grid-template-rows: 1fr;
}

/* make the text bigger */
.terminal-prompt,
.terminal-input,
.terminal-output {
  font-size: 20px;
  /* set the text color to neon green */
  color: #39ff14;
}

/* position the input container at the bottom of the terminal body */
.terminal-input-container {
  grid-row: 2;
  padding-top: 10px;
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
  methods: {
    processInput() {
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
          case "mark":
            let targetMarked = false;
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
          case "hack":
            let targetFound = false;
            for (let i = 0; i < this.targets.length; i++) {
              if (this.inputArray[1] === this.targets[i].name) {
                targetFound = true;
                if (this.targets[i].defense > this.attack) {
                  this.outputs.push(`Hack Failed: ${this.targets[i].name} has a defense of ${this.targets[i].defense}`);
                } else {
                  this.outputs.push(`Hack Successful: ${this.targets[i].name} has a defense of ${this.targets[i].defense}`);
                }
              }
            }
            if (!targetFound) {
              this.outputs.push(`Cannot find target ${this.inputArray[1]}`);
            }
            break;
          default:
            if (this.input === "ls") {
              let targetList = "";
              for (let i = 0; i < this.targets.length; i++) {
                targetList += this.targets[i].name + " ";
              }
              this.outputs.push(targetList);
            } else {
              this.outputs.push(`Invalid Command: ${this.input}`);
            }
            break;
        }
        this.input = "";
      }
    }
  }
};
</script>