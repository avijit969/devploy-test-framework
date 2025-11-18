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
    <div class="card" role="main" aria-labelledby="title">
      <header class="card-header">
        <h1 id="title" class="title">BMI Calculator</h1>
        <p class="subtitle">Track your Body Mass Index</p>
      </header>

      <div class="controls">
        <div class="unit-toggle" role="tablist" aria-label="Units">
          <button
            :class="['toggle-btn', { active: unit === 'metric' }]"
            @click="unit = 'metric'"
            role="tab"
            :aria-selected="unit === 'metric'"
          >
            Metric
          </button>
          <button
            :class="['toggle-btn', { active: unit === 'imperial' }]"
            @click="unit = 'imperial'"
            role="tab"
            :aria-selected="unit === 'imperial'"
          >
            Imperial
          </button>
        </div>

        <form class="form-grid" @submit.prevent>
          <div class="input-group">
            <label for="height"
              >Height ({{ unit === "metric" ? "cm" : "in" }})</label
            >
            <input
              id="height"
              v-model.number="height"
              type="number"
              inputmode="decimal"
              placeholder="e.g. 170"
              class="input"
              min="0"
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
              inputmode="decimal"
              placeholder="e.g. 65"
              class="input"
              min="0"
            />
          </div>

          <div class="actions" style="grid-column: 1 / -1">
            <button type="button" @click="reset" class="reset-btn">
              Reset
            </button>
          </div>
        </form>
      </div>

      <section
        v-if="bmi"
        class="result"
        :style="{ borderColor: categoryColor }"
        aria-live="polite"
      >
        <div class="result-grid">
          <div class="result-left">
            <div class="bmi-value" :style="{ color: categoryColor }">
              {{ bmi }}
            </div>
            <div class="bmi-category" :style="{ color: categoryColor }">
              {{ category }}
            </div>
            <div class="bmi-bar" aria-hidden="true">
              <div
                class="bmi-progress"
                :style="{
                  background: categoryColor,
                  width: computeProgress(bmi),
                }"
              ></div>
            </div>
          </div>

          <div class="result-right">
            <div class="bmi-info">
              <p><strong>Underweight:</strong> BMI &lt; 18.5</p>
              <p><strong>Normal:</strong> 18.5 - 24.9</p>
              <p><strong>Overweight:</strong> 25 - 29.9</p>
              <p><strong>Obese:</strong> BMI ≥ 30</p>
            </div>
          </div>
        </div>
      </section>
    </div>
  </div>
</template>

<script lang="ts">
/* small helper outside setup to keep template clean */
export default {
  methods: {
    computeProgress(bmi: string | null) {
      if (!bmi) return "0%";
      const value = Math.min(Math.max(parseFloat(bmi), 10), 40); // clamp
      const percent = ((value - 10) / (40 - 10)) * 100;
      return `${percent}%`;
    },
  },
};
</script>

<style scoped>
:root {
  --bg-start: #667eea;
  --bg-end: #764ba2;
  --card-bg: #ffffff;
  --muted: #6b7280;
  --shadow: 0 18px 50px rgba(15, 23, 42, 0.2);
}

/* reset */
* {
  box-sizing: border-box;
}

.container {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: clamp(16px, 4vw, 48px);
  background: linear-gradient(135deg, var(--bg-start), var(--bg-end));
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}

/* Card */
.card {
  width: 100%;
  max-width: 900px;
  background: var(--card-bg);
  border-radius: 16px;
  box-shadow: var(--shadow);
  padding: clamp(18px, 3.5vw, 40px);
  overflow: hidden;
}

/* Header */
.card-header {
  text-align: center;
  margin-bottom: clamp(12px, 2.5vw, 24px);
}
.title {
  font-size: clamp(20px, 3.6vw, 32px);
  font-weight: 700;
  color: #111827;
  margin-bottom: 6px;
}
.subtitle {
  color: var(--muted);
  font-size: clamp(12px, 1.8vw, 15px);
}

/* Controls */
.controls {
  margin-bottom: clamp(14px, 2.6vw, 26px);
}

/* Unit toggle */
.unit-toggle {
  display: flex;
  gap: 8px;
  justify-content: center;
  margin-bottom: 16px;
  flex-wrap: wrap;
}
.toggle-btn {
  padding: 8px 14px;
  border-radius: 10px;
  border: 2px solid #e6e9ef;
  background: #fff;
  color: var(--muted);
  font-weight: 600;
  cursor: pointer;
  transition: all 0.18s ease;
  min-width: 96px;
  text-align: center;
}
.toggle-btn.active {
  background: linear-gradient(90deg, #5563e3, #7b4bd7);
  color: #fff;
  border: none;
  box-shadow: 0 6px 18px rgba(99, 102, 241, 0.18);
}

/* Form grid: single column on small screens, two columns on medium+ */
.form-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 12px;
}
@media (min-width: 640px) {
  .form-grid {
    grid-template-columns: 1fr 1fr;
    gap: 16px;
    align-items: end;
  }
}

/* Input group */
.input-group label {
  display: block;
  font-weight: 600;
  color: #374151;
  margin-bottom: 6px;
  font-size: 14px;
}
.input {
  width: 100%;
  padding: 12px 14px;
  border-radius: 10px;
  border: 1.6px solid #eef2ff;
  background: #fbfdff;
  font-size: 15px;
  transition: box-shadow 0.16s ease, border-color 0.16s ease;
}
.input:focus {
  outline: none;
  box-shadow: 0 6px 20px rgba(99, 102, 241, 0.08);
  border-color: #667eea;
}

/* Actions */
.actions {
  display: flex;
  justify-content: center;
  gap: 12px;
}
.reset-btn {
  width: 100%;
  padding: 12px;
  border-radius: 10px;
  border: none;
  background: #f3f4f6;
  color: var(--muted);
  font-weight: 700;
  cursor: pointer;
  transition: all 0.14s ease;
}
.reset-btn:hover {
  transform: translateY(-1px);
}

/* Result */
.result {
  margin-top: 18px;
  border-left: 6px solid;
  border-radius: 10px;
  padding: 16px;
  background: linear-gradient(180deg, #fbfdff, #fffbff);
}

/* result layout: stack on mobile, two columns on larger */
.result-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 12px;
  align-items: center;
}
@media (min-width: 700px) {
  .result-grid {
    grid-template-columns: 260px 1fr;
    gap: 20px;
  }
}

.result-left {
  text-align: center;
  padding: 8px;
}
.bmi-value {
  font-size: clamp(28px, 6vw, 48px);
  font-weight: 800;
  letter-spacing: -0.02em;
}
.bmi-category {
  font-size: clamp(14px, 2vw, 18px);
  font-weight: 700;
  margin-top: 6px;
}

/* small progress bar */
.bmi-bar {
  margin-top: 12px;
  height: 10px;
  background: #eef2ff;
  border-radius: 999px;
  overflow: hidden;
}
.bmi-progress {
  height: 100%;
  width: 0%;
  border-radius: 999px;
  transition: width 0.6s cubic-bezier(0.2, 0.9, 0.34, 1);
}

/* Info */
.result-right {
  padding: 8px 6px;
}
.bmi-info {
  font-size: 13px;
  color: var(--muted);
  line-height: 1.6;
}

/* Responsiveness: tweak spacing on very small devices */
@media (max-width: 420px) {
  .card {
    padding: 16px;
  }
  .toggle-btn {
    min-width: 88px;
    padding: 8px 10px;
    font-size: 14px;
  }
  .reset-btn {
    padding: 10px;
  }
}
</style>
