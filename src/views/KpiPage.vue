<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title>KPIs</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" class="ion-padding">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">KPIs</ion-title>
        </ion-toolbar>
      </ion-header>

      <!-- KPIs DE NEGOCIO -->
      <div class="section-header">
        <span class="section-label">KPIs de Negocio</span>
        <span class="section-sub">Objetivos anuales y estado actual</span>
      </div>

  

      <!-- TARJETAS KPI NEGOCIO -->
      <div class="kpi-grid">
        <div class="kpi-card" v-for="kpi in businessKpis" :key="kpi.id">
          <div class="kpi-top">
            <span class="kpi-title">{{ kpi.title }}</span>
            <span class="kpi-badge" :class="kpi.status">{{ kpi.statusLabel }}</span>
          </div>

          <div class="kpi-progress-bar">
            <div
              class="kpi-progress-fill"
              :style="{ width: Math.min(kpi.progress, 100) + '%', background: kpi.color }"
            ></div>
          </div>

          <div class="kpi-bottom">
            <span class="kpi-current">{{ kpi.current }}</span>
            <span class="kpi-target">Meta: {{ kpi.target }}</span>
            <span class="kpi-pct" :style="{ color: kpi.color }">{{ kpi.progress }}%</span>
          </div>

          <p class="kpi-desc">{{ kpi.description }}</p>
        </div>
      </div>

      <!-- ACCORDION SMART NEGOCIO -->
      <div class="section-header" style="margin-top: 8px;">
        <span class="section-label">Objetivos SMART — Negocio</span>
        <span class="section-sub">Desglose detallado por KPI</span>
      </div>

      <ion-accordion-group expand="inset" :multiple="true">
        <ion-accordion v-for="item in businessKpisW" :key="item.id" :value="item.id.toString()">
          <ion-item slot="header">
            <ion-label>{{ item.title }} <strong>{{ item.current }}</strong></ion-label>
          </ion-item>

          <div class="ion-padding acc-content" slot="content">
            <p>{{ item.description }}</p>

            <ion-list :inset="true">
              <ion-item v-for="(element, index) in item.smart" :key="index">
                <ion-label class="smart-item">
                  <span class="smart-letter" :style="{ color: item.color }">{{ element.letter }}</span>
                  <span class="smart-text">{{ element.content }}</span>
                </ion-label>
              </ion-item>
            </ion-list>
          </div>
        </ion-accordion>
      </ion-accordion-group>

      <!-- KPIs TÉCNICOS -->
      <div class="section-header" style="margin-top: 24px;">
        <span class="section-label">KPIs Técnicos</span>
        <span class="section-sub">Rendimiento e infraestructura</span>
      </div>

      <!-- TARJETAS KPI TÉCNICO -->
     <div class="kpi-grid">
      <div class="kpi-card" v-for="t in techKpiCards" :key="t.id">
        <div class="kpi-top">
          <span class="kpi-title">{{ t.title }}</span>
          <span class="kpi-badge" :class="t.status">{{ t.statusLabel }}</span>
        </div>

        <div class="kpi-progress-bar">
          <div
            class="kpi-progress-fill"
            :style="{ width: Math.min(t.progress, 100) + '%', background: t.color }"
          ></div>
        </div>

        <div class="kpi-bottom">
          <span class="kpi-current">{{ t.current }}</span>
          <span class="kpi-target">Meta: {{ t.target }}</span>
          <span class="kpi-pct" :style="{ color: t.color }">{{ t.progress }}%</span>
     </div>

        <p class="kpi-desc">{{ t.description }}</p>
      </div>
    </div>

      <!-- ACCORDION SMART TÉCNICO -->
      <div class="section-header" style="margin-top: 8px;">
        <span class="section-label">Objetivos SMART — Técnico</span>
        <span class="section-sub">Desglose detallado por KPI</span>
      </div>

      <ion-accordion-group expand="inset" :multiple="true">
        <ion-accordion v-for="item in techKpis" :key="item.id" :value="item.id.toString()">
          <ion-item slot="header">
            <ion-label>{{ item.title }} <strong>{{ item.current }}</strong></ion-label>
          </ion-item>

          <div class="ion-padding acc-content" slot="content">
            <p>{{ item.description }}</p>

            <ion-list :inset="true">
              <ion-item v-for="(element, index) in item.smart" :key="index">
                <ion-label class="smart-item">
                  <span class="smart-letter" :style="{ color: item.color }">{{ element.letter }}</span>
                  <span class="smart-text">{{ element.content }}</span>
                </ion-label>
              </ion-item>
            </ion-list>
          </div>
        </ion-accordion>
      </ion-accordion-group>

      <br /><br />
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonButtons,
  IonContent,
  IonHeader,
  IonMenuButton,
  IonPage,
  IonTitle,
  IonToolbar,
  IonAccordionGroup,
  IonAccordion,
  IonItem,
  IonLabel,
  IonList,
} from '@ionic/vue';

