<template>
    <div class="data-card">
        <v-card-title>
            <div class="d-flex align-center">
                <!-- Rank badge for top differences tab -->
                <span v-if="showRank" class="rank-badge mr-2">{{ rank }}</span>

                <!-- ID Chip - always shown -->
                <v-chip :color="isCurrentConcept ? 'purple' : 'black'" class="ma-2">
                    <v-icon>mdi-passport</v-icon>
                    {{ concept.id }}
                </v-chip>

                <!-- Activations Chip - always shown -->
                <v-chip color="orange" class="ma-2">
                    <v-icon>mdi-image-area</v-icon>
                    {{ concept.nb_fire }}
                </v-chip>

                <!-- Energy Diff Chip -->
                <v-chip v-if="!hideEnergyDiff" class="ma-2" :style="diffChipStyle">
                    <v-icon>mdi-thermometer</v-icon>
                    {{ concept.energy_diff.toFixed(3) }}
                </v-chip>

                <!-- Relative Energy Diff Chip -->
                <v-chip v-if="!hideRelativeDiff" class="ma-2" :style="relativeDiffChipStyle">
                    <v-icon>mdi-thermometer-auto</v-icon>
                    {{ concept.relative_energy_diff.toFixed(3) }}
                </v-chip>

                <!-- Co-occurrence Chip -->
                <v-chip v-if="showCoOccurrence" color="indigo" class="ma-2">
                    <v-icon>mdi-bridge</v-icon>
                    {{ concept.links_value }}
                </v-chip>

                <!-- Magnify button (only in top diff tab) -->
                <v-btn v-if="showMagnify" icon size="small" @click="onMagnifyClick">
                    <v-icon>mdi-magnify</v-icon>
                </v-btn>
            </div>
        </v-card-title>

        <v-card-text>
            <v-row>
                <!-- Main concept visualization -->
                <v-col cols="12">
                    <v-img
                        :src="'https://kempner-prod-thomasfel-storage.s3.amazonaws.com/dinov2/top_heatmaps/' + concept.id + '.webp'"
                        :class="{ 'compact': compact }"
                        lazy-src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 1 1' height='500' width='500'%3E%3C/svg%3E"
                        :alt="`Concept ${concept.id}`">
                        <template v-slot:placeholder>
                            <v-row class="fill-height ma-0" align="center" justify="center">
                                <v-progress-circular indeterminate color="grey-lighten-5"></v-progress-circular>
                            </v-row>
                        </template>
                    </v-img>
                </v-col>

                <!-- Example images (only shown when showExamples is true) -->
                <template v-if="showExamples">
                    <v-col cols="6">
                        <v-img
                            :src="'https://huggingface.co/datasets/matybohacek/sae-diff/resolve/main/sample10k_' + modelKey + '/sample_' + concept.top_original_image + '/original.jpg'"
                            lazy-src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 1 1' height='500' width='500'%3E%3C/svg%3E"
                            :alt="`Top Original Image ${concept.id}`">
                            <template v-slot:placeholder>
                                <v-row class="fill-height ma-0" align="center" justify="center">
                                    <v-progress-circular indeterminate color="grey-lighten-5"></v-progress-circular>
                                </v-row>
                            </template>
                        </v-img>
                    </v-col>
                    <v-col cols="6">
                        <v-img
                            :src="'https://huggingface.co/datasets/matybohacek/sae-diff/resolve/main/sample10k_' + modelKey + '/sample_' + concept.top_ai_image + '/ai.jpg'"
                            lazy-src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 1 1' height='500' width='500'%3E%3C/svg%3E"
                            :alt="`Top AI Image ${concept.id}`">
                            <template v-slot:placeholder>
                                <v-row class="fill-height ma-0" align="center" justify="center">
                                    <v-progress-circular indeterminate color="grey-lighten-5"></v-progress-circular>
                                </v-row>
                            </template>
                        </v-img>
                    </v-col>
                </template>
            </v-row>
        </v-card-text>
    </div>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
    concept: {
        type: Object,
        required: true
    },
    modelKey: {
        type: String,
        required: true
    },
    isCurrentConcept: {
        type: Boolean,
        default: false
    },
    compact: {
        type: Boolean,
        default: false
    },
    showExamples: {
        type: Boolean,
        default: false
    },
    showCoOccurrence: {
        type: Boolean,
        default: false
    },
    hideEnergyDiff: {
        type: Boolean,
        default: false
    },
    hideRelativeDiff: {
        type: Boolean,
        default: false
    },
    showRank: {
        type: Boolean,
        default: false
    },
    rank: {
        type: Number,
        default: 0
    },
    showMagnify: {
        type: Boolean,
        default: false
    }
});

const emit = defineEmits(['magnify-click']);

// Helper functions to compute styles for chips
const getTextColor = (rgbArray) => {
    const r = rgbArray[0] * 255;
    const g = rgbArray[1] * 255;
    const b = rgbArray[2] * 255;
    // Perceived luminance (ITU-R BT.709)
    const luminance = 0.2126 * r + 0.7152 * g + 0.0722 * b;
    return luminance > 128 ? 'black' : 'white';
};

const diffChipStyle = computed(() => {
    const colorArray = props.concept.color_diff;
    return {
        background: `rgba(${colorArray[0] * 255}, ${colorArray[1] * 255}, ${colorArray[2] * 255}, 1)`,
        color: getTextColor(colorArray)
    };
});

const relativeDiffChipStyle = computed(() => {
    const colorArray = props.concept.color_relative_diff;
    return {
        background: `rgba(${colorArray[0] * 255}, ${colorArray[1] * 255}, ${colorArray[2] * 255}, 1)`,
        color: getTextColor(colorArray)
    };
});

const onMagnifyClick = () => {
    emit('magnify-click', props.concept);
};
</script>

<style scoped>
.data-card {
    transition: all 0.2s ease;
    opacity: 0.8;
    border: dashed 2px transparent;
    margin: 3px;
    border-radius: 3px;
}

.data-card:hover {
    opacity: 1.0;
    border-color: #cbd5e1;
}

img.compact {
    object-fit: cover;
}

img {
    object-fit: contain;
    width: 100%;
    height: 100%;
}

.rank-badge {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    width: 24px;
    height: 24px;
    background-color: #64748b;
    color: white;
    border-radius: 50%;
    font-size: 0.8rem;
    font-weight: bold;
}
</style>