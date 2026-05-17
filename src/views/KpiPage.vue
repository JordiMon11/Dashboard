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

      <!-- BLOQUE WEB: métricas directas del dashboard técnico -->
      <div class="section-header">
        <span class="section-label">Métricas Web</span>
        <span class="section-sub">Datos en tiempo real del dashboard técnico</span>
      </div>

      <div class="web-metrics-row">
        <div class="wm-card" v-for="m in webMetrics" :key="m.id">
          <div class="wm-left">
            <span class="wm-title">{{ m.title }}</span>
            <span class="wm-value">{{ m.value }}</span>
            <span class="wm-delta" :class="m.deltaType">{{ m.delta }}</span>
          </div>
          <div class="wm-right">
            <vapexChart
              type="radialBar"
              height="100"
              :options="m.chartOpts"
              :series="m.chartSeries"
            />
          </div>
        </div>
      </div>

      <!-- FUENTES DE TRÁFICO + DISPOSITIVOS: resumen visual -->
      <div class="two-col-row">
        <div class="summary-card">
          <div class="summary-header">
            <span class="summary-title">Fuentes de Tráfico</span>
            <span class="summary-sub">% visitas por canal</span>
          </div>
          <div class="source-list">
            <div class="source-row" v-for="s in trafficSources" :key="s.label">
              <span class="source-dot" :style="{ background: s.color }"></span>
              <span class="source-label">{{ s.label }}</span>
              <div class="source-bar-wrap">
                <div class="source-bar-fill" :style="{ width: s.pct + '%', background: s.color }"></div>
              </div>
              <span class="source-pct">{{ s.pct }}%</span>
            </div>
          </div>
        </div>

        <div class="summary-card">
          <div class="summary-header">
            <span class="summary-title">Dispositivos</span>
            <span class="summary-sub">Distribución de sesiones</span>
          </div>
          <div class="device-list">
            <div class="device-row" v-for="d in devices" :key="d.label">
              <span class="device-dot" :style="{ background: d.color }"></span>
              <span class="device-label">{{ d.label }}</span>
              <div class="source-bar-wrap">
                <div class="source-bar-fill" :style="{ width: d.pct + '%', background: d.color }"></div>
              </div>
              <span class="source-pct">{{ d.pct }}%</span>
            </div>
            <p class="device-note">El 58% del tráfico es móvil. La conversión móvil (1,8%) es el principal punto de mejora frente a escritorio (4,2%).</p>
          </div>
        </div>
      </div>

      <!-- KPIs DE NEGOCIO -->
      <div class="section-header">
        <span class="section-label">KPIs de Negocio</span>
        <span class="section-sub">Objetivos anuales y estado actual</span>
      </div>

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

      <!-- Tarjetas técnicas con mini-chart de contexto -->
      <div class="tech-grid">
        <div class="tech-card" v-for="t in techKpiCards" :key="t.id">
          <div class="tech-top">
            <div class="tech-icon-wrap" :style="{ background: t.color + '22', border: '1px solid ' + t.color + '44' }">
              <span class="tech-icon">{{ t.icon }}</span>
            </div>
            <div class="tech-info">
              <span class="tech-title">{{ t.title }}</span>
              <span class="tech-value" :style="{ color: t.color }">{{ t.current }}</span>
            </div>
            <span class="kpi-badge" :class="t.status">{{ t.statusLabel }}</span>
          </div>
          <div class="kpi-progress-bar" style="margin: 8px 0 4px;">
            <div
              class="kpi-progress-fill"
              :style="{ width: Math.min(t.progress, 100) + '%', background: t.color }"
            ></div>
          </div>
          <div class="kpi-bottom">
            <span class="kpi-target">Objetivo: {{ t.target }}</span>
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
  IonButtons, IonContent, IonHeader, IonMenuButton,
  IonPage, IonTitle, IonToolbar,
  IonAccordionGroup, IonAccordion, IonItem, IonLabel, IonList,
} from '@ionic/vue';
import { ref } from 'vue';
import vapexChart from 'vue3-apexcharts';