import { ref } from 'vue';
import vapexChart from 'vue3-apexcharts';

function makeSparkline(color: string) {
  return {
    chart: {
      type: 'line',
      sparkline: { enabled: true },
      toolbar: { show: false },
      background: 'transparent',
    },
    stroke: {
      curve: 'smooth',
      width: 2,
    },
    colors: [color],
    tooltip: {
      theme: 'dark',
    },
    grid: {
      show: false,
    },
  };
}

/* ─── Dashboard visual superior ─────────────────────────────────────────────── */

const dashboardCards = ref([
  {
    id: 1,
    title: 'Ventas Totales',
    value: '248.500 EUR',
    delta: '+3,5% sobre objetivo',
    status: 'positive',
    options: makeSparkline('#3ecf8e'),
    series: [
      {
        name: 'Ventas',
        data: [18, 21, 19, 24, 20, 28, 27, 32, 29, 36, 35, 40],
      },
    ],
  },
  {
    id: 2,
    title: 'Pedidos Realizados',
    value: '3.841',
    delta: '+10% sobre objetivo',
    status: 'positive',
    options: makeSparkline('#4f9cf9'),
    series: [
      {
        name: 'Pedidos',
        data: [220, 250, 240, 310, 290, 360, 345, 390, 380, 430, 420, 466],
      },
    ],
  },
  {
    id: 3,
    title: 'Visitantes Web',
    value: '127.340',
    delta: '+6% sobre objetivo',
    status: 'positive',
    options: makeSparkline('#f5a623'),
    series: [
      {
        name: 'Visitantes',
        data: [8200, 9100, 8700, 10500, 9900, 11300, 11000, 12400, 12100, 13000, 12800, 13640],
      },
    ],
  },
  {
    id: 4,
    title: 'Clientes Nuevos',
    value: '1.204',
    delta: '80% del objetivo',
    status: 'warning',
    options: makeSparkline('#a78bfa'),
    series: [
      {
        name: 'Clientes',
        data: [72, 85, 80, 96, 90, 105, 101, 116, 112, 123, 119, 105],
      },
    ],
  },
]);

const monthlySalesSeries = ref([
  {
    name: 'Camisetas',
    data: [8, 9, 7, 11, 12, 16, 18, 17, 14, 13, 11, 14],
  },
  {
    name: 'Pantalones',
    data: [6, 7, 6, 9, 10, 11, 12, 11, 10, 9, 8, 9],
  },
  {
    name: 'Calzado',
    data: [4, 5, 4, 6, 7, 8, 9, 8, 7, 7, 6, 8],
  },
  {
    name: 'Accesorios',
    data: [2, 3, 3, 4, 4, 5, 6, 6, 5, 4, 4, 4],
  },
]);

