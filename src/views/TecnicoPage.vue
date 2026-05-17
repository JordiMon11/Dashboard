<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title>Técnico</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" class="ion-padding">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Técnico</ion-title>
        </ion-toolbar>
      </ion-header>

      <ion-grid class="dashboard-grid">

        <!-- Fila 1: 3 métricas radial (ApexCharts) + 1 gráfico propio SVG -->
        <ion-row class="ion-row-1">
          <ion-col size="6" size-lg="3">
            <div class="box metric-box">
              <div class="metric-info">
                <span class="metric-label">Tasa de Conversión</span>
                <span class="metric-value">3,02%</span>
                <span class="metric-delta positive">+0.4% vs mes anterior</span>
              </div>
              <!-- PUNTO 3: ApexCharts -->
              <vapexChart type="radialBar" height="120" :options="radialConv" :series="seriesConv" />
            </div>
          </ion-col>
          <ion-col size="6" size-lg="3">
            <div class="box metric-box">
              <div class="metric-info">
                <span class="metric-label">Tiempo Medio en Web</span>
                <span class="metric-value">4m 22s</span>
                <span class="metric-delta positive">+18s vs mes anterior</span>
              </div>
              <!-- PUNTO 3: ApexCharts -->
              <vapexChart type="radialBar" height="120" :options="radialTime" :series="seriesTime" />
            </div>
          </ion-col>
          <ion-col size="6" size-lg="3">
            <div class="box metric-box">
              <div class="metric-info">
                <span class="metric-label">Tasa de Rebote</span>
                <span class="metric-value">38,7%</span>
                <span class="metric-delta negative">-2.1% vs mes anterior</span>
              </div>
              <!-- PUNTO 3: ApexCharts -->
              <vapexChart type="radialBar" height="120" :options="radialBounce" :series="seriesBounce" />
            </div>
          </ion-col>
          <ion-col size="6" size-lg="3">
            <div class="box metric-box-own">
              <span class="metric-label">Cobertura de Tests</span>
              <!-- PUNTO 1: Gráfico propio — barras CSS puras sin librería -->
              <div class="own-top-row">
                <span class="metric-value-own">41%</span>
                <span class="metric-sub-right">objetivo 70%</span>
              </div>
              <div class="own-main-bar">
                <div class="own-main-fill"></div>
                <div class="own-target-line"></div>
              </div>
              <div class="own-bars">
                <div class="own-bar-row" v-for="m in testModules" :key="m.name">
                  <span class="own-bar-label">{{ m.name }}</span>
                  <div class="own-bar-track">
                    <div class="own-bar-fill" :style="{ width: m.pct + '%', background: m.color }"></div>
                  </div>
                  <span class="own-bar-val">{{ m.pct }}%</span>
                </div>
              </div>
              <span class="metric-risk">En riesgo — objetivo: 70%</span>
            </div>
          </ion-col>
        </ion-row>

        <!-- Fila 2: Área (ApexCharts) + Pie (ApexCharts) -->
        <ion-row class="ion-row-2">
          <ion-col size="12" size-md="3" push-md="9">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title">Fuentes de Tráfico</span>
                <span class="chart-subtitle">% visitas por canal</span>
              </div>
              <!-- PUNTO 3: ApexCharts -->
              <vapexChart type="pie" height="85%" :options="pieOptions" :series="pieSeries" />
            </div>
          </ion-col>
          <ion-col size="12" size-md="9" pull-md="3">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title">Visitas Web - Evolución Anual</span>
                <span class="chart-subtitle">Sesiones únicas por mes</span>
              </div>
              <!-- PUNTO 3: ApexCharts -->
              <vapexChart type="area" height="85%" :options="areaOptions" :series="areaSeries" />
            </div>
          </ion-col>
        </ion-row>

        <!-- Fila 3: ECharts (dispositivos) + Chart.js (errores) + Tiempo real -->
        <ion-row class="ion-row-3">
          <ion-col size="12" size-lg="4">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title">Dispositivos</span>
                <span class="chart-subtitle">Sesiones por tipo</span>
              </div>
              <!-- PUNTO 4: ECharts -->
              <div ref="echartsContainer" class="echarts-wrap"></div>
            </div>
          </ion-col>
          <ion-col size="12" size-lg="4">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title">Errores 5xx por Mes</span>
                <span class="chart-subtitle">Actual: 3/mes — Objetivo: menos de 2</span>
              </div>
              <!-- PUNTO 2: Chart.js -->
              <div class="chartjs-wrap">
                <canvas ref="errorsCanvas"></canvas>
              </div>
            </div>
          </ion-col>
          <ion-col size="12" size-lg="4">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title">Velocidad de Carga (Tiempo Real)</span>
                <span class="chart-subtitle">Tiempo de respuesta en ms, actualización cada 2s</span>
              </div>
              <!-- PUNTO 5: Tiempo real -->
              <vapexChart type="line" height="85%" :options="realtimeOptions" :series="realtimeSeries" />
            </div>
          </ion-col>
        </ion-row>

      </ion-grid>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonButtons, IonContent, IonHeader, IonMenuButton,
  IonPage, IonTitle, IonToolbar, IonGrid, IonRow, IonCol,
} from '@ionic/vue';
import { ref, onMounted, onUnmounted, nextTick } from 'vue';
import vapexChart from 'vue3-apexcharts';
import * as echarts from 'echarts';