function makeRadial(color: string, label: string) {
  return {
    chart: { type: 'radialBar', background: 'transparent', sparkline: { enabled: true } },
    theme: { mode: 'dark' },
    plotOptions: {
      radialBar: {
        hollow: { size: '52%' },
        dataLabels: {
          name:  { show: true,  color: '#999', fontSize: '9px', offsetY: 18 },
          value: { show: true,  color: '#fff', fontSize: '13px', fontWeight: 700, offsetY: -8 },
        },
        track: { background: '#2a2a4a' },
      },
    },
    colors: [color],
    labels: [label],
  };
}

// Métricas web — coherentes con el dashboard técnico
const webMetrics = ref([
  {
    id: 1, title: 'Tasa de Conversión', value: '3,02%', delta: '+0.4% vs mes anterior', deltaType: 'positive',
    chartOpts: makeRadial('#3ecf8e', 'Conv.'), chartSeries: [30.2],
  },
  {
    id: 2, title: 'Tiempo Medio en Web', value: '4m 22s', delta: '+18s vs mes anterior', deltaType: 'positive',
    chartOpts: makeRadial('#f5a623', 'Min'), chartSeries: [72],
  },
  {
    id: 3, title: 'Tasa de Rebote', value: '38,7%', delta: '-2.1% vs mes anterior', deltaType: 'negative',
    chartOpts: makeRadial('#e879a0', 'Rebote'), chartSeries: [38.7],
  },
]);

// Fuentes de tráfico — igual que el pie del dashboard
const trafficSources = ref([
  { label: 'Orgánico',   pct: 42, color: '#4f9cf9' },
  { label: 'Directo',    pct: 28, color: '#3ecf8e' },
  { label: 'Redes Soc.', pct: 18, color: '#f5a623' },
  { label: 'Email',      pct:  8, color: '#e879a0' },
  { label: 'Otros',      pct:  4, color: '#a78bfa' },
]);

// Dispositivos — igual que el donut del dashboard
const devices = ref([
  { label: 'Móvil',      pct: 58, color: '#4f9cf9' },
  { label: 'Escritorio', pct: 32, color: '#3ecf8e' },
  { label: 'Tablet',     pct: 10, color: '#f5a623' },
]);

// KPIs de negocio
const businessKpis = ref([
  { id: 1, title: 'Ventas Anuales',        current: '248.500 EUR', target: '240.000 EUR', progress: 103, color: '#3ecf8e', status: 'achieved', statusLabel: 'Superado',     description: 'Meta anual superada en un 3,5%. Julio registró el pico histórico con 52.000 EUR.' },
  { id: 2, title: 'Tasa de Conversión',    current: '3,02%',       target: '3,5%',        progress:  86, color: '#f5a623', status: 'on-track', statusLabel: 'En Progreso',  description: 'En 3,02%. Mejorar el checkout y reducir el abandono de carrito son las acciones prioritarias.' },
  { id: 3, title: 'Clientes Nuevos',       current: '1.204',       target: '1.500',        progress:  80, color: '#4f9cf9', status: 'on-track', statusLabel: 'En Progreso',  description: 'Al 80% del objetivo. Las campañas de verano han aportado el 40% de las captaciones.' },
  { id: 4, title: 'NPS (Satisfacción)',    current: '72 pts',      target: '75 pts',       progress:  96, color: '#a78bfa', status: 'on-track', statusLabel: 'En Progreso',  description: 'NPS de 72, cerca del objetivo. El servicio posventa es el área con mayor margen de mejora.' },
  { id: 5, title: 'Tasa de Retención',    current: '64%',         target: '70%',          progress:  91, color: '#e879a0', status: 'on-track', statusLabel: 'En Progreso',  description: 'El 64% de clientes repiten compra. El programa de fidelización de Q3 está funcionando.' },
  { id: 6, title: 'Ticket Medio',         current: '64,7 EUR',    target: '60 EUR',       progress: 100, color: '#3ecf8e', status: 'achieved', statusLabel: 'Superado',     description: 'Supera el objetivo gracias al upselling de accesorios y packs de temporada.' },
]);