const monthlySalesOptions = ref({
  chart: {
    type: 'bar',
    stacked: true,
    background: 'transparent',
    toolbar: { show: false },
  },
  theme: { mode: 'dark' },
  colors: ['#4f9cf9', '#3ecf8e', '#f5a623', '#e879a0'],
  plotOptions: {
    bar: {
      borderRadius: 4,
      columnWidth: '55%',
    },
  },
  xaxis: {
    categories: ['Ene', 'Feb', 'Mar', 'Abr', 'May', 'Jun', 'Jul', 'Ago', 'Sep', 'Oct', 'Nov', 'Dic'],
    labels: { style: { colors: '#aaa' } },
  },
  yaxis: {
    labels: {
      style: { colors: '#aaa' },
      formatter: (value: number) => `${value}k`,
    },
  },
  grid: {
    borderColor: '#2a2a4a',
  },
  legend: {
    position: 'top',
    labels: { colors: '#aaa' },
  },
  tooltip: {
    theme: 'dark',
    y: {
      formatter: (value: number) => `${value}.000 EUR`,
    },
  },
});

const categoryDonutSeries = ref([38, 28, 21, 13]);

const categoryDonutOptions = ref({
  chart: {
    type: 'donut',
    background: 'transparent',
  },
  theme: { mode: 'dark' },
  labels: ['Camisetas', 'Pantalones', 'Calzado', 'Accesorios'],
  colors: ['#4f9cf9', '#3ecf8e', '#f5a623', '#e879a0'],
  legend: {
    position: 'bottom',
    labels: { colors: '#aaa' },
  },
  plotOptions: {
    pie: {
      donut: {
        size: '62%',
        labels: {
          show: true,
          total: {
            show: true,
            label: 'Total',
            color: '#aaa',
            formatter: () => '248.5k',
          },
        },
      },
    },
  },
  dataLabels: {
    enabled: true,
    formatter: (value: number) => `${value.toFixed(1)}%`,
  },
  tooltip: {
    theme: 'dark',
  },
});

const topProductsSeries = ref([
  {
    name: 'Unidades',
    data: [1820, 1610, 1500, 1380, 1240],
  },
]);

const topProductsOptions = ref({
  chart: {
    type: 'bar',
    background: 'transparent',
    toolbar: { show: false },
  },
  theme: { mode: 'dark' },
  colors: ['#4f9cf9', '#3ecf8e', '#f5a623', '#e879a0', '#a78bfa'],
  plotOptions: {
    bar: {
      horizontal: true,
      borderRadius: 5,
      distributed: true,
    },
  },
  xaxis: {
    categories: ['Chaqueta Denim', 'Leggings Sport', 'Sneakers Urban', 'Camiseta Premium', 'Hoodie Unisex'],
    labels: { style: { colors: '#aaa' } },
  },
  yaxis: {
    labels: { style: { colors: '#aaa' } },
  },
  grid: {
    borderColor: '#2a2a4a',
  },
  legend: {
    show: false,
  },
  tooltip: {
    theme: 'dark',
    y: {
      formatter: (value: number) => `${value} unidades`,
    },
  },
});

const ordersHeatmapSeries = ref([
  { name: 'Dom', data: [20, 28, 36, 42, 48, 55, 44] },
  { name: 'Sáb', data: [22, 35, 45, 48, 52, 60, 50] },
  { name: 'Vie', data: [18, 30, 38, 35, 43, 49, 39] },
  { name: 'Jue', data: [15, 25, 31, 29, 35, 41, 32] },
  { name: 'Mié', data: [14, 22, 29, 26, 33, 37, 30] },
  { name: 'Mar', data: [16, 24, 30, 28, 34, 40, 31] },
  { name: 'Lun', data: [12, 20, 27, 25, 31, 36, 29] },
]);

const ordersHeatmapOptions = ref({
  chart: {
    type: 'heatmap',
    background: 'transparent',
    toolbar: { show: false },
  },
  theme: { mode: 'dark' },
  colors: ['#4f9cf9'],
  dataLabels: {
    enabled: false,
  },
  xaxis: {
    categories: ['8h', '10h', '12h', '14h', '16h', '18h', '20h'],
    labels: { style: { colors: '#aaa' } },
  },
  yaxis: {
    labels: { style: { colors: '#aaa' } },
  },
  grid: {
    borderColor: '#2a2a4a',
  },
  tooltip: {
    theme: 'dark',
    y: {
      formatter: (value: number) => `${value} pedidos`,
    },
  },
});

