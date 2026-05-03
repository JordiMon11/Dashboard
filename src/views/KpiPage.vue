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

      <h1 class="section-title">KPIs de Negocio</h1>

      <div class="kpi-grid">
        <div class="kpi-card" v-for="kpi in businessKpis" :key="kpi.id">
          <div class="kpi-top">
            <span class="kpi-title">{{ kpi.title }}</span>
            <span class="kpi-badge" :class="kpi.status">{{ kpi.statusLabel }}</span>
          </div>
          <div class="kpi-progress-bar">
            <div class="kpi-progress-fill" :style="{ width: Math.min(kpi.progress, 100) + '%', background: kpi.color }"></div>
          </div>
          <div class="kpi-bottom">
            <span class="kpi-current">{{ kpi.current }}</span>
            <span class="kpi-target">Meta: {{ kpi.target }}</span>
            <span class="kpi-pct">{{ kpi.progress }}%</span>
          </div>
          <p class="kpi-desc">{{ kpi.description }}</p>
        </div>
      </div>

      <h1 class="section-title">KPIs Tecnicos</h1>

      <ion-accordion-group expand="inset" :multiple="true">
        <ion-accordion v-for="item in techKpis" :key="item.id" :value="item.id.toString()">
          <ion-item slot="header">
            <ion-label>{{ item.title }} — <strong>{{ item.current }}</strong></ion-label>
          </ion-item>
          <div class="ion-padding acc-content" slot="content">
            <p>{{ item.description }}</p>
            <ion-list :inset="true">
              <ion-item v-for="(element, index) in item.smart" :key="index">
                <ion-label><b>{{ element.letter }}</b> — {{ element.content }}</ion-label>
              </ion-item>
            </ion-list>
          </div>
        </ion-accordion>
      </ion-accordion-group>

      <br />
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar, IonAccordionGroup, IonAccordion, IonItem, IonLabel, IonList } from '@ionic/vue';
import { ref } from 'vue';

const businessKpis = ref([
  { id: 1, title: 'Ventas Anuales', current: '248.500 EUR', target: '240.000 EUR', progress: 103, color: '#3ecf8e', status: 'achieved', statusLabel: 'Superado', description: 'Meta anual de ventas superada en un 3,5%. El mes de julio registró el pico histórico con 52.000 EUR.' },
  { id: 2, title: 'Tasa de Conversión', current: '3,02%', target: '3,5%', progress: 86, color: '#f5a623', status: 'on-track', statusLabel: 'En Progreso', description: 'Actualmente en 3,02%. Necesitamos mejorar el proceso de checkout y reducir el abandono de carrito.' },
  { id: 3, title: 'Clientes Nuevos', current: '1.204', target: '1.500', progress: 80, color: '#4f9cf9', status: 'on-track', statusLabel: 'En Progreso', description: 'Adquisición de nuevos clientes al 80% del objetivo. Las campañas de verano han aportado el 40%.' },
  { id: 4, title: 'Satisfacción Cliente (NPS)', current: '72', target: '75', progress: 96, color: '#a78bfa', status: 'on-track', statusLabel: 'En Progreso', description: 'NPS de 72 puntos, muy cerca del objetivo. Las valoraciones de producto son positivas pero el servicio posventa necesita mejora.' },
  { id: 5, title: 'Tasa de Retención', current: '64%', target: '70%', progress: 91, color: '#e879a0', status: 'on-track', statusLabel: 'En Progreso', description: 'El 64% de clientes repiten compra en el año. El programa de fidelización lanzado en Q3 está dando resultados.' },
  { id: 6, title: 'Ticket Medio', current: '64,7 EUR', target: '60 EUR', progress: 100, color: '#3ecf8e', status: 'achieved', statusLabel: 'Superado', description: 'El ticket medio supera el objetivo gracias al upselling de accesorios y packs de temporada.' },
]);

