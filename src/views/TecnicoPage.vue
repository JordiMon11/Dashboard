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
              <div class="metric-icon">⏱</div>
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
        <!-- Fila 2: Tráfico por fuente + Embudo -->
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
        <!-- Fila 3: Dispositivos + Velocidad + Errores -->
        <ion-row class="ion-row-3">
          <ion-col size="12" size-lg="4">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title"> Dispositivos</span>
                <span class="chart-subtitle">Sesiones por tipo de dispositivo</span>
              </div>
              <vapexChart type="donut" height="85%" :options="deviceOptions" :series="deviceSeries" />
            </div>
          </ion-col>
          <ion-col size="12" size-lg="4">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title"> Velocidad de Carga</span>
                <span class="chart-subtitle">Tiempo de respuesta (ms) semanal</span>
              </div>
              <vapexChart type="line" height="85%" :options="speedOptions" :series="speedSeries" />
            </div>
          </ion-col>
          <ion-col size="12" size-lg="4">
            <div class="box box-chart">
              <div class="chart-header">
                <span class="chart-title"> Errores HTTP</span>
                <span class="chart-subtitle">Errores 4xx y 5xx por semana</span>
              </div>
              <vapexChart type="bar" height="85%" :options="errorOptions" :series="errorSeries" />
            </div>
          </ion-col>
        </ion-row>
      </ion-grid>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar, IonGrid, IonRow, IonCol } from '@ionic/vue';
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

const deviceSeries = [58, 32, 10];
const deviceOptions = {
  chart: { type: 'donut', background: 'transparent' },
  theme: { mode: 'dark' },
  labels: ['Móvil', 'Escritorio', 'Tablet'],
  colors: ['#4f9cf9','#3ecf8e','#f5a623'],
  legend: { position: 'bottom', labels: { colors: '#ccc' } },
  plotOptions: { pie: { donut: { size: '60%' } } },
  tooltip: { theme: 'dark' },
};

const speedSeries = [{ name: 'Tiempo (ms)', data: [320,295,340,280,315,298,265,310,285,271,258,242] }];
const speedOptions = {
  chart: { type: 'line', background: 'transparent', toolbar: { show: false } },
  theme: { mode: 'dark' },
  colors: ['#a78bfa'],
  stroke: { curve: 'smooth', width: 3 },
  markers: { size: 4, colors: ['#a78bfa'] },
  dataLabels: { enabled: false },
  xaxis: { categories: ['Ene','Feb','Mar','Abr','May','Jun','Jul','Ago','Sep','Oct','Nov','Dic'], labels: { style: { colors: '#aaa' } } },
  yaxis: { labels: { style: { colors: '#aaa' }, formatter: (v: number) => `${v}ms` } },
  grid: { borderColor: '#333' },
  tooltip: { theme: 'dark', y: { formatter: (v: number) => `${v} ms` } },
};

const errorSeries = [
  { name: 'Errores 4xx', data: [12,8,15,6,10,7,9,5,8,4,6,3] },
  { name: 'Errores 5xx', data: [3,2,5,1,4,2,3,1,2,1,2,0] },
];
const errorOptions = {
  chart: { type: 'bar', background: 'transparent', toolbar: { show: false } },
  theme: { mode: 'dark' },
  colors: ['#f5a623','#e879a0'],
  plotOptions: { bar: { borderRadius: 4, columnWidth: '65%' } },
  dataLabels: { enabled: false },
  xaxis: { categories: ['Ene','Feb','Mar','Abr','May','Jun','Jul','Ago','Sep','Oct','Nov','Dic'], labels: { style: { colors: '#aaa' } } },
  yaxis: { labels: { style: { colors: '#aaa' } } },
  legend: { labels: { colors: '#ccc' } },
  grid: { borderColor: '#333' },
  tooltip: { theme: 'dark' },
};
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
.metric-delta.negative { color: #3ecf8e; }
@media (min-width: 992px) {
  ion-grid { height: 100%; }
  .ion-row-1 { height: 20%; max-height: 20%; }
  .ion-row-2 { height: 40%; max-height: 40%; }
  .ion-row-3 { height: 40%; max-height: 40%; }
}
</style>