// ─── PUNTO 1: Gráfico propio — datos para barras SVG de cobertura tests ───────
const testModules = [
  { name: 'Carrito',       pct: 55, color: '#a78bfa' },
  { name: 'Pago',          pct: 38, color: '#e879a0' },
  { name: 'Auth',          pct: 62, color: '#4f9cf9' },
  { name: 'Catálogo',      pct: 28, color: '#f5a623' },
];

// ─── PUNTO 3: ApexCharts — Radial ────────────────────────────────────────────
function makeRadial(color: string, label: string) {
  return {
    chart: { type: 'radialBar', background: 'transparent', sparkline: { enabled: true } },
    theme: { mode: 'dark' },
    plotOptions: {
      radialBar: {
        hollow: { size: '50%' },
        dataLabels: {
          name:  { show: true, color: '#aaa', fontSize: '10px', offsetY: 20 },
          value: { show: true, color: '#fff', fontSize: '14px', fontWeight: 700, offsetY: -10 },
        },
        track: { background: '#333' },
      },
    },
    colors: [color],
    labels: [label],
  };
}

const radialConv   = makeRadial('#3ecf8e', 'Conv.');
const radialTime   = makeRadial('#f5a623', 'Min');
const radialBounce = makeRadial('#e879a0', 'Rebote');
const seriesConv   = [30.2];
const seriesTime   = [72];
const seriesBounce = [38.7];

// ─── PUNTO 3: ApexCharts — Pie ───────────────────────────────────────────────
const pieSeries = [42, 28, 18, 8, 4];
const pieOptions = {
  chart: { type: 'pie', background: 'transparent' },
  theme: { mode: 'dark' },
  labels: ['Orgánico', 'Directo', 'Redes Soc.', 'Email', 'Otros'],
  colors: ['#4f9cf9', '#3ecf8e', '#f5a623', '#e879a0', '#a78bfa'],
  legend: { position: 'bottom', labels: { colors: '#ccc' }, fontSize: '11px' },
  tooltip: { theme: 'dark', y: { formatter: (v: number) => `${v}%` } },
  dataLabels: { style: { fontSize: '11px' } },
};

// ─── PUNTO 3: ApexCharts — Area ──────────────────────────────────────────────
const areaSeries = [
  { name: 'Sesiones',        data: [8200,9500,7800,12000,10500,14000,13200,16800,15400,19200,18100,21000] },
  { name: 'Usuarios únicos', data: [6100,7200,5900,9400,8200,11200,10500,13600,12800,15400,14600,17200] },
];
const areaOptions = {
  chart: { type: 'area', background: 'transparent', toolbar: { show: false } },
  theme: { mode: 'dark' },
  colors: ['#4f9cf9', '#3ecf8e'],
  stroke: { curve: 'smooth', width: 2 },
  fill: { type: 'gradient', gradient: { opacityFrom: 0.4, opacityTo: 0.05 } },
  dataLabels: { enabled: false },
  xaxis: {
    categories: ['Ene','Feb','Mar','Abr','May','Jun','Jul','Ago','Sep','Oct','Nov','Dic'],
    labels: { style: { colors: '#aaa' } },
  },
  yaxis: { labels: { style: { colors: '#aaa' }, formatter: (v: number) => `${(v/1000).toFixed(0)}k` } },
  legend: { labels: { colors: '#ccc' } },
  grid: { borderColor: '#333' },
  tooltip: { theme: 'dark' },
};

