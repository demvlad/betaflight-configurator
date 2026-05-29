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
                        :min="5"
                        :max="200"
                        size="xs"
                        orientation="vertical"
                        class="w-full"
                    />

                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasRollDampingCli"
                            :step="1"
                            :min="0"
                            :max="255"
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
                        :min="5"
                        :max="255"
                        size="xs"
                        orientation="vertical"
                        class="w-full"
                    />

                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasPitchDampingCli"
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
                            v-model="psasPitchStabilityCli"
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
                        :max="200"
                        size="xs"
                        orientation="vertical"
                        class="w-full"
                    />

                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasYawDampingCli"
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
                            v-model="psasYawStabilityCli"
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

            <UiBox title="Accel Z controller" type="neutral">
                <div class="grid grid-cols-[3rem_repeat(1,minmax(4rem,auto))] gap-x-3 gap-y-1 items-center min-w-0">
                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">I</div>
                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasAccelICli"
                            :step="1"
                            :min="0"
                            :max="255"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasAccelI"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>

                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">P</div>
                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasAccelPCli"
                            :step="1"
                            :min="0"
                            :max="1000"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasAccelP"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>

                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">
                        G load max
                    </div>
                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasGLoadMaxCli"
                            :step="1"
                            :min="20"
                            :max="255"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasGLoadMax"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>

                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">
                        G load min
                    </div>
                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasGLoadMinCli"
                            :step="1"
                            :min="10"
                            :max="255"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasGLoadMin"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>

                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">
                        Servo time
                    </div>
                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasServoTimeCli"
                            :step="1"
                            :min="5"
                            :max="1000"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasServoTime"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>
                </div>
            </UiBox>

            <UiBox title="AoA limiter" type="neutral">
                <div class="grid grid-cols-[3rem_repeat(1,minmax(4rem,auto))] gap-x-3 gap-y-1 items-center min-w-0">
                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">
                        Wing loading
                    </div>
                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasWingLoadingCli"
                            :step="1"
                            :min="0"
                            :max="1500"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasWingLoading"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>

                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">
                        Limiter I
                    </div>
                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasLimiterGainCli"
                            :step="1"
                            :min="0"
                            :max="255"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasLimiterGain"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>

                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">
                        Limiter forecast
                    </div>
                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasLimiterForecastCli"
                            :step="1"
                            :min="0"
                            :max="255"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasLimiterForecast"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>

                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">
                        Return time
                    </div>
                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasLimiterTauReturnCli"
                            :step="1"
                            :min="1"
                            :max="50"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasLimiterTauReturn"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>
                </div>
            </UiBox>

            <UiBox title="Roll to yaw cross link" type="neutral">
                <div class="grid grid-cols-[3rem_repeat(1,minmax(4rem,auto))] gap-x-3 gap-y-1 items-center min-w-0">
                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">Gain</div>
                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasRollToYawLinkCli"
                            :step="1"
                            :min="0"
                            :max="255"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasRollToYawLink"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>

                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">
                        Lift coef start
                    </div>
                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasRollToYawLiftCoefStartCli"
                            :step="1"
                            :min="0"
                            :max="20"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasRollToYawLiftCoefStart"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>

                    <div class="font-bold text-white text-center py-0.5 px-1 bg-[#e24761] rounded text-xs">
                        Lift coef stop
                    </div>
                    <div style="display: flex">
                        <UInputNumber
                            v-model="psasRollToYawLiftCoefStopCli"
                            :step="1"
                            :min="0"
                            :max="20"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                        <UInputNumber
                            v-model="psasRollToYawLiftCoefStop"
                            :disabled="true"
                            size="xs"
                            orientation="vertical"
                            class="w-full"
                        />
                    </div>
                </div>
            </UiBox>

            <!-- Save/Revert Buttons -->
            <div class="content_toolbar toolbar_fixed_bottom flex items-center gap-2">
                <UButton label="Default" @click="reset" />
                <UButton
                    :label="$t('pidTuningButtonRefresh')"
                    :disabled="!hasChanges"
                    @click="refresh"
                    variant="soft"
                />
                <UButton :label="$t('pidTuningButtonSave')" :disabled="!hasChanges" @click="save" />
            </div>
        </div>
    </BaseTab>
