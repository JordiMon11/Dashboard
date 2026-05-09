<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title> Técnico</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true" class="ion-padding">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large"> Técnico</ion-title>
        </ion-toolbar>
      </ion-header>
      <ion-grid class="dashboard-grid">
        <!-- Fila 1: 3 métricas web -->
        <ion-row class="ion-row-1">
          <ion-col size="12" size-lg="4">
            <div class="box metric-box">
              <div class="metric-icon"></div>
              <div class="metric-info">
                <span class="metric-label">Tasa de Conversión</span>
                <span class="metric-value">3,02%</span>
                <span class="metric-delta positive">▲ +0.4% vs mes anterior</span>
              </div>
              <vapexChart type="radialBar" height="120" :options="radialOpts('#3ecf8e', 'Conv.')" :series="[30.2]" />
            </div>
          </ion-col>
          <ion-col size="6" size-lg="4">
            <div class="box metric-box">
              <div class="metric-icon"></div>
              <div class="metric-info">
                <span class="metric-label">Tiempo Medio en Web</span>
                <span class="metric-value">4m 22s</span>
                <span class="metric-delta positive">▲ +18s vs mes anterior</span>
              </div>
              <vapexChart type="radialBar" height="120" :options="radialOpts('#f5a623', 'Min')" :series="[72]" />
            </div>
          </ion-col>
          <ion-col size="6" size-lg="4">
            <div class="box metric-box">
              <div class="metric-icon"></div>
              <div class="metric-info">
                <span class="metric-label">Tasa de Rebote</span>
                <span class="metric-value">38,7%</span>
                <span class="metric-delta negative">▼ -2.1% vs mes anterior</span>
              </div>
              <vapexChart type="radialBar" height="120" :options="radialOpts('#e879a0', 'Rebote')" :series="[38.7]" />
            </div>
          </ion-col>
        </ion-row>
        <!-- Fila 2: Tráfico por fuente + Evolución anual -->
        <ion-row class="ion-row-2">
          <ion-col size="12" size-md="3" push-md="9">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title">Fuentes de Tráfico</span>
                <span class="chart-subtitle">% visitas por canal</span>
              </div>
              <vapexChart type="pie" height="85%" :options="pieOptions" :series="pieSeries" />
            </div>
          </ion-col>
          <ion-col size="12" size-md="9" pull-md="3">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title"> Visitas Web — Evolución Anual</span>
                <span class="chart-subtitle">Sesiones únicas por mes</span>
              </div>
              <vapexChart type="area" height="85%" :options="areaOptions" :series="areaSeries" />
            </div>
          </ion-col>
        </ion-row>
        <!-- Fila 3: Dispositivos + Velocidad en tiempo real -->
        <ion-row class="ion-row-3">
          <ion-col size="12" size-lg="6">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title"> Dispositivos</span>
                <span class="chart-subtitle">Sesiones por tipo de dispositivo</span>
              </div>
              <div ref="echartsContainer" class="echarts-wrap"></div>
            </div>
          </ion-col>
          <ion-col size="12" size-lg="6">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title"> Velocidad de Carga (Tiempo Real)</span>
                <span class="chart-subtitle">Tiempo de respuesta (ms) — actualización cada 2s</span>
              </div>
              <vapexChart ref="realtimeChartRef" type="line" height="85%" :options="realtimeOptions" :series="realtimeSeries" />
            </div>
          </ion-col>
        </ion-row>
      </ion-grid>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar, IonGrid, IonRow, IonCol } from '@ionic/vue';
import { ref, onMounted, onUnmounted } from 'vue';
import vapexChart from 'vue3-apexcharts';

const radialOpts = (color: string, label: string) => ({
  chart: { type: 'radialBar', background: 'transparent', sparkline: { enabled: true } },
  theme: { mode: 'dark' },
  plotOptions: { radialBar: { hollow: { size: '50%' }, dataLabels: { name: { show: true, color: '#aaa', fontSize: '10px', offsetY: 20 }, value: { show: true, color: '#fff', fontSize: '14px', fontWeight: 700, offsetY: -10 } }, track: { background: '#333' } } },
  colors: [color],
  labels: [label],
});

const pieSeries = [42, 28, 18, 8, 4];
const pieOptions = {
  chart: { type: 'pie', background: 'transparent' },
  theme: { mode: 'dark' },
  labels: ['Orgánico', 'Directo', 'Redes Soc.', 'Email', 'Otros'],
  colors: ['#4f9cf9','#3ecf8e','#f5a623','#e879a0','#a78bfa'],
  legend: { position: 'bottom', labels: { colors: '#ccc' }, fontSize: '11px' },
  tooltip: { theme: 'dark', y: { formatter: (v: number) => `${v}%` } },
  dataLabels: { style: { fontSize: '11px' } },
};

