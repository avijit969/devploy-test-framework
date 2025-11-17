<script setup lang="ts">
import { ref, computed } from "vue";

const height = ref<number | null>(null);
const weight = ref<number | null>(null);
const unit = ref<"metric" | "imperial">("metric");

const bmi = computed(() => {
  if (!height.value || !weight.value) return null;

  if (unit.value === "metric") {
    const heightInMeters = height.value / 100;
    return (weight.value / (heightInMeters * heightInMeters)).toFixed(1);
  } else {
    return ((weight.value / (height.value * height.value)) * 703).toFixed(1);
  }
});

const category = computed(() => {
  const bmiValue = parseFloat(bmi.value || "0");
  if (bmiValue < 18.5) return "Underweight";
  if (bmiValue < 25) return "Normal Weight";
  if (bmiValue < 30) return "Overweight";
  return "Obese";
});

const categoryColor = computed(() => {
  const cat = category.value;
  switch (cat) {
    case "Underweight":
      return "#3b82f6";
    case "Normal Weight":
      return "#10b981";
    case "Overweight":
      return "#f59e0b";
    case "Obese":
      return "#ef4444";
    default:
      return "#6b7280";
  }
});

const reset = () => {
  height.value = null;
  weight.value = null;
};
</script>

<template>
  <div class="container">
    <div class="card">
      <h1 class="title">BMI Calculator</h1>
      <p class="subtitle">Track your Body Mass Index</p>

      <div class="unit-toggle">
        <button
          :class="['toggle-btn', { active: unit === 'metric' }]"
          @click="unit = 'metric'"
        >
          Metric
        </button>
        <button
          :class="['toggle-btn', { active: unit === 'imperial' }]"
          @click="unit = 'imperial'"
        >
          Imperial
        </button>
      </div>

      <div class="input-group">
        <label for="height"
          >Height ({{ unit === "metric" ? "cm" : "inches" }})</label
        >
        <input
          id="height"
          v-model.number="height"
          type="number"
          placeholder="Enter height"
          class="input"
        />
      </div>

      <div class="input-group">
        <label for="weight"
          >Weight ({{ unit === "metric" ? "kg" : "lbs" }})</label
        >
        <input
          id="weight"
          v-model.number="weight"
          type="number"
          placeholder="Enter weight"
          class="input"
        />
      </div>

      <button @click="reset" class="reset-btn">Reset</button>

      <div v-if="bmi" class="result" :style="{ borderColor: categoryColor }">
        <div class="bmi-value" :style="{ color: categoryColor }">{{ bmi }}</div>
        <div class="bmi-category" :style="{ color: categoryColor }">
          {{ category }}
        </div>
        <div class="bmi-info">
          <p><strong>Underweight:</strong> BMI &lt; 18.5</p>
          <p><strong>Normal:</strong> 18.5 - 24.9</p>
          <p><strong>Overweight:</strong> 25 - 29.9</p>
          <p><strong>Obese:</strong> BMI ≥ 30</p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.container {
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}

.card {
  background: white;
  border-radius: 20px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
  padding: 40px;
  max-width: 400px;
  width: 100%;
}

.title {
  font-size: 32px;
  font-weight: 700;
  color: #1f2937;
  margin-bottom: 8px;
  text-align: center;
}

.subtitle {
  color: #6b7280;
  text-align: center;
  margin-bottom: 30px;
  font-size: 14px;
}

.unit-toggle {
  display: flex;
  gap: 10px;
  margin-bottom: 30px;
}

.toggle-btn {
  flex: 1;
  padding: 10px;
  border: 2px solid #e5e7eb;
  background: white;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.3s ease;
  color: #6b7280;
}

.toggle-btn.active {
  background: #667eea;
  color: white;
  border-color: #667eea;
}

.input-group {
  margin-bottom: 20px;
}

label {
  display: block;
  color: #374151;
  font-weight: 600;
  margin-bottom: 8px;
  font-size: 14px;
}

.input {
  width: 100%;
  padding: 12px 15px;
  border: 2px solid #e5e7eb;
  border-radius: 8px;
  font-size: 16px;
  transition: border-color 0.3s ease;
}

.input:focus {
  outline: none;
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.reset-btn {
  width: 100%;
  padding: 12px;
  background: #f3f4f6;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
  color: #6b7280;
  transition: all 0.3s ease;
  margin-bottom: 30px;
}

.reset-btn:hover {
  background: #e5e7eb;
}

.result {
  background: linear-gradient(135deg, #f0f4ff 0%, #f8f0ff 100%);
  border-left: 4px solid;
  border-radius: 12px;
  padding: 25px;
  text-align: center;
}

.bmi-value {
  font-size: 48px;
  font-weight: 700;
  margin-bottom: 10px;
}

.bmi-category {
  font-size: 18px;
  font-weight: 600;
  margin-bottom: 20px;
}

.bmi-info {
  font-size: 12px;
  color: #6b7280;
  line-height: 1.8;
  text-align: left;
}

.bmi-info p {
  margin: 5px 0;
}
</style>
