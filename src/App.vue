<template>
    <v-app>
        <v-app-bar app elevation="2">

            <img src="@/assets/images/logo.png" alt="DINO Vision" class="dinovision-logo"
                style="max-height: 60px; margin-left: 15px" />

            <v-app-bar-title class="text-h5 font-weight-bold">
                <v-app-bar-title>SAE<b>Diff</b></v-app-bar-title>
                <v-spacer></v-spacer>
            </v-app-bar-title>

            <v-spacer></v-spacer>

            <v-btn icon>
                <v-icon>mdi-information-outline</v-icon>
            </v-btn>

            <v-btn icon>
                <v-icon>mdi-cog-outline</v-icon>
            </v-btn>

            <!-- Theme Toggle -->
            <v-btn icon @click="toggleTheme">
                <v-icon>{{ isDarkTheme ? 'mdi-weather-sunny' : 'mdi-weather-night' }}</v-icon>
            </v-btn>
        </v-app-bar>

        <v-main style="margin: auto">
            <div style="max-width: 1600px;">
                <ScatterPlotDemo />
            </div>
        </v-main>

        <!-- Footer -->
        <v-footer app absolute class="">
            <span>&copy; {{ new Date().getFullYear() }} -- NeurIPS Submission</span>
            <v-spacer></v-spacer>
            Anonymous authors
            <!--
            <span><a href="https://www.matyasbohacek.com">Maty(as) Bohacek</a> @ Stanford University</span>
            <span style="margin-left: 10px; margin-right: 10px;">|</span>
            <span><a href="https://thomasfel.me">Thomas Fel</a> @ Kempner Institute, Harvard University</span>
            -->
        </v-footer>
    </v-app>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue';
import { useTheme, useDisplay } from 'vuetify';
import ScatterPlotDemo from './components/canvas.vue';

// Reactive state
const drawer = ref(false);
// default to dark theme
const theme = useTheme();
const isDarkTheme = computed(() => theme.global.name.value === 'dark');
const isFullscreen = ref(false);
const scatterPlot = ref(null);

// Functions
function toggleTheme() {
    theme.global.name.value = isDarkTheme.value ? 'light' : 'dark';
}
</script>

<style>
.gradient-header {
    background: linear-gradient(to right, #1E293B, #334155);
    color: white;
}

.v-card {
    border-radius: 8px;
    overflow: hidden;
}

/* Customize scrollbars for a more modern look */
::-webkit-scrollbar {
    width: 8px;
    height: 8px;
}

::-webkit-scrollbar-track {
    background: rgba(100, 116, 139, 0.1);
}

::-webkit-scrollbar-thumb {
    background-color: rgba(100, 116, 139, 0.3);
    border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
    background-color: rgba(100, 116, 139, 0.5);
}
</style>