const areaSeries = [
  { name: 'Sesiones', data: [8200,9500,7800,12000,10500,14000,13200,16800,15400,19200,18100,21000] },
  { name: 'Usuarios únicos', data: [6100,7200,5900,9400,8200,11200,10500,13600,12800,15400,14600,17200] },
];
const areaOptions = {
  chart: { type: 'area', background: 'transparent', toolbar: { show: false } },
  theme: { mode: 'dark' },
  colors: ['#4f9cf9','#3ecf8e'],
  stroke: { curve: 'smooth', width: 2 },
  fill: { type: 'gradient', gradient: { opacityFrom: 0.4, opacityTo: 0.05 } },
  dataLabels: { enabled: false },
  xaxis: { categories: ['Ene','Feb','Mar','Abr','May','Jun','Jul','Ago','Sep','Oct','Nov','Dic'], labels: { style: { colors: '#aaa' } } },
  yaxis: { labels: { style: { colors: '#aaa' }, formatter: (v: number) => `${(v/1000).toFixed(0)}k` } },
  legend: { labels: { colors: '#ccc' } },
  grid: { borderColor: '#333' },
  tooltip: { theme: 'dark' },
};

// ECharts — Dispositivos (cargado desde CDN)
const echartsContainer = ref<HTMLElement | null>(null);
let echartsInstance: any = null;

function loadEChartsScript(src: string): Promise<void> {
  return new Promise((resolve, reject) => {
    if (document.querySelector(`script[src="${src}"]`)) { resolve(); return; }
    const s = document.createElement('script');
    s.src = src; s.onload = () => resolve(); s.onerror = reject;
    document.head.appendChild(s);
  });
}

async function initECharts() {
  await loadEChartsScript('https://cdn.jsdelivr.net/npm/echarts@5/dist/echarts.min.js');
  const echarts = (window as any).echarts;
  if (!echartsContainer.value || !echarts) return;
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
    }]
  });
  window.addEventListener('resize', () => echartsInstance?.resize());
}

// Gráfico en tiempo real
const MAX_POINTS = 20;
const realtimeData = ref<number[]>([320,295,340,280,315,298,265,310,285,271,258,242,268,244,255,239,261,248,252,242]);
const realtimeLabels = ref<string[]>(Array.from({length: 20}, (_,i) => `${i+1}s`));
const realtimeChartRef = ref<any>(null);
const realtimeSeries = ref([{ name: 'Tiempo (ms)', data: [...realtimeData.value] }]);
const realtimeOptions = {
  chart: { id: 'realtime', type: 'line', background: 'transparent', toolbar: { show: false }, animations: { enabled: true, easing: 'linear', dynamicAnimation: { speed: 1000 } } },
  theme: { mode: 'dark' },
  colors: ['#a78bfa'],
  stroke: { curve: 'smooth', width: 2 },
  markers: { size: 0 },
  dataLabels: { enabled: false },
  xaxis: { categories: realtimeLabels.value, labels: { style: { colors: '#aaa' } }, range: MAX_POINTS },
  yaxis: { min: 200, max: 400, labels: { style: { colors: '#aaa' }, formatter: (v: number) => `${v}ms` } },
  grid: { borderColor: '#333' },
  tooltip: { theme: 'dark', y: { formatter: (v: number) => `${v} ms` } },
};
let realtimeInterval: ReturnType<typeof setInterval> | null = null;
onMounted(() => {
  initECharts();
  realtimeInterval = setInterval(() => {
    const newVal = Math.round(230 + Math.random() * 120);
    realtimeData.value.push(newVal);
    if (realtimeData.value.length > MAX_POINTS) realtimeData.value.shift();
    realtimeSeries.value = [{ name: 'Tiempo (ms)', data: [...realtimeData.value] }];
  }, 2000);
});
onUnmounted(() => { if (realtimeInterval) clearInterval(realtimeInterval); if (echartsInstance) echartsInstance.dispose(); });
</script>

<style scoped>
ion-row { overflow: hidden; }
ion-col { max-height: 100%; --ion-grid-column-padding: 8px; }
.box { background: #1a1a2e; height: 100%; max-height: 100%; overflow: hidden; border-radius: 10px; border: 1px solid #2a2a4a; }
.box-chart { display: flex; flex-direction: column; padding: 12px 8px 4px 8px; }
.chart-header { display: flex; flex-direction: column; gap: 2px; padding: 0 8px 8px 8px; }
.chart-title { font-size: 0.9rem; font-weight: 600; color: #e0e0e0; }
.chart-subtitle { font-size: 0.72rem; color: #888; }
.metric-box { display: flex; flex-direction: row; align-items: center; padding: 12px 16px; gap: 12px; justify-content: space-between; }
.metric-icon { font-size: 2rem; }
.metric-info { display: flex; flex-direction: column; flex: 1; }
.metric-label { font-size: 0.72rem; color: #888; }
.metric-value { font-size: 1.6rem; font-weight: 700; color: #e0e0e0; }
.metric-delta { font-size: 0.7rem; }
.metric-delta.positive { color: #3ecf8e; }
.metric-delta.negative { color: #e879a0; }
.echarts-wrap { flex: 1; min-height: 0; }
@media (min-width: 992px) {
  ion-grid { height: 100%; }
  .ion-row-1 { height: 20%; max-height: 20%; }
  .ion-row-2 { height: 40%; max-height: 40%; }
  .ion-row-3 { height: 40%; max-height: 40%; }
}
</style>