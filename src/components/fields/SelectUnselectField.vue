<template>
    <div class="select-unselect-container">
        <div class="column">
            <h3>Available options</h3>
            <select multiple :name="field.id" v-model="selectedAvailable" class="options-list" size="8" @change="updateData" ref="selectRef" :class="{ 'required-highlight': highlightRequired }">
                <option v-for="option in availableOptions" :key="option.id" :value="option.id">
                    {{ option.label }}
                </option>
            </select>
        </div>
        <div class="column">
            <h3>Disabled options</h3>
            <select multiple class="options-list" size="8" disabled>
                <option v-for="option in disabledOptionsList" :key="option.id" :value="option.id">
                    {{ option.label }}
                </option>
            </select>
        </div>
    </div>
</template>

<script setup>
import fieldMixin from '../FieldMixin';
import { ref, watch, computed } from 'vue';
const emit = defineEmits(['update']);
const props = defineProps({
    field: {
        type: Object,
        required: true,
    },
    modelValue: {
        type: [Array, Object, String],
        default: () => ([]),
    },
    disabledOptions: {
        type: Array,
        default: () => ([]),
    }
});
let { handleChange, debounce } = fieldMixin.setup(props, { emit });

const selectedAvailable = ref([]);
const selectRef = ref(null);
const highlightRequired = ref(false);

// Calcular las opciones deshabilitadas a partir de los ids
const disabledOptionsList = computed(() => {
    return props.field.options.filter(opt => props.disabledOptions.includes(opt.id));
});
// Filtrar las opciones disponibles para que no estén las deshabilitadas
const availableOptions = computed(() => {
    return props.field.options.filter(opt => !props.disabledOptions.includes(opt.id));
});

// Inicializar selección desde modelValue
watch(() => props.modelValue, (newVal) => {
    if (Array.isArray(newVal)) {
        selectedAvailable.value = newVal;
    } else if (newVal && newVal.available) {
        selectedAvailable.value = newVal.available.map(opt => opt.id);
    } else {
        selectedAvailable.value = [];
    }
}, { immediate: true });

const updateData = (event) => {
    debounce(handleChange, 50)(event);
};

function showRequiredFeedback() {
    highlightRequired.value = true;
    setTimeout(() => {
        highlightRequired.value = false;
    }, 1200);
}

defineExpose({ showRequiredFeedback });
</script>

<style scoped>
.select-unselect-container {
    display: flex;
    gap: 20px;
}

.column {
    flex: 1;
    padding: 10px;
}

.options-list {
    list-style: none;
    padding: 0;
    margin: 0;
    background: #181818; /* fondo negro */
    color: #fff;          /* texto blanco */
    min-height: 180px;
    min-width: 300px;
    overflow-y: hidden;
}

.option-item {
    padding: 8px 12px;
    margin: 0;
    background: transparent; /* sin fondo gris */
    color: #fff;
    border: none;
    border-radius: 0;
    cursor: pointer;
    text-align: left;
    transition: background 0.2s;
}

.option-item:hover {
    background: #333; /* resalta con gris oscuro */
    color: #fff;
}

h3 {
    margin-top: 0;
    margin-bottom: 10px;
    text-align: center;
    color: #fff;
}

.required-highlight {
    border: 2px solid #ff4d4f !important;
    box-shadow: 0 0 0 2px #ff4d4f33;
}
</style>
