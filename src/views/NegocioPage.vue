<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title> Negocio</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true" class="ion-padding">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large"> Negocio</ion-title>
        </ion-toolbar>
      </ion-header>
      <ion-grid class="dashboard-grid">
        <!-- Fila 1: 4 KPI Cards -->
        <ion-row class="ion-row-1">
          <ion-col size="6" size-lg="3">
            <SparkLine title="Ventas Totales" value="€248.500" icon-name="cash-outline" bg-color="gradient-blue" text-color="white" :chart-options="sparkOptions('€')" :chart-series="[{ name: 'Ventas', data: [21000,28000,19000,35000,22000,41000,38000,52000,44000,61000,57000,69000] }]" />
          </ion-col>
          <ion-col size="6" size-lg="3">
            <SparkLine title="Pedidos" value="3.841" icon-name="bag-handle-outline" bg-color="gradient-green" text-color="white" :chart-options="sparkOptions('')" :chart-series="[{ name: 'Pedidos', data: [180,220,195,310,260,380,340,420,390,510,480,556] }]" />
          </ion-col>
          <ion-col size="6" size-lg="3">
            <SparkLine title="Visitantes Web" value="127.340" icon-name="eye-outline" bg-color="gradient-orange" text-color="white" :chart-options="sparkOptions('')" :chart-series="[{ name: 'Visitas', data: [8200,9500,7800,12000,10500,14000,13200,16800,15400,19200,18100,21000] }]" />
          </ion-col>
          <ion-col size="6" size-lg="3">
            <SparkLine title="Clientes Nuevos" value="1.204" icon-name="people-outline" bg-color="gradient-pink" text-color="white" :chart-options="sparkOptions('')" :chart-series="[{ name: 'Clientes', data: [65,80,72,110,95,130,118,145,132,165,152,184] }]" />
          </ion-col>
        </ion-row>
        <!-- Fila 2: Barras + Donut -->
        <ion-row class="ion-row-2">
          <ion-col size="12" size-lg="8">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title"> Ventas Mensuales por Categoría</span>
                <span class="chart-subtitle">Enero – Diciembre 2025</span>
              </div>
              <vapexChart type="bar" height="85%" :options="barChartOptions" :series="barChartSeries" />
            </div>
          </ion-col>
          <ion-col size="12" size-lg="4">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title"> Ventas por Categoría</span>
                <span class="chart-subtitle">Participación % anual</span>
              </div>
              <vapexChart type="donut" height="85%" :options="donutOptions" :series="donutSeries" />
            </div>
          </ion-col>
        </ion-row>
        <!-- Fila 3: Heatmap + Top Productos -->
        <ion-row class="ion-row-3">
          <ion-col size="12" size-lg="5">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title"> Mapa de Calor — Pedidos por Hora/Día</span>
                <span class="chart-subtitle">Distribución semanal de transacciones</span>
              </div>
              <vapexChart type="heatmap" height="85%" :options="heatmapOptions" :series="heatmapSeries" />
            </div>
          </ion-col>
          <ion-col size="12" size-lg="7">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title"> Top 5 Productos más Vendidos</span>
                <span class="chart-subtitle">Unidades vendidas en 2025</span>
              </div>
              <div class="chartjs-wrap">
                <canvas ref="topProductsCanvas" style="width:100%;height:100%;"></canvas>
              </div>
            </div>
          </ion-col>
        </ion-row>

      </ion-grid>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';
import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar, IonGrid, IonRow, IonCol } from '@ionic/vue';
import vapexChart from 'vue3-apexcharts';
import SparkLine from '@/components/SparkLine.vue';
import { addIcons } from 'ionicons';
import { bagHandleOutline, eyeOutline, peopleOutline, cashOutline } from 'ionicons/icons';

addIcons({ 'bag-handle-outline': bagHandleOutline, 'eye-outline': eyeOutline, 'people-outline': peopleOutline, 'cash-outline': cashOutline });

const sparkOptions = (prefix: string) => ({
  chart: { type: 'area', sparkline: { enabled: true }, background: 'transparent' },
  stroke: { curve: 'smooth', width: 2 },
  fill: { opacity: 0.3 },
  colors: ['#ffffff'],
  tooltip: { theme: 'dark', y: { formatter: (v: number) => `${prefix}${v.toLocaleString()}` } }
});

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
  xaxis: { categories: ['Ene','Feb','Mar','Abr','May','Jun','Jul','Ago','Sep','Oct','Nov','Dic'], labels: { style: { colors: '#aaaaaa' } } },
  yaxis: { labels: { style: { colors: '#aaaaaa' }, formatter: (v: number) => `€${(v/1000).toFixed(0)}k` } },
  legend: { position: 'top', labels: { colors: '#cccccc' } },
  grid: { borderColor: '#333' },
  tooltip: { theme: 'dark', y: { formatter: (v: number) => `€${v.toLocaleString()}` } },
};

