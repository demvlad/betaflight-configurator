<template>
    <BaseTab tab-name="PSAS">
        <div class="content_wrapper">
            <div class="tab_title" v-html="$t('tabPsasTuning')"></div>
            <div class="cf_doc_version_bt">
                <WikiButton docUrl="PSAS" />
            </div>

            <div class="flex items-start gap-3 flex-wrap mb-2">
                <!-- Profile Selector -->
                <div class="flex flex-col gap-1 min-w-[130px]">
                    <SettingRow :label="$t('pidTuningProfile')" :help="$t('pidTuningProfileTip')">
                        <USelect
                            v-model="currentProfile"
                            :items="profileItems"
                            class="min-w-20"
                            :disabled="hasChanges"
                            @update:model-value="onProfileChange"
                        />
                    </SettingRow>
                </div>
            </div>

            <!-- SAS Table -->
            <UiBox :title="$t('psasMainSettings')" type="neutral">
                <div class="grid grid-cols-[3rem_repeat(3,minmax(4rem,auto))] gap-x-3 gap-y-1 items-center min-w-0">
                    <!-- Header -->
                    <div></div>
                    <div class="flex items-center justify-center gap-0.5 text-xs">
                        <span v-html="$t('psasTuningPilot')"></span>
                        <HelpIcon :text="$t('psasTuningPilotHelp')" />
                    </div>
                    <div class="flex items-center justify-center gap-0.5 text-xs">
                        <span v-html="$t('psasTuningDamping')"></span>
                        <HelpIcon :text="$t('psasTuningDampingHelp')" />
                    </div>
                    <div class="flex items-center justify-center gap-0.5 text-xs">
                        <span v-html="$t('psasTuningStability')"></span>
                        <HelpIcon :text="$t('psasTuningStabilityHelp')" />
                    </div>

                    <!-- ROLL -->
                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">ROLL</div>

                    <UInputNumber
                        v-model="psasRollPilot"
                        :step="1"
                        :min="0"
                        :max="255"
                        size="xs"
                        orientation="vertical"
                        class="w-full"
                    />

                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasRollDampingCLI"
                            :step="1"
                            :min="0"
                            :max="1000"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasRollDamping"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>

                    <div></div>

                    <!-- PITCH -->
                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">PITCH</div>
                    <UInputNumber
                        v-model="psasPitchPilot"
                        :step="1"
                        :min="0"
                        :max="255"
                        size="xs"
                        orientation="vertical"
                        class="w-full"
                    />

                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasPitchDampingCLI"
                            :step="1"
                            :min="0"
                            :max="1000"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasPitchDamping"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>

                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasPitchStabilityCLI"
                            :step="1"
                            :min="0"
                            :max="300"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasPitchStability"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>

                    <!-- YAW -->
                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">YAW</div>
                    <UInputNumber
                        v-model="psasYawPilot"
                        :step="1"
                        :min="0"
                        :max="255"
                        size="xs"
                        orientation="vertical"
                        class="w-full"
                    />

                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasYawDampingCLI"
                            :step="1"
                            :min="0"
                            :max="1000"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasYawDamping"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>

                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasYawStabilityCLI"
                            :step="1"
                            :min="0"
                            :max="300"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasYawStability"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>
                </div>
            </UiBox>
        </div>
    </BaseTab>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import { usePidTuningStore } from "@/stores/pidTuning";
import BaseTab from "./BaseTab.vue";
import WikiButton from "@/components/elements/WikiButton.vue";
import UiBox from "@/components/elements/UiBox.vue";
import HelpIcon from "@/components/elements/HelpIcon.vue";
import SettingRow from "../elements/SettingRow.vue";
import GUI from "@/js/gui";
import MSP from "@/js/msp";
import MSPCodes from "@/js/msp/MSPCodes";
import FC from "@/js/fc";
import { i18n } from "@/js/localization";
import { useTranslation } from "i18next-vue";

const { t } = useTranslation();
const pidTuningStore = usePidTuningStore();