const realTimeSeries = ref([
  {
    name: 'Pedidos acumulados',
    data: [40, 41, 42, 44, 45, 47, 49, 52, 55, 58, 60, 63, 67, 70, 74, 78, 83, 88, 94, 100, 107, 113, 119, 126],
  },
]);

const realTimeOptions = ref({
  chart: {
    type: 'line',
    background: 'transparent',
    toolbar: { show: false },
    animations: {
      enabled: true,
    },
  },
  theme: { mode: 'dark' },
  colors: ['#a78bfa'],
  stroke: {
    curve: 'smooth',
    width: 3,
  },
  xaxis: {
    labels: { show: false },
    axisBorder: { color: '#aaa' },
    axisTicks: { color: '#aaa' },
  },
  yaxis: {
    min: 40,
    max: 130,
    labels: { style: { colors: '#aaa' } },
  },
  grid: {
    borderColor: '#2a2a4a',
  },
  tooltip: {
    theme: 'dark',
    y: {
      formatter: (value: number) => `${value} pedidos`,
    },
  },
});

/* ─── KPIs de negocio ──────────────────────────────────────────────────────── */

const businessKpis = ref([
  {
    id: 1,
    title: 'Ventas Totales',
    current: '248.500 EUR',
    target: '240.000 EUR',
    progress: 103,
    color: '#3ecf8e',
    status: 'achieved',
    statusLabel: 'Superado',
    description: 'Objetivo superado. Se han alcanzado 248.500 EUR frente a una meta de 240.000 EUR.',
  },
  {
    id: 2,
    title: 'Pedidos Realizados',
    current: '3.841',
    target: '3.500',
    progress: 110,
    color: '#4f9cf9',
    status: 'achieved',
    statusLabel: 'Superado',
    description: 'Objetivo superado. Se han registrado 3.841 pedidos frente a una meta de 3.500.',
  },
  {
    id: 3,
    title: 'Visitantes Web',
    current: '127.340',
    target: '120.000',
    progress: 106,
    color: '#f5a623',
    status: 'achieved',
    statusLabel: 'Superado',
    description: 'Objetivo cumplido. La web ha superado los 120.000 visitantes previstos.',
  },
  {
    id: 4,
    title: 'Clientes Nuevos',
    current: '1.204',
    target: '1.500',
    progress: 80,
    color: '#a78bfa',
    status: 'at-risk',
    statusLabel: 'No Cumplido',
    description: 'No se ha alcanzado la meta. Se han conseguido 1.204 clientes nuevos de los 1.500 previstos.',
  },
  {
    id: 5,
    title: 'Producto Insignia',
    current: 'Chaqueta Denim',
    target: 'Top 1 ventas',
    progress: 100,
    color: '#e879a0',
    status: 'achieved',
    statusLabel: 'Superado',
    description: 'La Chaqueta Denim se consolida como el producto más vendido de Kora.',
  },
]);

/* ─── KPI cards técnicas ───────────────────────────────────────────────────── */

const techKpiCards = ref([
  {
    id: 1,
    title: 'Velocidad de Carga',
    current: '242ms',
    target: '<250ms',
    progress: 97,
    color: '#3ecf8e',
    status: 'achieved',
    statusLabel: 'Superado',
    description:
      'Objetivo superado. La carga promedio está en 242ms, por debajo del umbral objetivo de 250ms. El gráfico en tiempo real muestra oscilaciones normales entre 200ms y 340ms.',
  },
  {
    id: 2,
    title: 'Tráfico Móvil',
    current: '58% tráfico / 1,8% conv.',
    target: '2,8% conv.',
    progress: 64,
    color: '#4f9cf9',
    status: 'not-achieved',
    statusLabel: 'No Cumplido',
    description:
      'No se ha alcanzado la meta. Aunque el 58% del tráfico total proviene de dispositivos móviles, la conversión móvil sigue siendo del 1,8%, por debajo del objetivo del 2,8%.',
  },
  {
    id: 3,
    title: 'Posicionamiento SEO',
    current: 'Posición 4',
    target: 'Top 3',
    progress: 75,
    color: '#f5a623',
    status: 'not-achieved',
    statusLabel: 'No Cumplido',
    description:
      'No se ha alcanzado la meta. Actualmente la tienda se encuentra en posición 4 y el objetivo era entrar en el Top 3 para las palabras clave principales.',
  },
  {
    id: 4,
    title: 'Errores de Servidor',
    current: '3 errores/mes',
    target: '<2 errores/mes',
    progress: 60,
    color: '#e879a0',
    status: 'not-achieved',
    statusLabel: 'No Cumplido',
    description:
      'No se ha alcanzado la meta. Actualmente se registran 3 errores de servidor al mes y el objetivo era reducirlos a menos de 2 errores mensuales.',
  },
  {
    id: 5,
    title: 'Cobertura de Tests',
    current: '41%',
    target: '70%',
    progress: 59,
    color: '#a78bfa',
    status: 'not-achieved',
    statusLabel: 'No Cumplido',
    description:
      'No se ha alcanzado la meta. La cobertura actual es del 41%, por debajo del objetivo del 70%, especialmente en módulos críticos como Pago y Catálogo.',
  },
]);

