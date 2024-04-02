<template>
  <div class="app">
    <h1>Motor Control</h1>

    <div class="slider-container">
      <label for="motor-percent">Motor Percent:</label>
      <input
        type="range"
        id="motor-percent"
        v-model="motorPercent"
        min="0"
        max="100"
        @input="calcRatios"
      />
      <span>{{ motorPercent }}%</span>
    </div>

    <div class="output-container">
      <div class="output-item">
        <label>RPM Input: </label>
        <span>{{ calcInputRPM().toFixed(1) }}</span>
      </div>
      <div class="output-item">
        <label>Torque Input: </label>
        <span>{{ calcInputTorque().toFixed(2) }}</span>
        <label> N•m</label>
      </div>
    </div>

    <div class="graph-container">
      <div class="graph">
        <div class="rpm-bar" :style="{ width: calcInputRPMPercent() + '%' }"></div>
      </div>
      <div class="graph">
        <div class="torque-bar" :style="{ width: calcInputTorquePercent() + '%' }"></div>
      </div>
    </div>


    <div class="gear-reductions">
      <h1>Gear Reductions</h1>
      <div class="gear-input">
        <label for="planetaries">Planetaries: </label>
        <input
          type="ratioText"
          id="planetBox1"
          v-model="planetBox1"
          @input="calcRatios"
        />
        <span> : </span>
        <input
          type="ratioText"
          id="planetBox2"
          v-model="planetBox2"
          @input="calcRatios"
        />
        <span><b> = {{ planetariesRatio.toFixed(2) }}</b></span>
      </div>


      <div class="gear-input">
        <label for="sprockets">Sprockets: </label>
        <input
          type="ratioText"
          id="sprocketBox1"
          v-model="sprocketBox1"
          @input="calcRatios"
        />
        <span> : </span>
        <input
          type="ratioText"
          id="sprocketBox2"
          v-model="sprocketBox2"
          @input="calcRatios"
        />
        <span><b> = {{ sprocketsRatio.toFixed(2) }}</b></span>
      </div>

      <label for="total-ratio">Total Reduction Ratio: </label>
      <span><b>{{ totalRatio.toFixed(2) }}</b></span>
      <label><b> : 1</b></label>
    </div>
  


    <h1>Arm Output</h1>

    <div class="output-container">
      <div class="output-item">
        <label>RPM Output: </label>
        <span>{{ calcRatiodRPM().toFixed(1) }}</span>
      </div>
      <div class="output-item">
        <label>Torque Output: </label>
        <span>{{ calcRatiodTorque().toFixed(2) }}</span>
        <label> N•m</label>
      </div>
    </div>

    <div class="graph-container">
      <div class="graph">
        <div class="rpm-bar" :style="{ width: calcOutputRPMPercent() + '%' }"></div>
      </div>
      <div class="graph">
        <div class="torque-bar" :style="{ width: calcOutputTorquePercent() + '%' }"></div>
      </div>
    </div>

    <label>Time taken to rotate 90º: </label>
    <span><b>{{ timeRotate90.toFixed(1) }}</b></span>
    <label>s</label>


    <h1>Can Lift Arm?</h1>

    <label>Minimum Torque Required: <b>3.277 N•m</b></label>
    <div class="graph-container">
      <div class="gradient-box" :style="boxStyle"></div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      motorPercent: 10,
      planetariesRatio: 1,
      sprocketsRatio: 1,
      totalRatio: 1,
      timeRotate90: 0,
      planetBox1: 196,
      planetBox2: 1,
      sprocketBox1: 42,
      sprocketBox2: 15,
    };
  },
  computed: {
    boxStyle() {
      // let motorPercent = this.motorPercent;
      let torque = this.calcRatiodTorque();
      let backgroundColor;

      /*
      if (motorPercent < 10) {
        // deep red to orange gradient
        let redValue = 255;
        let greenValue = 150 * (motorPercent / 10);
        let blueValue = 0;
        backgroundColor = `rgb(${redValue}, ${greenValue}, ${blueValue})`;
      } else if (motorPercent < 50) {
        // orange to deep green gradient
        let redValue = 255 * (100 - ((motorPercent - 10) * 1.25) / 100);
        let greenValue = 150 + ((motorPercent - 10) * 1.25);
        let blueValue = 0;
        backgroundColor = `rgb(${redValue}, ${greenValue}, ${blueValue})`;
      } else {
        // deep green to dark purple gradient
        let redValue = 150 * ((motorPercent - 50) * 2) / 100;
        let greenValue = 200 * (100 - ((motorPercent - 50) * 2) / 100);
        let blueValue = 255 * (((motorPercent - 50) * 2) / 100);
        backgroundColor = `rgb(${redValue}, ${greenValue}, ${blueValue})`;
      }
      */

      // MAKE MORE MOTORS
      // MAKE GRAPH
      // MAKE TIME TAKEN TO GO 90º

      if (torque > 3.227) {
        backgroundColor = `rgb(0, 200, 0)`;
      } else {
        backgroundColor = `rgb(255, 0, 0)`;
      }

      return { backgroundColor };
    }
  },
  methods: {
    // math equations for applying ratios
    calcRatiodTorque() {
      return this.calcInputTorque() * this.totalRatio;
    },
    calcRatiodRPM() {
      return this.calcInputRPM() / this.totalRatio;
    },
    getMaxRatiodTorque() {
      return 2.41 * this.totalRatio;
    },
    getMaxRatiodRPM() {
      return 5330 / this.totalRatio;
    },  
    // input methods
    calcInputRPM() {
      return 5330 * (this.motorPercent / 100);
    },
    calcInputRPMPercent() {
      return (this.calcInputRPM() / 5330) * 100;
    },
    calcInputTorque() {
      return 2.41 * (1 - this.motorPercent / 100);
    },
    calcInputTorquePercent() {
      return (this.calcInputTorque() / 2.41) * 100;
    },
    // output methods
    calcOutputRPMPercent() {
      return (this.calcRatiodRPM() / this.getMaxRatiodRPM()) * 100;
    },
    calcOutputTorquePercent() {
      return (this.calcRatiodTorque() / this.getMaxRatiodTorque()) * 100;
    },
    // ratio methods
    calculatePlanetaries() {
      this.planetariesRatio = this.planetBox1 / this.planetBox2;
    },
    calculateSprockets() {
      this.sprocketsRatio = this.sprocketBox1 / this.sprocketBox2;
    },
    calculateTotalRatio() {
      this.totalRatio = this.planetariesRatio * this.sprocketsRatio;
    },
    calcRatios() {
      this.calculatePlanetaries();
      this.calculateSprockets();
      this.calculateTotalRatio();
      this.calcTimeRotate90();
    },
    // time method
    calcTimeRotate90() {
      this.timeRotate90 = (90 / this.calcRatiodRPM()) * (1 / 360) * 60;
    }
  },
  // method to call when app opened
  created() {
      this.calculatePlanetaries();
      this.calculateSprockets();
      this.calculateTotalRatio();
      this.calcTimeRotate90();
    }
};
</script>

<style scoped>
.app {
  text-align: center;
}

.slider-container {
  margin: 20px 0;
  display: flex;
  align-items: center;
  justify-content: center;
}

.slider-container input {
  width: 200px;
  margin: 0 10px;
}

.output-container {
  display: flex;
  justify-content: center;
}

.output-item {
  margin: 0 20px;
}

.graph-container {
  display: flex;
  justify-content: center;
}

.graph {
  width: 200px;
  height: 20px;
  background-color: #ccc;
  margin: 0 10px;
  position: relative;
}

.rpm-bar,
.torque-bar {
  height: 100%;
  background-color: #0077c0;
  transition: width 0.5s;
}

.gradient-box {
  width: 100px;
  height: 100px;
}

input[type="ratioText"] {
  width: 40px; /* Adjust the width as needed */
  height: 20px; /* Adjust the height as needed */
}
</style>
