<template>
    <div ref="mapElement" class="task-location-map" aria-label="Mapa da localização" />
</template>

<script setup>
import { nextTick, onBeforeUnmount, onMounted, ref, watch } from 'vue'
import L from 'leaflet'
import 'leaflet/dist/leaflet.css'
import markerIcon from 'leaflet/dist/images/marker-icon.png'
import markerIcon2x from 'leaflet/dist/images/marker-icon-2x.png'
import markerShadow from 'leaflet/dist/images/marker-shadow.png'

L.Icon.Default.mergeOptions({
    iconRetinaUrl: markerIcon2x,
    iconUrl: markerIcon,
    shadowUrl: markerShadow,
})

const props = defineProps({
    location: {
        type: Object,
        required: true,
    },
})

const mapElement = ref(null)
let map
let marker
let accuracyCircle

function renderLocation() {
    if (!map) return
    const { latitude, longitude, label, accuracy } = props.location
    const point = [latitude, longitude]

    map.setView(point, 17)
    marker?.remove()
    accuracyCircle?.remove()
    marker = L.marker(point).addTo(map)

    if (label) {
        marker.bindPopup(label).openPopup()
    }
    if (accuracy > 0) {
        accuracyCircle = L.circle(point, {
            radius: accuracy,
            color: '#4a90d9',
            fillColor: '#4a90d9',
            fillOpacity: 0.15,
        }).addTo(map)
    }
    nextTick(() => map.invalidateSize())
}

onMounted(() => {
    map = L.map(mapElement.value).setView([0, 0], 2)
    L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution:
            '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
    }).addTo(map)
    renderLocation()
})

watch(() => props.location, renderLocation, { deep: true })

onBeforeUnmount(() => map?.remove())
</script>

<style scoped>
.task-location-map {
    width: 100%;
    height: 240px;
    margin-top: 12px;
    border-radius: 16px;
    border: 1px solid #cbd5e0;
    overflow: hidden;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}
</style>