/* ─── Accordions SMART — negocio ───────────────────────────────────────────── */

const businessKpisW = ref([
  {
    id: 1,
    title: 'Ventas Totales',
    current: '248.500 EUR',
    color: '#3ecf8e',
    description:
      'Alcanzar una facturación total de 240.000 EUR en la tienda online durante el periodo analizado, mediante campañas promocionales, mejora de productos destacados y optimización del proceso de compra. Actualmente se han alcanzado 248.500 EUR, por lo que el objetivo se ha cumplido.',
    smart: [
      { letter: 'S', content: 'Aumentar las ventas totales de la tienda online' },
      { letter: 'M', content: 'Meta: 240.000 EUR | Actual: 248.500 EUR | Objetivo cumplido' },
      { letter: 'A', content: 'Mediante campañas promocionales, mejora de productos destacados y optimización del checkout' },
      { letter: 'R', content: 'Las ventas permiten medir si el negocio está creciendo económicamente' },
      { letter: 'T', content: 'Durante el periodo anual analizado' },
    ],
  },
  {
    id: 2,
    title: 'Pedidos Realizados',
    current: '3.841',
    color: '#4f9cf9',
    description:
      'Alcanzar un total de 3.500 pedidos realizados durante el periodo analizado, mejorando la experiencia de usuario, el funcionamiento del carrito y la conversión en el checkout. Actualmente se han registrado 3.841 pedidos, por lo que el objetivo se ha cumplido.',
    smart: [
      { letter: 'S', content: 'Incrementar el número total de pedidos realizados en la tienda online' },
      { letter: 'M', content: 'Meta: 3.500 pedidos | Actual: 3.841 pedidos | Objetivo cumplido' },
      { letter: 'A', content: 'Mejorando la experiencia de usuario, el carrito y el proceso de pago' },
      { letter: 'R', content: 'Los pedidos muestran la actividad real de compra dentro del e-commerce' },
      { letter: 'T', content: 'Durante el periodo anual analizado' },
    ],
  },
  {
    id: 3,
    title: 'Clientes Nuevos',
    current: '1.204',
    color: '#a78bfa',
    description:
      'Conseguir 1.500 clientes nuevos durante el periodo analizado mediante campañas en redes sociales, descuentos de primera compra, newsletter y promociones exclusivas. Actualmente se han conseguido 1.204 clientes nuevos, por lo que el objetivo todavía no se ha cumplido.',
    smart: [
      { letter: 'S', content: 'Captar nuevos clientes que realicen su primera compra en Kora' },
      { letter: 'M', content: 'Meta: 1.500 clientes nuevos | Actual: 1.204 | Objetivo no cumplido' },
      { letter: 'A', content: 'Mediante campañas en redes sociales, descuentos de primera compra y promociones exclusivas' },
      { letter: 'R', content: 'La captación de clientes nuevos es clave para hacer crecer la base de usuarios' },
      { letter: 'T', content: 'Durante el periodo anual analizado' },
    ],
  },
  {
    id: 4,
    title: 'Visitantes Web',
    current: '127.340',
    color: '#f5a623',
    description:
      'Alcanzar 120.000 visitantes web durante el periodo analizado, mejorando el SEO, publicando contenido visual atractivo y potenciando campañas en redes sociales. Actualmente se han alcanzado 127.340 visitantes, por lo que el objetivo se ha cumplido.',
    smart: [
      { letter: 'S', content: 'Aumentar el número de visitantes web de la tienda online' },
      { letter: 'M', content: 'Meta: 120.000 visitantes | Actual: 127.340 | Objetivo cumplido' },
      { letter: 'A', content: 'Mejorando el SEO, el contenido visual y la difusión en redes sociales' },
      { letter: 'R', content: 'Un mayor tráfico web aumenta las posibilidades de generar ventas' },
      { letter: 'T', content: 'Durante el periodo anual analizado' },
    ],
  },
  {
    id: 5,
    title: 'Producto Insignia',
    current: 'Chaqueta Denim',
    color: '#e879a0',
    description:
      'Consolidar la Chaqueta Denim como producto insignia de Kora, posicionándola como el artículo más vendido del periodo mediante banners, recomendaciones, presencia destacada en la home y campañas promocionales. Actualmente es el producto con más unidades vendidas, por lo que el objetivo se ha cumplido.',
    smart: [
      { letter: 'S', content: 'Convertir la Chaqueta Denim en el producto insignia de Kora' },
      { letter: 'M', content: 'Meta: ser el producto más vendido | Actual: producto líder en ventas | Objetivo cumplido' },
      { letter: 'A', content: 'Destacándola en la home, banners, recomendaciones y campañas promocionales' },
      { letter: 'R', content: 'Un producto insignia refuerza la identidad de marca y concentra las campañas comerciales' },
      { letter: 'T', content: 'Durante el periodo anual analizado' },
    ],
  },
]);