const techKpis = ref([
  {
    id: 1, title: 'Velocidad de Carga', current: '242ms',
    description: 'Reducir el tiempo de carga de la web de 340ms a 250ms en los próximos 3 meses optimizando imágenes y caché.',
    smart: [
      { letter: 'S', content: 'Reducir el tiempo de carga promedio de la web' },
      { letter: 'M', content: 'De 340ms a menos de 250ms (actual: 242ms)' },
      { letter: 'A', content: 'Optimizando imágenes WebP, implementando CDN y caché de navegador' },
      { letter: 'R', content: 'Mejorar la experiencia de usuario y el posicionamiento SEO' },
      { letter: 'T', content: 'En los próximos 3 meses' },
    ]
  },
  {
    id: 2, title: 'Tráfico Movil', current: '58%',
    description: 'Aumentar la conversión desde móvil del 1,8% al 2,8% rediseñando el flujo de compra para dispositivos táctiles.',
    smart: [
      { letter: 'S', content: 'Mejorar la conversión en dispositivos móviles' },
      { letter: 'M', content: 'De 1,8% a 2,8% de conversión móvil' },
      { letter: 'A', content: 'Rediseñando el checkout móvil y añadiendo Apple Pay / Google Pay' },
      { letter: 'R', content: 'El 58% del tráfico es móvil — es el canal con mayor potencial de crecimiento' },
      { letter: 'T', content: 'Antes del inicio de la temporada de rebajas (enero 2025)' },
    ]
  },
  {
    id: 3, title: 'Posicionamiento SEO', current: 'Posición 4',
    description: 'Alcanzar la posición top 3 en búsquedas de "ropa online España" y "moda sostenible" en 6 meses.',
    smart: [
      { letter: 'S', content: 'Alcanzar el top 3 en Google para palabras clave principales' },
      { letter: 'M', content: 'Pasar de posición 4 a posición 1-3 en 5 keywords objetivo' },
      { letter: 'A', content: 'Publicando 3 artículos SEO semanales y mejorando backlinks' },
      { letter: 'R', content: 'El 42% del tráfico actual proviene de búsqueda orgánica' },
      { letter: 'T', content: 'En 6 meses' },
    ]
  },
  {
    id: 4, title: 'Reducción de Errores', current: '3 errores/mes',
    description: 'Reducir los errores 5xx de 5 a menos de 2 por mes mejorando la infraestructura y los tests automáticos.',
    smart: [
      { letter: 'S', content: 'Reducir los errores de servidor críticos (5xx)' },
      { letter: 'M', content: 'De 5 errores/mes a menos de 2 errores/mes' },
      { letter: 'A', content: 'Implementando monitorización 24h y pipelines de CI/CD con tests' },
      { letter: 'R', content: 'Cada error 5xx supone una pérdida media de 800 EUR en ventas perdidas' },
      { letter: 'T', content: 'En los próximos 2 meses' },
    ]
  },
]);
</script>

<style scoped>
.section-title { padding: 16px 8px 8px 8px; font-size: 1.1rem; font-weight: 700; color: #e0e0e0; text-transform: uppercase; letter-spacing: 0.05em; }

.kpi-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 12px;
  margin-bottom: 24px;
}
.kpi-card {
  background: #1a1a2e;
  border: 1px solid #2a2a4a;
  border-radius: 12px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}
.kpi-top { display: flex; align-items: center; gap: 8px; flex-wrap: wrap; }
.kpi-title { font-size: 0.88rem; font-weight: 600; color: #e0e0e0; flex: 1; }
.kpi-badge { font-size: 0.68rem; padding: 2px 10px; border-radius: 20px; font-weight: 600; text-transform: uppercase; letter-spacing: 0.04em; }
.kpi-badge.achieved { background: rgba(62,207,142,0.15); color: #3ecf8e; border: 1px solid rgba(62,207,142,0.3); }
.kpi-badge.on-track { background: rgba(79,156,249,0.15); color: #4f9cf9; border: 1px solid rgba(79,156,249,0.3); }
.kpi-badge.at-risk { background: rgba(232,121,160,0.15); color: #e879a0; border: 1px solid rgba(232,121,160,0.3); }
.kpi-progress-bar { background: #2a2a4a; border-radius: 999px; height: 6px; overflow: hidden; }
.kpi-progress-fill { height: 100%; border-radius: 999px; transition: width 0.6s ease; }
.kpi-bottom { display: flex; align-items: center; gap: 8px; }
.kpi-current { font-size: 1.1rem; font-weight: 700; color: #fff; flex: 1; }
.kpi-target { font-size: 0.72rem; color: #888; }
.kpi-pct { font-size: 0.8rem; font-weight: 600; color: #aaa; }
.kpi-desc { font-size: 0.75rem; color: #777; margin: 0; line-height: 1.5; }

.acc-content p { color: #aaa; font-size: 0.85rem; margin-bottom: 8px; }

ion-accordion.accordion-collapsing ion-item[slot='header'],
ion-accordion.accordion-collapsed ion-item[slot='header'] {
  --background: var(--ion-color-light);
  --color: var(--ion-color-light-contrast);
}
ion-accordion.accordion-expanding ion-item[slot='header'],
ion-accordion.accordion-expanded ion-item[slot='header'] {
  --background: rgba(var(--ion-color-primary-rgb), 0.14);
  color: var(--ion-color-primary);
}
</style>