// KPI cards técnicas — coherentes con el dashboard
const techKpiCards = ref([
  { id: 1, title: 'Velocidad de Carga', current: '242ms',        target: '<250ms',       progress: 97,  color: '#3ecf8e', status: 'achieved', statusLabel: 'Superado',    icon: '⚡', description: 'La carga promedio está en 242ms, por debajo del umbral objetivo de 250ms. El gráfico en tiempo real muestra oscilaciones entre 230-300ms.' },
  { id: 2, title: 'Tráfico Móvil',     current: '58%',          target: 'Conv. 2,8%',   progress: 64,  color: '#4f9cf9', status: 'on-track', statusLabel: 'En Progreso', icon: '📱', description: 'El 58% del tráfico es móvil (reflejado en el donut del dashboard). La conversión móvil está al 64% del objetivo de 2,8%.' },
  { id: 3, title: 'SEO — Posición',    current: 'Pos. 4',       target: 'Top 3',        progress: 75,  color: '#f5a623', status: 'on-track', statusLabel: 'En Progreso', icon: '🔍', description: 'El 42% del tráfico es orgánico (mayor canal según el pie del dashboard). Objetivo: top 3 en 5 keywords en 6 meses.' },
  { id: 4, title: 'Errores Servidor',  current: '3/mes',        target: '<2/mes',       progress: 60,  color: '#e879a0', status: 'at-risk',  statusLabel: 'En Riesgo',  icon: '🔴', description: 'Todavía por encima del objetivo. Cada error 5xx supone una pérdida media de 800 EUR en ventas.' },
  { id: 5, title: 'Cobertura Tests',   current: '41%',          target: '70%',          progress: 59,  color: '#a78bfa', status: 'at-risk',  statusLabel: 'En Riesgo',  icon: '🧪', description: 'Cobertura en módulos críticos (carrito, pago, autenticación). El bajo porcentaje aumenta el riesgo de regresiones.' },
]);