const isSupported = ref(false);
const currentProfile = ref(FC.CONFIG.profile);

const originalConfigs = ref("");

// Items arrays for USelect / UTabs
const profileItems = computed(() => {
    const items = [];
    for (let i = 0; i < 3; i++) {
        items.push({ label: i18n.getMessage("pidTuningProfileOption", [i + 1]), value: i });
    }
    return items;
});

// Profile Management
async function onProfileChange() {
    FC.CONFIG.profile = currentProfile.value;

    // Select profile via MSP
    await MSP.promise(MSPCodes.MSP_SELECT_SETTING, [currentProfile.value]);

    // Reload data
    await loadData();
}

// hasChanges is owned by the Pinia store
const hasChanges = computed(() => pidTuningStore.hasChanges);

async function loadData() {
    try {
        await MSP.promise(MSPCodes.MSP_PSAS_CONFIG);
        initializeUI();
    } catch (e) {
        console.error("Failed to load PSAS configs", e);
        isSupported.value = false;
        GUI.content_ready();
    }
}

// PSAS - Individual per-cell computed properties to avoid array destruction.
// v-model.number writes a single number, so using array-level computeds would
// replace the sub-array reference with that number. Instead, read/write each
// element directly.

// Roll
const psasRollPilot = computed({
    get: () => FC.PSAS_CONFIG.stick_gain[0],
    set: (val) => {
        FC.PSAS_CONFIG.stick_gain[0] = val;
    },
});

const psasRollDampingCLI = computed({
    get: () => FC.PSAS_CONFIG.damping_gain[0],
    set: (val) => {
        FC.PSAS_CONFIG.damping_gain[0] = val;
    },
});

const psasRollDamping = computed({
    get: () => FC.PSAS_CONFIG.damping_gain[0] * 0.001,
});

// Pitch
const psasPitchPilot = computed({
    get: () => FC.PSAS_CONFIG.stick_gain[1],
    set: (val) => {
        FC.PSAS_CONFIG.stick_gain[1] = val;
    },
});

const psasPitchDampingCLI = computed({
    get: () => FC.PSAS_CONFIG.damping_gain[1],
    set: (val) => {
        FC.PSAS_CONFIG.damping_gain[1] = val;
    },
});

const psasPitchDamping = computed({
    get: () => FC.PSAS_CONFIG.damping_gain[1] * 0.001,
});

const psasPitchStabilityCLI = computed({
    get: () => FC.PSAS_CONFIG.pitch_stability_gain,
    set: (val) => {
        FC.PSAS_CONFIG.pitch_stability_gain = val;
    },
});

const psasPitchStability = computed({
    get: () => FC.PSAS_CONFIG.pitch_stability_gain * 0.1,
});

// Yaw
const psasYawPilot = computed({
    get: () => FC.PSAS_CONFIG.stick_gain[2],
    set: (val) => {
        FC.PSAS_CONFIG.stick_gain[2] = val;
    },
});

const psasYawDampingCLI = computed({
    get: () => FC.PSAS_CONFIG.damping_gain[2],
    set: (val) => {
        FC.PSAS_CONFIG.damping_gain[2] = val;
    },
});

const psasYawDamping = computed({
    get: () => FC.PSAS_CONFIG.damping_gain[2] * 0.001,
});

const psasYawStabilityCLI = computed({
    get: () => FC.PSAS_CONFIG.yaw_stability_gain,
    set: (val) => {
        FC.PSAS_CONFIG.yaw_stability_gain = val;
    },
});

const psasYawStability = computed({
    get: () => FC.PSAS_CONFIG.yaw_stability_gain * 0.1,
});

function initializeUI() {
    if (!FC.PSAS_CONFIG || FC.PSAS_CONFIG.length === 0) {
        isSupported.value = false;
        GUI.content_ready();
        return;
    }

    isSupported.value = true;

    GUI.content_ready();
}

onMounted(() => {
    loadData();
});
</script>
