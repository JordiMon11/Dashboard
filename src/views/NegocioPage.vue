<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title>Negocio</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true" class="ion-padding">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Negocio</ion-title>
        </ion-toolbar>
      </ion-header>

      <ion-grid class="dashboard-grid">

        <!-- Fila 1: 4 KPI Cards con gráfico PROPIO (SVG sparkline sin librería) -->
        <ion-row class="ion-row-1">
          <ion-col size="6" size-lg="3" v-for="kpi in kpiCards" :key="kpi.id">
            <div class="box kpi-card-box">
              <div class="kpi-card-info">
                <span class="kpi-card-label">{{ kpi.label }}</span>
                <span class="kpi-card-value">{{ kpi.value }}</span>
                <span class="kpi-card-delta" :class="kpi.deltaType">{{ kpi.delta }}</span>
              </div>
              <!-- PUNTO 1: Gráfico propio — SVG sparkline sin ninguna librería -->
              <svg class="sparkline-svg" viewBox="0 0 100 40" preserveAspectRatio="none">
                <defs>
                  <linearGradient :id="'grad-' + kpi.id" x1="0" y1="0" x2="0" y2="1">
                    <stop offset="0%" :stop-color="kpi.color" stop-opacity="0.5"/>
                    <stop offset="100%" :stop-color="kpi.color" stop-opacity="0"/>
                  </linearGradient>
                </defs>
                <path :d="buildSparkArea(kpi.data)" :fill="'url(#grad-' + kpi.id + ')'" />
                <polyline :points="buildSparkLine(kpi.data)" :stroke="kpi.color" stroke-width="1.5" fill="none" stroke-linecap="round" stroke-linejoin="round"/>
              </svg>
            </div>
          </ion-col>
        </ion-row>

        <!-- Fila 2: Barras ApexCharts + Donut ApexCharts -->
        <ion-row class="ion-row-2">
          <ion-col size="12" size-lg="8">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title">Ventas Mensuales por Categoría</span>
                <span class="chart-subtitle">Enero – Diciembre 2025</span>
              </div>
              <!-- PUNTO 3: ApexCharts -->
              <vapexChart type="bar" height="85%" :options="barChartOptions" :series="barChartSeries" />
            </div>
          </ion-col>
          <ion-col size="12" size-lg="4">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title">Ventas por Categoría</span>
                <span class="chart-subtitle">Participación % anual</span>
              </div>
              <!-- PUNTO 3: ApexCharts -->
              <vapexChart type="donut" height="85%" :options="donutOptions" :series="donutSeries" />
            </div>
          </ion-col>
        </ion-row>

        <!-- Fila 3: Chart.js (Top Productos) + ECharts (Heatmap) + Tiempo Real -->
        <ion-row class="ion-row-3">
          <ion-col size="12" size-lg="4">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title">Top 5 Productos</span>
                <span class="chart-subtitle">Unidades vendidas en 2025</span>
              </div>
              <!-- PUNTO 2: Chart.js -->
              <div class="chartjs-wrap">
                <canvas ref="topProductsCanvas"></canvas>
              </div>
            </div>
          </ion-col>
          <ion-col size="12" size-lg="4">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title">Pedidos por Hora/Día</span>
                <span class="chart-subtitle">Distribución semanal</span>
              </div>
              <!-- PUNTO 4: ECharts — heatmap -->
              <div ref="echartsContainer" class="echarts-wrap"></div>
            </div>
          </ion-col>
          <ion-col size="12" size-lg="4">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title">Ventas en Tiempo Real</span>
                <span class="chart-subtitle">Pedidos acumulados hoy, actualización cada 3s</span>
              </div>
              <!-- PUNTO 5: Tiempo real con ApexCharts -->
              <vapexChart type="area" height="85%" :options="realtimeOptions" :series="realtimeSeries" />
            </div>
          </ion-col>
        </ion-row>

      </ion-grid>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, nextTick } from 'vue';
import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar, IonGrid, IonRow, IonCol } from '@ionic/vue';
import vapexChart from 'vue3-apexcharts';
import * as echarts from 'echarts';

// ─── PUNTO 1: Gráfico propio — helpers para SVG sparkline ────────────────────
function buildSparkLine(data: number[]): string {
  const max = Math.max(...data), min = Math.min(...data);
  const range = max - min || 1;
  return data.map((v, i) => {
    const x = (i / (data.length - 1)) * 100;
    const y = 38 - ((v - min) / range) * 34;
    return `${x.toFixed(1)},${y.toFixed(1)}`;
  }).join(' ');
}
function buildSparkArea(data: number[]): string {
  const pts = buildSparkLine(data).split(' ');
  return `M0,40 L${pts.join(' L')} L100,40 Z`;
}

