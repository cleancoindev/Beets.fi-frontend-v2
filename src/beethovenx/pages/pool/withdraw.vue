<script setup lang="ts">
import { configService } from '@/services/config/config.service';
// Composables
import usePoolTransfers from '@/composables/contextual/pool-transfers/usePoolTransfers';
// Components
import WithdrawForm from '@/components/forms/pool_actions/WithdrawForm/WithdrawForm.vue';
import TradeSettingsPopover, {
  TradeSettingsContext
} from '@/components/popovers/TradeSettingsPopover.vue';
import { computed } from 'vue';

/**
 * STATE
 */
const { network } = configService;
const { pool, loadingPool, transfersAllowed } = usePoolTransfers();

const hasBptStaked = computed(
  () => pool.value && pool.value.farm && pool.value.farm.userBpt > 0
);
</script>

<template>
  <div>
    <BalLoadingBlock v-if="loadingPool || !transfersAllowed" class="h-96" />
    <BalCard v-else shadow="xl" exposeOverflow noBorder>
      <template #header>
        <div class="w-full">
          <div class="text-xs text-gray-500 leading-none">
            {{ network.chainName }}
          </div>
          <div class="flex items-center justify-between">
            <h4>{{ $t('withdrawFromPool') }}</h4>
            <TradeSettingsPopover :context="TradeSettingsContext.invest" />
          </div>
          <BalAlert
            v-if="hasBptStaked"
            title="You have BPT staked in the farm"
            description="To withdraw your funds, you need to first unstake your BPT from the farm."
            type="warning"
            size="sm"
            class="mt-2 mb-2"
          />
        </div>
      </template>
      <WithdrawForm :pool="pool" />
    </BalCard>
  </div>
</template>