/* ─── Accordions SMART — técnico ───────────────────────────────────────────── */

const techKpis = ref([
  {
    id: 1,
    title: 'Velocidad de Carga',
    current: '242ms',
    target: '<250ms',
    progress: 97,
    color: '#3ecf8e',
    status: 'achieved',
    statusLabel: 'Superado',
    description:
      'Reducir el tiempo de carga promedio de la web hasta situarlo por debajo de 250ms mediante la optimización de imágenes WebP, implementación de CDN y caché de navegador. Actualmente el valor es de 242ms, por lo que el objetivo se ha superado. El gráfico en tiempo real muestra oscilaciones normales entre 200ms y 340ms.',
    smart: [
      { letter: 'S', content: 'Reducir el tiempo de carga promedio de la tienda online' },
      { letter: 'M', content: 'Meta: menos de 250ms | Actual: 242ms | Progreso: 97% | Estado: superado' },
      { letter: 'A', content: 'Optimizando imágenes WebP, implementando CDN y aplicando caché de navegador' },
      { letter: 'R', content: 'Una carga rápida mejora la experiencia de usuario, el SEO y la conversión' },
      { letter: 'T', content: 'Durante el periodo técnico analizado' },
    ],
  },
  {
    id: 2,
    title: 'Tráfico Móvil',
    current: '58% tráfico / 1,8% conv.',
    target: '2,8% conv.',
    progress: 64,
    color: '#4f9cf9',
    status: 'not-achieved',
    statusLabel: 'No Cumplido',
    description:
      'Mejorar la conversión en dispositivos móviles hasta alcanzar el 2,8%, aprovechando que el 58% del tráfico total ya proviene de móvil. Actualmente la conversión móvil es del 1,8%, por lo que el objetivo no se ha cumplido.',
    smart: [
      { letter: 'S', content: 'Mejorar la conversión de los usuarios que acceden desde dispositivos móviles' },
      { letter: 'M', content: 'Meta: 2,8% conversión móvil | Actual: 1,8% | Tráfico móvil: 58% | Progreso: 64% | Estado: no cumplido' },
      { letter: 'A', content: 'Rediseñando el checkout móvil, reduciendo pasos y añadiendo métodos de pago rápidos' },
      { letter: 'R', content: 'Más de la mitad del tráfico procede de móvil, por lo que mejorar este canal puede aumentar las ventas' },
      { letter: 'T', content: 'Durante el periodo técnico analizado' },
    ],
  },
  {
    id: 3,
    title: 'Posicionamiento SEO',
    current: 'Posición 4',
    target: 'Top 3',
    progress: 75,
    color: '#f5a623',
    status: 'not-achieved',
    statusLabel: 'No Cumplido',
    description:
      'Alcanzar el Top 3 de Google en las principales palabras clave del proyecto. Actualmente la tienda se encuentra en posición 4, por lo que todavía no se ha cumplido el objetivo marcado.',
    smart: [
      { letter: 'S', content: 'Alcanzar el Top 3 en Google para las palabras clave principales de Kora' },
      { letter: 'M', content: 'Meta: Top 3 | Actual: posición 4 | Progreso: 75% | Estado: no cumplido' },
      { letter: 'A', content: 'Creando contenido SEO, optimizando páginas de producto y mejorando backlinks' },
      { letter: 'R', content: 'El tráfico orgánico representa el 42% del total y es uno de los canales más importantes' },
      { letter: 'T', content: 'Durante los próximos meses del periodo analizado' },
    ],
  },
  {
    id: 4,
    title: 'Errores de Servidor',
    current: '3 errores/mes',
    target: '<2 errores/mes',
    progress: 60,
    color: '#e879a0',
    status: 'not-achieved',
    statusLabel: 'No Cumplido',
    description:
      'Reducir los errores críticos de servidor 5xx hasta situarlos por debajo de 2 errores al mes. Actualmente se registran 3 errores mensuales, por lo que el objetivo no se ha cumplido.',
    smart: [
      { letter: 'S', content: 'Reducir los errores críticos de servidor 5xx en la tienda online' },
      { letter: 'M', content: 'Meta: menos de 2 errores/mes | Actual: 3 errores/mes | Progreso: 60% | Estado: no cumplido' },
      { letter: 'A', content: 'Implementando monitorización 24h, alertas automáticas y pipelines de CI/CD con tests' },
      { letter: 'R', content: 'Reducir errores evita pérdidas de ventas y mejora la estabilidad del e-commerce' },
      { letter: 'T', content: 'Durante el periodo técnico analizado' },
    ],
  },
  {
    id: 5,
    title: 'Cobertura de Tests',
    current: '41%',
    target: '70%',
    progress: 59,
    color: '#a78bfa',
    status: 'not-achieved',
    statusLabel: 'No Cumplido',
    description:
      'Aumentar la cobertura de tests automáticos de los módulos críticos hasta alcanzar el 70% antes del 30 de junio de 2026. Actualmente la cobertura total es del 41%, por lo que el objetivo no se ha cumplido.',
    smart: [
      { letter: 'S', content: 'Aumentar la cobertura de tests automáticos en los módulos críticos del proyecto' },
      { letter: 'M', content: 'Meta: 70% | Actual: 41% | Progreso: 59% | Estado: no cumplido' },
      { letter: 'A', content: 'Añadiendo tests unitarios y de integración en Auth, Carrito, Pago y Catálogo' },
      { letter: 'R', content: 'Una mayor cobertura reduce el riesgo de regresiones y errores en cada despliegue' },
      { letter: 'T', content: 'Antes del 30 de junio de 2026' },
    ],
  },
]);
</script>