const kpiCards = ref([
  { id: 1, label: 'Ventas Totales',  value: '248.500 EUR', delta: '+3,5% vs objetivo', deltaType: 'positive', color: '#3ecf8e', data: [21,28,19,35,22,41,38,52,44,61,57,69] },
  { id: 2, label: 'Pedidos',         value: '3.841',       delta: '+12% vs año ant.', deltaType: 'positive', color: '#4f9cf9', data: [180,220,195,310,260,380,340,420,390,510,480,556] },
  { id: 3, label: 'Visitantes Web',  value: '127.340',     delta: '+8% vs año ant.',  deltaType: 'positive', color: '#f5a623', data: [82,95,78,120,105,140,132,168,154,192,181,210] },
  { id: 4, label: 'Clientes Nuevos', value: '1.204',       delta: '80% del objetivo', deltaType: 'neutral',  color: '#a78bfa', data: [65,80,72,110,95,130,118,145,132,165,152,184] },
]);

// ─── PUNTO 3: ApexCharts — Barras ────────────────────────────────────────────
const barChartSeries = [
  { name: 'Camisetas',  data: [8200,9100,7400,11200,12400,15600,18200,17100,14300,12800,11200,13400] },
  { name: 'Pantalones', data: [6100,7200,5900,8400,9200,10800,12400,11600,9800,8900,8200,9600] },
  { name: 'Calzado',    data: [4300,5100,4800,6200,7100,8400,9200,8600,7400,6800,6100,7200] },
  { name: 'Accesorios', data: [2100,2800,2400,3600,4200,5100,5800,5400,4600,4100,3800,4400] },
];
const barChartOptions = {
  chart: { type: 'bar', stacked: true, background: 'transparent', toolbar: { show: false } },
  theme: { mode: 'dark' },
  colors: ['#4f9cf9','#3ecf8e','#f5a623','#e879a0'],
  plotOptions: { bar: { borderRadius: 4, columnWidth: '60%' } },
  dataLabels: { enabled: false },
  xaxis: { categories: ['Ene','Feb','Mar','Abr','May','Jun','Jul','Ago','Sep','Oct','Nov','Dic'], labels: { style: { colors: '#aaa' } } },
  yaxis: { labels: { style: { colors: '#aaa' }, formatter: (v: number) => `${(v/1000).toFixed(0)}k` } },
  legend: { position: 'top', labels: { colors: '#ccc' } },
  grid: { borderColor: '#333' },
  tooltip: { theme: 'dark', y: { formatter: (v: number) => `${v.toLocaleString()} EUR` } },
};

// ─── PUNTO 3: ApexCharts — Donut ─────────────────────────────────────────────
const donutSeries = [38, 28, 21, 13];
const donutOptions = {
  chart: { type: 'donut', background: 'transparent' },
  theme: { mode: 'dark' },
  labels: ['Camisetas', 'Pantalones', 'Calzado', 'Accesorios'],
  colors: ['#4f9cf9','#3ecf8e','#f5a623','#e879a0'],
  legend: { position: 'bottom', labels: { colors: '#ccc' } },
  plotOptions: { pie: { donut: { size: '65%', labels: { show: true, total: { show: true, label: 'Total', color: '#aaa', formatter: () => '248.5k' } } } } },
  tooltip: { theme: 'dark', y: { formatter: (v: number) => `${v}%` } },
};

// ─── PUNTO 2: Chart.js — Top 5 Productos ─────────────────────────────────────
const topProductsCanvas = ref<HTMLCanvasElement | null>(null);
let chartjsInstance: any = null;

async function initChartJs() {
  const { Chart, registerables } = await import('chart.js');
  Chart.register(...registerables);
  if (!topProductsCanvas.value) return;
  chartjsInstance = new Chart(topProductsCanvas.value, {
    type: 'bar',
    data: {
      labels: ['Chaqueta Denim', 'Leggings Sport', 'Sneakers Urban', 'Camiseta Premium', 'Hoodie Unisex'],
      datasets: [{
        label: 'Unidades vendidas',
        data: [1842, 1634, 1521, 1398, 1247],
        backgroundColor: ['#4f9cf9bb','#3ecf8ebb','#f5a623bb','#e879a0bb','#a78bfabb'],
        borderColor:     ['#4f9cf9',  '#3ecf8e',  '#f5a623',  '#e879a0',  '#a78bfa'],
        borderWidth: 1, borderRadius: 6,
      }]
    },
    options: {
      responsive: true, maintainAspectRatio: false, indexAxis: 'y',
      plugins: {
        legend: { display: false },
        tooltip: { callbacks: { label: (ctx: any) => ` ${ctx.parsed.x.toLocaleString()} uds` } }
      },
      scales: {
        x: { ticks: { color: '#aaa', font: { size: 11 } }, grid: { color: '#333' } },
        y: { ticks: { color: '#ccc', font: { size: 11 } }, grid: { color: '#222' } }
      }
    }
  });
}

// ─── PUNTO 4: ECharts — Heatmap pedidos por hora/día ─────────────────────────
const echartsContainer = ref<HTMLElement | null>(null);
let echartsInstance: echarts.ECharts | null = null;