const donutSeries = [38, 28, 21, 13];
const donutOptions = {
  chart: { type: 'donut', background: 'transparent' },
  theme: { mode: 'dark' },
  labels: ['Camisetas', 'Pantalones', 'Calzado', 'Accesorios'],
  colors: ['#4f9cf9','#3ecf8e','#f5a623','#e879a0'],
  legend: { position: 'bottom', labels: { colors: '#cccccc' } },
  plotOptions: { pie: { donut: { size: '65%', labels: { show: true, total: { show: true, label: 'Total', color: '#aaa', formatter: () => '€248.5k' } } } } },
  tooltip: { theme: 'dark', y: { formatter: (v: number) => `${v}%` } },
};

const heatmapSeries = [
  { name: 'Lun', data: [{x:'8h',y:12},{x:'10h',y:28},{x:'12h',y:45},{x:'14h',y:38},{x:'16h',y:52},{x:'18h',y:67},{x:'20h',y:31}] },
  { name: 'Mar', data: [{x:'8h',y:18},{x:'10h',y:35},{x:'12h',y:52},{x:'14h',y:44},{x:'16h',y:59},{x:'18h',y:74},{x:'20h',y:40}] },
  { name: 'Mié', data: [{x:'8h',y:10},{x:'10h',y:22},{x:'12h',y:38},{x:'14h',y:31},{x:'16h',y:45},{x:'18h',y:61},{x:'20h',y:28}] },
  { name: 'Jue', data: [{x:'8h',y:15},{x:'10h',y:30},{x:'12h',y:48},{x:'14h',y:40},{x:'16h',y:55},{x:'18h',y:70},{x:'20h',y:35}] },
  { name: 'Vie', data: [{x:'8h',y:22},{x:'10h',y:42},{x:'12h',y:65},{x:'14h',y:58},{x:'16h',y:78},{x:'18h',y:92},{x:'20h',y:55}] },
  { name: 'Sáb', data: [{x:'8h',y:35},{x:'10h',y:68},{x:'12h',y:95},{x:'14h',y:88},{x:'16h',y:102},{x:'18h',y:115},{x:'20h',y:78}] },
  { name: 'Dom', data: [{x:'8h',y:28},{x:'10h',y:55},{x:'12h',y:80},{x:'14h',y:72},{x:'16h',y:85},{x:'18h',y:98},{x:'20h',y:62}] },
];
const heatmapOptions = {
  chart: { type: 'heatmap', background: 'transparent', toolbar: { show: false } },
  theme: { mode: 'dark' },
  dataLabels: { enabled: false },
  colors: ['#4f9cf9'],
  xaxis: { labels: { style: { colors: '#aaa' } } },
  yaxis: { labels: { style: { colors: '#aaa' } } },
  grid: { borderColor: '#333' },
  tooltip: { theme: 'dark', y: { formatter: (v: number) => `${v} pedidos` } },
};

// Chart.js — Top 5 Productos (cargado desde CDN)
const topProductsCanvas = ref<HTMLCanvasElement | null>(null);
let chartjsInstance: any = null;

function loadScript(src: string): Promise<void> {
  return new Promise((resolve, reject) => {
    if (document.querySelector(`script[src="${src}"]`)) { resolve(); return; }
    const s = document.createElement('script');
    s.src = src; s.onload = () => resolve(); s.onerror = reject;
    document.head.appendChild(s);
  });
}

async function initChartJs() {
  await loadScript('https://cdn.jsdelivr.net/npm/chart.js@4/dist/chart.umd.min.js');
  const ChartJS = (window as any).Chart;
  if (!topProductsCanvas.value || !ChartJS) return;
  chartjsInstance = new ChartJS(topProductsCanvas.value, {
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
      plugins: { legend: { display: false }, tooltip: { callbacks: { label: (ctx: any) => ` ${ctx.parsed.x.toLocaleString()} uds` } } },
      scales: {
        x: { ticks: { color: '#aaa', font: { size: 11 } }, grid: { color: '#333' } },
        y: { ticks: { color: '#ccc', font: { size: 11 } }, grid: { color: '#222' } }
      }
    }
  });
}
onMounted(() => { initChartJs(); });
onUnmounted(() => { if (chartjsInstance) chartjsInstance.destroy(); });

</script>

<style scoped>
ion-row { overflow: hidden; }
ion-col { max-height: 100%; --ion-grid-column-padding: 8px; }
.box { background: #1a1a2e; height: 100%; max-height: 100%; overflow: hidden; border-radius: 10px; border: 1px solid #2a2a4a; }
.box-chart { display: flex; flex-direction: column; padding: 12px 8px 4px 8px; }
.chart-header { display: flex; flex-direction: column; gap: 2px; padding: 0 8px 8px 8px; }
.chart-title { font-size: 0.9rem; font-weight: 600; color: #e0e0e0; }
.chart-subtitle { font-size: 0.72rem; color: #888; }
.chartjs-wrap { flex: 1; min-height: 0; padding: 4px 8px 8px; position: relative; }
@media (min-width: 992px) {
  ion-grid { height: 100%; }
  .ion-row-1 { height: 20%; max-height: 20%; }
  .ion-row-2 { height: 40%; max-height: 40%; }
  .ion-row-3 { height: 40%; max-height: 40%; }
}
</style>