</template>

<script setup>
import { ref, watch, computed, onMounted } from "vue";
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

const currentProfile = ref(FC.CONFIG.profile);

// Items arrays for USelect / UTabs
const profileItems = computed(() => {
    const items = [];
    for (let i = 0; i < 4; i++) {
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

const hasChanges = ref(null);
watch(
    () => JSON.stringify(FC.PSAS_CONFIG),
    () => (hasChanges.value = hasChanges.value !== null),
);

let defaultSettings = null;
async function loadData() {
    if (!defaultSettings) {
        defaultSettings = structuredClone(FC.PSAS_CONFIG);
    }
    try {
        await MSP.promise(MSPCodes.MSP_PSAS_CONFIG);
        initializeUI();
        hasChanges.value = false;
    } catch (e) {
        console.error("Failed to load PSAS configs", e);
        GUI.content_ready();
    }
}

function initializeUI() {
    if (!FC.PSAS_CONFIG || FC.PSAS_CONFIG.length === 0) {
        GUI.content_ready();
        return;
    }

    GUI.content_ready();
}

// Save/Refresh
async function save() {
    if (!hasChanges.value) {
        return;
    }

    try {
        // Save PIDs
        await MSP.promise(MSPCodes.MSP_SET_PSAS_CONFIG, mspHelper.crunch(MSPCodes.MSP_SET_PSAS_CONFIG));

        // Write to EEPROM
        await MSP.promise(MSPCodes.MSP_EEPROM_WRITE);

        hasChanges.value = false;
    } catch (e) {
        console.error("[PidTuning] Save failed:", e);
    }
}

async function refresh() {
    try {
        if (await loadData()) {
            gui_log(t("pidTuningDataRefreshed"));
            hasChanges.value = false;
        }
    } catch (error) {
        console.error("[PidTuningTab] Failed to refresh data:", error);
    }
}

async function reset() {
    FC.PSAS_CONFIG = structuredClone(defaultSettings);
}

onMounted(() => {
    loadData();
});

// PSAS - Individual per-cell computed properties to avoid array destruction.
// v-model.number writes a single number, so using array-level computeds would
// replace the sub-array reference with that number. Instead, read/write each
// element directly.

// Main settings
// Roll
const psasRollPilot = computed({
    get: () => FC.PSAS_CONFIG.stick_gain[0],
    set: (val) => {
        FC.PSAS_CONFIG.stick_gain[0] = val;
    },
});

const psasRollDampingCli = computed({
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

const psasPitchDampingCli = computed({
    get: () => FC.PSAS_CONFIG.damping_gain[1],
    set: (val) => {
        FC.PSAS_CONFIG.damping_gain[1] = val;
    },
});

const psasPitchDamping = computed({
    get: () => FC.PSAS_CONFIG.damping_gain[1] * 0.001,
});

const psasPitchStabilityCli = computed({
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

const psasYawDampingCli = computed({
    get: () => FC.PSAS_CONFIG.damping_gain[2],
    set: (val) => {
        FC.PSAS_CONFIG.damping_gain[2] = val;
    },
});

const psasYawDamping = computed({
    get: () => FC.PSAS_CONFIG.damping_gain[2] * 0.001,
});

const psasYawStabilityCli = computed({
    get: () => FC.PSAS_CONFIG.yaw_stability_gain,
    set: (val) => {
        FC.PSAS_CONFIG.yaw_stability_gain = val;
    },
});

const psasYawStability = computed({
    get: () => FC.PSAS_CONFIG.yaw_stability_gain * 0.1,
});

// Accel Z controller
const psasAccelICli = computed({
    get: () => FC.PSAS_CONFIG.pitch_accel_i_gain,
    set: (val) => {
        FC.PSAS_CONFIG.pitch_accel_i_gain = val;
    },
});

const psasAccelI = computed({
    get: () => FC.PSAS_CONFIG.pitch_accel_i_gain,
});

const psasAccelPCli = computed({
    get: () => FC.PSAS_CONFIG.pitch_accel_p_gain,
    set: (val) => {
        FC.PSAS_CONFIG.pitch_accel_p_gain = val;
    },
});

const psasAccelP = computed({
    get: () => FC.PSAS_CONFIG.pitch_accel_p_gain * 0.1,
});

const psasGLoadMaxCli = computed({
    get: () => FC.PSAS_CONFIG.pitch_accel_max,
    set: (val) => {
        FC.PSAS_CONFIG.pitch_accel_max = val;
    },
});

const psasGLoadMax = computed({
    get: () => FC.PSAS_CONFIG.pitch_accel_max * 0.1,
});

const psasGLoadMinCli = computed({
    get: () => FC.PSAS_CONFIG.pitch_accel_min,
    set: (val) => {
        FC.PSAS_CONFIG.pitch_accel_min = val;
    },
});

const psasGLoadMin = computed({
    get: () => -FC.PSAS_CONFIG.pitch_accel_min * 0.1,
});

const psasServoTimeCli = computed({
    get: () => FC.PSAS_CONFIG.servo_time,
    set: (val) => {
        FC.PSAS_CONFIG.servo_time = val;
    },
});

const psasServoTime = computed({
    get: () => FC.PSAS_CONFIG.servo_time * 0.001,
});

// AoA limiter
const psasWingLoadingCli = computed({
    get: () => FC.PSAS_CONFIG.wing_load,
    set: (val) => {
        FC.PSAS_CONFIG.wing_load = val;
    },
});

const psasWingLoading = computed({
    get: () => FC.PSAS_CONFIG.wing_load * 0.1,
});

const psasLimiterGainCli = computed({
    get: () => FC.PSAS_CONFIG.aoa_limiter_gain,
    set: (val) => {
        FC.PSAS_CONFIG.aoa_limiter_gain = val;
    },
});

const psasLimiterGain = computed({
    get: () => FC.PSAS_CONFIG.aoa_limiter_gain,
});

const psasLimiterForecastCli = computed({
    get: () => FC.PSAS_CONFIG.aoa_limiter_forecast_time,
    set: (val) => {
        FC.PSAS_CONFIG.aoa_limiter_forecast_time = val;
    },
});

const psasLimiterForecast = computed({
    get: () => FC.PSAS_CONFIG.aoa_limiter_forecast_time * 0.01,
});

const psasLimiterTauReturnCli = computed({
    get: () => FC.PSAS_CONFIG.aoa_limiter_tau_return,
    set: (val) => {
        FC.PSAS_CONFIG.aoa_limiter_tau_return = val;
    },
});

const psasLimiterTauReturn = computed({
    get: () => FC.PSAS_CONFIG.aoa_limiter_tau_return * 0.1,
});

const psasRollToYawLinkCli = computed({
    get: () => FC.PSAS_CONFIG.roll_to_yaw_link,
    set: (val) => {
        FC.PSAS_CONFIG.roll_to_yaw_link = val;
    },
});

const psasRollToYawLink = computed({
    get: () => FC.PSAS_CONFIG.roll_to_yaw_link * 0.1,
});

const psasRollToYawLiftCoefStartCli = computed({
    get: () => FC.PSAS_CONFIG.roll_yaw_clift_start,
    set: (val) => {
        FC.PSAS_CONFIG.roll_yaw_clift_start = val;
    },
});

const psasRollToYawLiftCoefStart = computed({
    get: () => FC.PSAS_CONFIG.roll_yaw_clift_start * 0.1,
});

const psasRollToYawLiftCoefStopCli = computed({
    get: () => FC.PSAS_CONFIG.roll_yaw_clift_stop,
    set: (val) => {
        FC.PSAS_CONFIG.roll_yaw_clift_stop = val;
    },
});

const psasRollToYawLiftCoefStop = computed({
    get: () => FC.PSAS_CONFIG.roll_yaw_clift_stop * 0.1,
});
</script>