function initECharts() {
  if (!echartsContainer.value) return;
  echartsInstance = echarts.init(echartsContainer.value, 'dark');
  const days = ['Lun','Mar','Mié','Jue','Vie','Sáb','Dom'];
  const hours = ['8h','10h','12h','14h','16h','18h','20h'];
  const raw = [
    [12,28,45,38,52,67,31],[18,35,52,44,59,74,40],[10,22,38,31,45,61,28],
    [15,30,48,40,55,70,35],[22,42,65,58,78,92,55],[35,68,95,88,102,115,78],[28,55,80,72,85,98,62]
  ];
  const data: [number,number,number][] = [];
  raw.forEach((row, di) => row.forEach((v, hi) => data.push([hi, di, v])));
  echartsInstance.setOption({
    backgroundColor: 'transparent',
    tooltip: { formatter: (p: any) => `${days[p.data[1]]} ${hours[p.data[0]]}: ${p.data[2]} pedidos` },
    grid: { left: 40, right: 10, top: 10, bottom: 30 },
    xAxis: { type: 'category', data: hours, axisLabel: { color: '#aaa', fontSize: 10 }, splitLine: { show: false } },
    yAxis: { type: 'category', data: days, axisLabel: { color: '#aaa', fontSize: 10 }, splitLine: { show: false } },
    visualMap: { min: 0, max: 115, calculable: false, show: false, inRange: { color: ['#1a1a2e','#4f9cf9'] } },
    series: [{ type: 'heatmap', data, emphasis: { itemStyle: { shadowBlur: 6 } } }],
  });
  setTimeout(() => echartsInstance?.resize(), 100);
}

const onWindowResize = () => echartsInstance?.resize();

// ─── PUNTO 5: Tiempo real — pedidos acumulados hoy ───────────────────────────
const MAX_POINTS = 20;
const realtimeData = ref<number[]>([
  42,45,47,51,54,58,61,65,68,72,75,79,83,86,90,94,98,102,107,112
]);
const realtimeSeries = ref([{ name: 'Pedidos hoy', data: [...realtimeData.value] }]);

const realtimeOptions = {
  chart: {
    id: 'realtime-negocio',
    type: 'area',
    background: 'transparent',
    toolbar: { show: false },
    animations: { enabled: true, easing: 'linear', dynamicAnimation: { speed: 1000 } },
  },
  theme: { mode: 'dark' },
  colors: ['#4f9cf9'],
  stroke: { curve: 'smooth', width: 2.5 },
  fill: { type: 'gradient', gradient: { shadeIntensity: 1, opacityFrom: 0.4, opacityTo: 0.05, stops: [0, 100] } },
  markers: { size: 0 },
  dataLabels: { enabled: false },
  xaxis: { labels: { show: false }, range: MAX_POINTS, axisBorder: { show: false }, axisTicks: { show: false } },
  yaxis: { min: 30, labels: { style: { colors: '#aaa' }, formatter: (v: number) => `${Math.round(v)}` } },
  grid: { borderColor: '#2a2a4a', padding: { left: 4, right: 4 } },
  tooltip: { theme: 'dark', y: { formatter: (v: number) => `${v} pedidos` } },
};

let realtimeInterval: ReturnType<typeof setInterval> | null = null;

// ─── Lifecycle ────────────────────────────────────────────────────────────────
onMounted(async () => {
  await nextTick();
  initChartJs();
  initECharts();
  window.addEventListener('resize', onWindowResize);

  realtimeInterval = setInterval(() => {
    const last = realtimeData.value[realtimeData.value.length - 1];
    const next = last + Math.round(2 + Math.random() * 6);
    realtimeData.value.push(next);
    if (realtimeData.value.length > MAX_POINTS) realtimeData.value.shift();
    realtimeSeries.value = [{ name: 'Pedidos hoy', data: [...realtimeData.value] }];
  }, 3000);
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

/* ─── KPI card con sparkline propio ─── */
.kpi-card-box {
  display: flex;
  flex-direction: column;
  padding: 12px 14px 8px;
  gap: 6px;
}
.kpi-card-info { display: flex; flex-direction: column; gap: 2px; }
.kpi-card-label { font-size: 0.7rem; color: #888; }
.kpi-card-value { font-size: 1.2rem; font-weight: 700; color: #e0e0e0; }
.kpi-card-delta { font-size: 0.68rem; }
.kpi-card-delta.positive { color: #3ecf8e; }
.kpi-card-delta.negative { color: #e879a0; }
.kpi-card-delta.neutral  { color: #f5a623; }

.sparkline-svg {
  width: 100%;
  height: 40px;
  flex-shrink: 0;
}

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
  min-height: 180px;
  width: 100%;
}

@media (min-width: 992px) {
  ion-grid   { height: 100%; }
  .ion-row-1 { height: 18%; max-height: 18%; }
  .ion-row-2 { height: 42%; max-height: 42%; }
  .ion-row-3 { height: 40%; max-height: 40%; }
}
</style>