// Accordions SMART — negocio
const businessKpisW = ref([
  {
    id: 1, title: 'Ventas Anuales', current: '248.500 EUR', color: '#3ecf8e',
    description: 'Incrementar las ventas anuales totales de la tienda online de 228.000 EUR a 240.000 EUR mediante campañas de verano, Black Friday y upselling de accesorios, antes del 31 de diciembre de 2026.',
    smart: [
      { letter: 'S', content: 'Incrementar las ventas anuales totales de la tienda online' },
      { letter: 'M', content: 'De 228.000 EUR a 240.000 EUR (logrado: 248.500 EUR, +3,5%)' },
      { letter: 'A', content: 'Mediante campañas de verano, Black Friday y upselling de accesorios' },
      { letter: 'R', content: 'Las ventas son el indicador principal de salud del negocio' },
      { letter: 'T', content: 'Antes del 31 de diciembre de 2026' },
    ],
  },
  {
    id: 2, title: 'Tasa de Conversión', current: '3,02%', color: '#f5a623',
    description: 'Aumentar el porcentaje de visitantes que completan una compra de 2,6% a 3,5% rediseñando el checkout y añadiendo métodos de pago, antes del 1 de octubre de 2026.',
    smart: [
      { letter: 'S', content: 'Aumentar el porcentaje de visitantes que completan una compra' },
      { letter: 'M', content: 'De 2,6% a 3,5% de tasa de conversión (actual: 3,02%)' },
      { letter: 'A', content: 'Rediseñando el checkout, añadiendo métodos de pago y reduciendo pasos' },
      { letter: 'R', content: 'Una mayor conversión multiplica los ingresos sin aumentar el tráfico' },
      { letter: 'T', content: 'Antes del 1 de octubre de 2026' },
    ],
  },
  {
    id: 3, title: 'Clientes Nuevos', current: '1.204', color: '#4f9cf9',
    description: 'Captar nuevos clientes que realicen su primera compra, pasando de 980 a 1.500 mediante campañas en redes sociales, Google Ads y programa de referidos, antes del 31 de diciembre de 2026.',
    smart: [
      { letter: 'S', content: 'Captar nuevos clientes que realicen su primera compra' },
      { letter: 'M', content: 'De 980 a 1.500 nuevos clientes en el año (actual: 1.204)' },
      { letter: 'A', content: 'Con campañas en redes sociales, Google Ads y programa de referidos' },
      { letter: 'R', content: 'La base de clientes nuevos es clave para el crecimiento sostenido' },
      { letter: 'T', content: 'Antes del 31 de diciembre de 2026' },
    ],
  },
  {
    id: 4, title: 'NPS (Satisfacción)', current: '72 pts', color: '#a78bfa',
    description: 'Mejorar la satisfacción global del cliente de 68 a 75 puntos NPS mejorando los tiempos de respuesta del soporte y la política de devoluciones, antes del 31 de diciembre de 2026.',
    smart: [
      { letter: 'S', content: 'Mejorar la satisfacción global del cliente medida con NPS' },
      { letter: 'M', content: 'De 68 a 75 puntos de NPS (actual: 72)' },
      { letter: 'A', content: 'Mejorando tiempos de respuesta del soporte y política de devoluciones' },
      { letter: 'R', content: 'Un NPS alto reduce la tasa de abandono y aumenta las recomendaciones' },
      { letter: 'T', content: 'Antes del 31 de diciembre de 2026' },
    ],
  },
  {
    id: 5, title: 'Tasa de Retención', current: '64%', color: '#e879a0',
    description: 'Aumentar el porcentaje de clientes que realizan más de una compra al año de 58% a 70% mediante el programa de puntos, emails de reactivación y descuentos personalizados, antes del 31 de diciembre de 2026.',
    smart: [
      { letter: 'S', content: 'Aumentar el porcentaje de clientes que realizan más de una compra al año' },
      { letter: 'M', content: 'De 58% a 70% de tasa de retención anual (actual: 64%)' },
      { letter: 'A', content: 'Con el programa de puntos, emails de reactivación y descuentos personalizados' },
      { letter: 'R', content: 'Retener clientes existentes es 5 veces más barato que adquirir nuevos' },
      { letter: 'T', content: 'Antes del 31 de diciembre de 2026' },
    ],
  },
]);