// ─── PUNTO 4: ECharts — Dispositivos donut ───────────────────────────────────
const echartsContainer = ref<HTMLElement | null>(null);
let echartsInstance: echarts.ECharts | null = null;

function initECharts() {
  if (!echartsContainer.value) return;
  echartsInstance = echarts.init(echartsContainer.value, 'dark');
  echartsInstance.setOption({
    backgroundColor: 'transparent',
    tooltip: { trigger: 'item' },
    legend: { bottom: 8, textStyle: { color: '#ccc', fontSize: 11 } },
    series: [{
      type: 'pie',
      radius: ['40%', '65%'],
      center: ['50%', '45%'],
      data: [
        { value: 58, name: 'Móvil',      itemStyle: { color: '#4f9cf9' } },
        { value: 32, name: 'Escritorio', itemStyle: { color: '#3ecf8e' } },
        { value: 10, name: 'Tablet',     itemStyle: { color: '#f5a623' } },
      ],
      label: { color: '#ccc', fontSize: 11 },
    }],
  });
  setTimeout(() => echartsInstance?.resize(), 100);
}

const onWindowResize = () => echartsInstance?.resize();

// ─── PUNTO 2: Chart.js — Errores 5xx por mes ─────────────────────────────────
const errorsCanvas = ref<HTMLCanvasElement | null>(null);
let chartjsInstance: any = null;

async function initChartJs() {
  const { Chart, registerables } = await import('chart.js');
  Chart.register(...registerables);
  if (!errorsCanvas.value) return;
  chartjsInstance = new Chart(errorsCanvas.value, {
    type: 'bar',
    data: {
      labels: ['Ene','Feb','Mar','Abr','May','Jun','Jul','Ago','Sep','Oct','Nov','Dic'],
      datasets: [
        {
          label: 'Errores 5xx',
          data: [7,5,8,4,6,5,3,4,3,4,3,3],
          backgroundColor: '#e879a0bb',
          borderColor: '#e879a0',
          borderWidth: 1, borderRadius: 4,
        },
        {
          label: 'Objetivo (<2)',
          data: [2,2,2,2,2,2,2,2,2,2,2,2],
          type: 'line',
          borderColor: '#f5a623',
          borderWidth: 1,
          borderDash: [4,4],
          pointRadius: 0,
          fill: false,
        } as any,
      ]
    },
    options: {
      responsive: true, maintainAspectRatio: false,
      plugins: {
        legend: { labels: { color: '#ccc', font: { size: 11 } } },
        tooltip: { callbacks: { label: (ctx: any) => ` ${ctx.parsed.y} errores` } }
      },
      scales: {
        x: { ticks: { color: '#aaa', font: { size: 10 } }, grid: { color: '#333' } },
        y: { ticks: { color: '#aaa', font: { size: 10 } }, grid: { color: '#333' }, min: 0 }
      }
    }
  });
}

// ─── PUNTO 5: Tiempo real — velocidad de carga ───────────────────────────────
const MAX_POINTS = 20;
const realtimeData = ref<number[]>([
  320,295,340,280,315,298,265,310,285,271,
  258,242,268,244,255,239,261,248,252,242,
]);
const realtimeSeries = ref([{ name: 'Tiempo (ms)', data: [...realtimeData.value] }]);

const realtimeOptions = {
  chart: {
    id: 'realtime-tecnico',
    type: 'line',
    background: 'transparent',
    toolbar: { show: false },
    animations: { enabled: true, easing: 'linear', dynamicAnimation: { speed: 1000 } },
  },
  theme: { mode: 'dark' },
  colors: ['#a78bfa'],
  stroke: { curve: 'smooth', width: 2 },
  markers: { size: 0 },
  dataLabels: { enabled: false },
  xaxis: { labels: { show: false }, range: MAX_POINTS },
  yaxis: {
    min: 200, max: 400,
    labels: { style: { colors: '#aaa' }, formatter: (v: number) => `${v}ms` },
  },
  grid: { borderColor: '#333' },
  tooltip: { theme: 'dark', y: { formatter: (v: number) => `${v} ms` } },
};

let realtimeInterval: ReturnType<typeof setInterval> | null = null;