<style scoped>
/* ─── Secciones ─────────────────────────────────────────────────────────────── */
.section-header {
  display: flex;
  flex-direction: column;
  gap: 2px;
  padding: 20px 4px 10px 4px;
}

.section-label {
  font-size: 0.78rem;
  font-weight: 700;
  color: #e0e0e0;
  text-transform: uppercase;
  letter-spacing: 0.1em;
}

.section-sub {
  font-size: 0.72rem;
  color: #555;
}


/* ─── KPI cards negocio ─────────────────────────────────────────────────────── */
.kpi-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(270px, 1fr));
  gap: 10px;
  margin-bottom: 12px;
}

.kpi-card {
  background: #1a1a2e;
  border: 1px solid #2a2a4a;
  border-radius: 12px;
  padding: 14px 16px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.kpi-top {
  display: flex;
  align-items: center;
  gap: 8px;
  flex-wrap: wrap;
}

.kpi-title {
  font-size: 0.85rem;
  font-weight: 600;
  color: #e0e0e0;
  flex: 1;
}

.kpi-badge {
  font-size: 0.65rem;
  padding: 2px 10px;
  border-radius: 20px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  white-space: nowrap;
}

.kpi-badge.achieved {
  background: rgba(62, 207, 142, 0.12);
  color: #3ecf8e;
  border: 1px solid rgba(62, 207, 142, 0.3);
}

.kpi-badge.on-track {
  background: rgba(79, 156, 249, 0.12);
  color: #4f9cf9;
  border: 1px solid rgba(79, 156, 249, 0.3);
}

.kpi-badge.at-risk {
  background: rgba(232, 121, 160, 0.12);
  color: #e879a0;
  border: 1px solid rgba(232, 121, 160, 0.3);
}

.kpi-progress-bar {
  background: #2a2a4a;
  border-radius: 999px;
  height: 5px;
  overflow: hidden;
}

.kpi-progress-fill {
  height: 100%;
  border-radius: 999px;
  transition: width 0.6s ease;
}

.kpi-bottom {
  display: flex;
  align-items: center;
  gap: 8px;
}

.kpi-current {
  font-size: 1.05rem;
  font-weight: 700;
  color: #fff;
  flex: 1;
}

.kpi-target {
  font-size: 0.7rem;
  color: #666;
}

.kpi-pct {
  font-size: 0.8rem;
  font-weight: 700;
}

.kpi-desc {
  font-size: 0.73rem;
  color: #666;
  margin: 0;
  line-height: 1.5;
}

/* ─── KPI cards técnico ─────────────────────────────────────────────────────── */
.tech-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(270px, 1fr));
  gap: 10px;
  margin-bottom: 12px;
}
.kpi-badge.not-achieved {
  background: rgba(232, 121, 160, 0.12);
  color: #e879a0;
  border: 1px solid rgba(232, 121, 160, 0.3);
}
.tech-card {
  background: #1a1a2e;
  border: 1px solid #2a2a4a;
  border-radius: 12px;
  padding: 14px 16px;
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.tech-top {
  display: flex;
  align-items: center;
  gap: 10px;
}

.tech-icon-wrap {
  width: 36px;
  height: 36px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.tech-icon {
  font-size: 1.1rem;
}

.tech-info {
  display: flex;
  flex-direction: column;
  flex: 1;
  gap: 1px;
}

.tech-title {
  font-size: 0.75rem;
  color: #888;
}

.tech-value {
  font-size: 1rem;
  font-weight: 700;
}

/* ─── Accordions ───────────────────────────────────────────────────────────── */
.acc-content p {
  color: #888;
  font-size: 0.82rem;
  margin-bottom: 8px;
  line-height: 1.5;
}

.smart-item {
  display: flex !important;
  align-items: flex-start;
  gap: 10px;
  white-space: normal;
}

.smart-letter {
  font-size: 0.8rem;
  font-weight: 800;
  min-width: 14px;
}

.smart-text {
  font-size: 0.8rem;
  color: #aaa;
}

ion-accordion.accordion-expanding ion-item[slot='header'],
ion-accordion.accordion-expanded ion-item[slot='header'] {
  --background: rgba(var(--ion-color-primary-rgb), 0.1);
  color: var(--ion-color-primary);
}

/* ─── Responsive ───────────────────────────────────────────────────────────── */
@media (max-width: 1100px) {
  .dashboard-grid {
    grid-template-columns: repeat(2, 1fr);
  }

  .chart-wide {
    grid-column: span 2;
  }
}

@media (max-width: 640px) {
  .dashboard-grid {
    grid-template-columns: 1fr;
  }

  .chart-wide {
    grid-column: span 1;
  }
}
</style>