// Accordions SMART — técnico
const techKpis = ref([
  {
    id: 1, title: 'Velocidad de Carga', current: '242ms', color: '#3ecf8e',
    description: 'Reducir el tiempo de carga promedio de la web de 340ms a menos de 250ms optimizando imágenes WebP, implementando CDN y caché de navegador. El gráfico de tiempo real del dashboard confirma que ya estamos por debajo del umbral en condiciones normales.',
    smart: [
      { letter: 'S', content: 'Reducir el tiempo de carga promedio de la web' },
      { letter: 'M', content: 'De 340ms a menos de 250ms (actual: 242ms — objetivo conseguido)' },
      { letter: 'A', content: 'Optimizando imágenes WebP, implementando CDN y caché de navegador' },
      { letter: 'R', content: 'Mejora la experiencia de usuario y el posicionamiento SEO' },
      { letter: 'T', content: 'En los próximos 3 meses (ya alcanzado)' },
    ],
  },
  {
    id: 2, title: 'Tráfico Móvil — Conversión', current: '58% tráfico / 1,8% conv.', color: '#4f9cf9',
    description: 'Mejorar la conversión en dispositivos móviles de 1,8% a 2,8% rediseñando el checkout móvil y añadiendo Apple Pay y Google Pay. El dashboard muestra que el 58% del tráfico ya es móvil.',
    smart: [
      { letter: 'S', content: 'Mejorar la conversión en dispositivos móviles' },
      { letter: 'M', content: 'De 1,8% a 2,8% de conversión móvil (tráfico móvil actual: 58%)' },
      { letter: 'A', content: 'Rediseñando el checkout móvil y añadiendo Apple Pay y Google Pay' },
      { letter: 'R', content: 'El 58% del tráfico es móvil — es el canal con mayor potencial de crecimiento' },
      { letter: 'T', content: 'Antes del inicio de la temporada de rebajas (enero 2026)' },
    ],
  },
  {
    id: 3, title: 'Posicionamiento SEO', current: 'Posición 4', color: '#f5a623',
    description: 'Alcanzar el top 3 en Google para las palabras clave principales. El 42% del tráfico proviene de búsqueda orgánica según el dashboard — es el canal más valioso y el que más merece inversión.',
    smart: [
      { letter: 'S', content: 'Alcanzar el top 3 en Google para palabras clave principales' },
      { letter: 'M', content: 'Pasar de posición 4 a posición 1-3 en 5 keywords objetivo' },
      { letter: 'A', content: 'Publicando 3 artículos SEO semanales y mejorando backlinks' },
      { letter: 'R', content: 'El 42% del tráfico actual proviene de búsqueda orgánica (mayor canal)' },
      { letter: 'T', content: 'En 6 meses' },
    ],
  },
  {
    id: 4, title: 'Reducción de Errores', current: '3 errores/mes', color: '#e879a0',
    description: 'Reducir los errores de servidor críticos (5xx) de 5 a menos de 2 por mes. Cada error supone una pérdida media de 800 EUR y afecta directamente a la tasa de conversión visible en el dashboard.',
    smart: [
      { letter: 'S', content: 'Reducir los errores de servidor críticos (5xx)' },
      { letter: 'M', content: 'De 5 errores/mes a menos de 2 errores/mes (actual: 3)' },
      { letter: 'A', content: 'Implementando monitorización 24h y pipelines de CI/CD con tests' },
      { letter: 'R', content: 'Cada error 5xx supone una pérdida media de 800 EUR en ventas perdidas' },
      { letter: 'T', content: 'En los próximos 2 meses' },
    ],
  },
  {
    id: 5, title: 'Cobertura de Tests', current: '41%', color: '#a78bfa',
    description: 'Aumentar la cobertura de tests automáticos en los módulos críticos de 41% a 70% medida con Vitest. Una mayor cobertura reduce el riesgo de regresiones que impacten en la velocidad o conversión del sitio.',
    smart: [
      { letter: 'S', content: 'Aumentar la cobertura de tests automáticos en los módulos críticos' },
      { letter: 'M', content: 'De 41% a 70% de cobertura medida con Vitest' },
      { letter: 'A', content: 'Escribiendo tests unitarios y de integración para carrito, pago y autenticación' },
      { letter: 'R', content: 'Una mayor cobertura reduce el riesgo de regresiones en cada despliegue' },
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

/* ─── Métricas web (radial) ─────────────────────────────────────────────────── */
.web-metrics-row {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
  gap: 10px;
  margin-bottom: 12px;
}
.wm-card {
  background: #1a1a2e;
  border: 1px solid #2a2a4a;
  border-radius: 12px;
  padding: 14px 16px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 8px;
}
.wm-left { display: flex; flex-direction: column; gap: 4px; flex: 1; }
.wm-title { font-size: 0.72rem; color: #777; }
.wm-value { font-size: 1.5rem; font-weight: 700; color: #e0e0e0; }
.wm-delta { font-size: 0.7rem; }
.wm-delta.positive { color: #3ecf8e; }
.wm-delta.negative { color: #e879a0; }

/* ─── Dos columnas (fuentes + dispositivos) ─────────────────────────────────── */
.two-col-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  margin-bottom: 4px;
}
@media (max-width: 640px) {
  .two-col-row { grid-template-columns: 1fr; }
}
.summary-card {
  background: #1a1a2e;
  border: 1px solid #2a2a4a;
  border-radius: 12px;
  padding: 14px 16px;
}
.summary-header { margin-bottom: 12px; display: flex; flex-direction: column; gap: 2px; }
.summary-title { font-size: 0.85rem; font-weight: 600; color: #e0e0e0; }
.summary-sub   { font-size: 0.7rem; color: #666; }
.source-list, .device-list { display: flex; flex-direction: column; gap: 8px; }
.source-row, .device-row {
  display: flex;
  align-items: center;
  gap: 8px;
}
.source-dot, .device-dot {
  width: 8px; height: 8px;
  border-radius: 50%;
  flex-shrink: 0;
}
.source-label, .device-label {
  font-size: 0.75rem;
  color: #aaa;
  width: 72px;
  flex-shrink: 0;
}
.source-bar-wrap {
  flex: 1;
  height: 5px;
  background: #2a2a4a;
  border-radius: 999px;
  overflow: hidden;
}
.source-bar-fill {
  height: 100%;
  border-radius: 999px;
  transition: width 0.6s ease;
}
.source-pct {
  font-size: 0.72rem;
  font-weight: 600;
  color: #888;
  width: 30px;
  text-align: right;
}
.device-note {
  font-size: 0.7rem;
  color: #555;
  margin: 6px 0 0 0;
  line-height: 1.5;
}

/* ─── KPI cards negocio ──────────────────────────────────────────────────────── */
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
.kpi-top { display: flex; align-items: center; gap: 8px; flex-wrap: wrap; }
.kpi-title { font-size: 0.85rem; font-weight: 600; color: #e0e0e0; flex: 1; }
.kpi-badge {
  font-size: 0.65rem;
  padding: 2px 10px;
  border-radius: 20px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  white-space: nowrap;
}
.kpi-badge.achieved { background: rgba(62,207,142,0.12); color: #3ecf8e; border: 1px solid rgba(62,207,142,0.3); }
.kpi-badge.on-track { background: rgba(79,156,249,0.12); color: #4f9cf9; border: 1px solid rgba(79,156,249,0.3); }
.kpi-badge.at-risk  { background: rgba(232,121,160,0.12); color: #e879a0; border: 1px solid rgba(232,121,160,0.3); }
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
.kpi-bottom { display: flex; align-items: center; gap: 8px; }
.kpi-current { font-size: 1.05rem; font-weight: 700; color: #fff; flex: 1; }
.kpi-target  { font-size: 0.7rem; color: #666; }
.kpi-pct     { font-size: 0.8rem; font-weight: 700; }
.kpi-desc    { font-size: 0.73rem; color: #666; margin: 0; line-height: 1.5; }

/* ─── KPI cards técnico ──────────────────────────────────────────────────────── */
.tech-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(270px, 1fr));
  gap: 10px;
  margin-bottom: 12px;
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
.tech-top { display: flex; align-items: center; gap: 10px; }
.tech-icon-wrap {
  width: 36px; height: 36px;
  border-radius: 8px;
  display: flex; align-items: center; justify-content: center;
  flex-shrink: 0;
}
.tech-icon { font-size: 1.1rem; }
.tech-info { display: flex; flex-direction: column; flex: 1; gap: 1px; }
.tech-title { font-size: 0.75rem; color: #888; }
.tech-value { font-size: 1rem; font-weight: 700; }

/* ─── Accordions ─────────────────────────────────────────────────────────────── */
.acc-content p { color: #888; font-size: 0.82rem; margin-bottom: 8px; line-height: 1.5; }
.smart-item { display: flex !important; align-items: flex-start; gap: 10px; white-space: normal; }
.smart-letter { font-size: 0.8rem; font-weight: 800; min-width: 14px; }
.smart-text   { font-size: 0.8rem; color: #aaa; }

ion-accordion.accordion-expanding ion-item[slot='header'],
ion-accordion.accordion-expanded  ion-item[slot='header'] {
  --background: rgba(var(--ion-color-primary-rgb), 0.1);
  color: var(--ion-color-primary);
}
</style>