// ─── Lifecycle ────────────────────────────────────────────────────────────────
onMounted(async () => {
  await nextTick();
  initECharts();
  initChartJs();
  window.addEventListener('resize', onWindowResize);

  realtimeInterval = setInterval(() => {
    const newVal = Math.round(230 + Math.random() * 120);
    realtimeData.value.push(newVal);
    if (realtimeData.value.length > MAX_POINTS) realtimeData.value.shift();
    realtimeSeries.value = [{ name: 'Tiempo (ms)', data: [...realtimeData.value] }];
  }, 2000);
});

onUnmounted(() => {
  if (realtimeInterval) clearInterval(realtimeInterval);
  window.removeEventListener('resize', onWindowResize);
  if (chartjsInstance) chartjsInstance.destroy();
  if (echartsInstance) { echartsInstance.dispose(); echartsInstance = null; }
});
</script>

<style scoped>
ion-row { overflow: hidden; }
ion-col { max-height: 100%; --ion-grid-column-padding: 8px; }

.box {
  background: #1a1a2e;
  height: 100%;
  max-height: 100%;
  overflow: hidden;
  border-radius: 10px;
  border: 1px solid #2a2a4a;
}
.box-chart {
  display: flex;
  flex-direction: column;
  padding: 12px 8px 4px 8px;
}
.chart-header {
  display: flex;
  flex-direction: column;
  gap: 2px;
  padding: 0 8px 8px 8px;
}
.chart-title    { font-size: 0.9rem; font-weight: 600; color: #e0e0e0; }
.chart-subtitle { font-size: 0.72rem; color: #888; }

/* ─── Metric box ApexCharts ─── */
.metric-box {
  display: flex;
  flex-direction: row;
  align-items: center;
  padding: 12px 16px;
  gap: 12px;
  justify-content: space-between;
}
.metric-info  { display: flex; flex-direction: column; flex: 1; }
.metric-label { font-size: 0.72rem; color: #888; }
.metric-value { font-size: 1.4rem; font-weight: 700; color: #e0e0e0; }
.metric-delta { font-size: 0.7rem; }
.metric-delta.positive { color: #3ecf8e; }
.metric-delta.negative { color: #e879a0; }

/* ─── PUNTO 1: Metric box gráfico propio ─── */
.metric-box-own {
  display: flex;
  flex-direction: column;
  padding: 14px 14px 12px;
  gap: 8px;
  height: 100%;
}
.metric-value-own { font-size: 1.5rem; font-weight: 700; color: #a78bfa; }

.own-top-row { display: flex; align-items: baseline; gap: 8px; }
.metric-sub-right { font-size: 0.7rem; color: #666; }

.own-main-bar {
  position: relative;
  height: 8px;
  background: #2a2a4a;
  border-radius: 999px;
  overflow: visible;
}
.own-main-fill {
  width: 41%;
  height: 100%;
  background: #a78bfa;
  border-radius: 999px;
}
.own-target-line {
  position: absolute;
  top: -3px;
  left: 70%;
  width: 2px;
  height: 14px;
  background: #f5a623;
  border-radius: 1px;
}
.own-target-line::after {
  content: '70%';
  position: absolute;
  top: -14px;
  left: 4px;
  font-size: 0.6rem;
  color: #f5a623;
  white-space: nowrap;
}

.own-bars { display: flex; flex-direction: column; gap: 6px; margin-top: 2px; }
.own-bar-row { display: flex; align-items: center; gap: 8px; }
.own-bar-label { font-size: 0.68rem; color: #888; width: 54px; flex-shrink: 0; }
.own-bar-track {
  flex: 1;
  height: 6px;
  background: #2a2a4a;
  border-radius: 999px;
  overflow: hidden;
}
.own-bar-fill { height: 100%; border-radius: 999px; transition: width 0.4s ease; }
.own-bar-val { font-size: 0.68rem; color: #aaa; width: 28px; text-align: right; flex-shrink: 0; }

.metric-risk { font-size: 0.68rem; color: #e879a0; margin-top: auto; }

/* ─── Chart.js wrap ─── */
.chartjs-wrap {
  flex: 1;
  min-height: 0;
  padding: 4px 8px 8px;
  position: relative;
}
.chartjs-wrap canvas { width: 100% !important; height: 100% !important; }

/* ─── ECharts wrap ─── */
.echarts-wrap {
  flex: 1;
  min-height: 200px;
  width: 100%;
}

@media (min-width: 992px) {
  ion-grid   { height: 100%; }
  .ion-row-1 { height: 22%; max-height: 22%; }
  .ion-row-2 { height: 38%; max-height: 38%; }
  .ion-row-3 { height: 40%; max-height: 40%; }
}
</style>