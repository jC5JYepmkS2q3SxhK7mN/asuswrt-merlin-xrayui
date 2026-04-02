<template>
  <tbody v-if="transport.hysteriaSettings">
    <tr>
      <th>
        {{ $t('com.NetworkHysteria.label_auth') }}
        <hint v-html="$t('com.NetworkHysteria.hint_auth')"></hint>
      </th>
      <td>
        <input type="text" class="input_25_table" v-model="transport.hysteriaSettings.auth" autocomplete="off" autocorrect="off" autocapitalize="off" />
      </td>
    </tr>
    <tr>
      <th>
        {{ $t('com.NetworkHysteria.label_congestion') }}
        <hint v-html="$t('com.NetworkHysteria.hint_congestion')"></hint>
      </th>
      <td>
        <select class="input_option" v-model="transport.hysteriaSettings.congestion">
          <option v-for="(opt, index) in congestionOptions" :key="index" :value="opt">{{ opt === '' ? 'auto' : opt }}</option>
        </select>
        <span class="hint-color">auto: brutal (with up) or bbr (without up)</span>
      </td>
    </tr>
    <tr>
      <th>
        {{ $t('com.NetworkHysteria.label_up') }}
        <hint v-html="$t('com.NetworkHysteria.hint_up')"></hint>
      </th>
      <td>
        <input type="text" class="input_15_table" v-model="transport.hysteriaSettings.up" autocomplete="off" autocorrect="off" autocapitalize="off" />
        <span class="hint-color">e.g., 100mbps</span>
      </td>
    </tr>
    <tr>
      <th>
        {{ $t('com.NetworkHysteria.label_down') }}
        <hint v-html="$t('com.NetworkHysteria.hint_down')"></hint>
      </th>
      <td>
        <input type="text" class="input_15_table" v-model="transport.hysteriaSettings.down" autocomplete="off" autocorrect="off" autocapitalize="off" />
        <span class="hint-color">e.g., 100mbps</span>
      </td>
    </tr>
    <tr>
      <th>
        {{ $t('com.NetworkHysteria.label_udphop_enabled') }}
        <hint v-html="$t('com.NetworkHysteria.hint_udphop_enabled')"></hint>
      </th>
      <td>
        <input type="checkbox" v-model="udphopEnabled" />
      </td>
    </tr>
    <tr v-if="udphopEnabled">
      <th>
        {{ $t('com.NetworkHysteria.label_udphop_port') }}
        <hint v-html="$t('com.NetworkHysteria.hint_udphop_port')"></hint>
      </th>
      <td>
        <input type="text" class="input_15_table" v-model="transport.hysteriaSettings.udphop!.port" autocomplete="off" autocorrect="off" autocapitalize="off" />
        <span class="hint-color">e.g., 20000-50000</span>
      </td>
    </tr>
    <tr v-if="udphopEnabled">
      <th>
        {{ $t('com.NetworkHysteria.label_udphop_interval') }}
        <hint v-html="$t('com.NetworkHysteria.hint_udphop_interval')"></hint>
      </th>
      <td>
        <input type="number" class="input_6_table" v-model.number="transport.hysteriaSettings.udphop!.interval" autocomplete="off" autocorrect="off" autocapitalize="off" />
        <span class="hint-color">default: 30 (seconds)</span>
      </td>
    </tr>
    <tr>
      <th>
        {{ $t('com.NetworkHysteria.label_udp_idle_timeout') }}
        <hint v-html="$t('com.NetworkHysteria.hint_udp_idle_timeout')"></hint>
      </th>
      <td>
        <input type="number" class="input_6_table" v-model.number="transport.hysteriaSettings.udpIdleTimeout" autocomplete="off" autocorrect="off" autocapitalize="off" />
        <span class="hint-color">default: 60 (seconds)</span>
      </td>
    </tr>
    <tr>
      <th>
        {{ $t('com.NetworkHysteria.label_salamander_enabled') }}
        <hint v-html="$t('com.NetworkHysteria.hint_salamander_enabled')"></hint>
      </th>
      <td>
        <input type="checkbox" v-model="salamanderEnabled" />
      </td>
    </tr>
    <tr v-if="salamanderEnabled">
      <th>
        {{ $t('com.NetworkHysteria.label_salamander_password') }}
        <hint v-html="$t('com.NetworkHysteria.hint_salamander_password')"></hint>
      </th>
      <td>
        <input type="text" class="input_25_table" v-model="salamanderPassword" autocomplete="off" autocorrect="off" autocapitalize="off" />
      </td>
    </tr>
    <tr>
      <th>
        {{ $t('com.NetworkHysteria.label_masquerade_enabled') }}
        <hint v-html="$t('com.NetworkHysteria.hint_masquerade_enabled')"></hint>
      </th>
      <td>
        <input type="checkbox" v-model="masqueradeEnabled" />
      </td>
    </tr>
    <template v-if="masqueradeEnabled && transport.hysteriaSettings.masquerade">
      <tr>
        <th>
          {{ $t('com.NetworkHysteria.label_masquerade_type') }}
          <hint v-html="$t('com.NetworkHysteria.hint_masquerade_type')"></hint>
        </th>
        <td>
          <select class="input_option" v-model="transport.hysteriaSettings.masquerade.type">
            <option v-for="(opt, index) in masqueradeTypes" :key="index" :value="opt">{{ opt === '' ? 'default (404)' : opt }}</option>
          </select>
        </td>
      </tr>
      <tr v-if="transport.hysteriaSettings.masquerade.type === 'file'">
        <th>
          {{ $t('com.NetworkHysteria.label_masquerade_dir') }}
          <hint v-html="$t('com.NetworkHysteria.hint_masquerade_dir')"></hint>
        </th>
        <td>
          <input type="text" class="input_25_table" v-model="transport.hysteriaSettings.masquerade.dir" autocomplete="off" autocorrect="off" autocapitalize="off" />
        </td>
      </tr>
      <tr v-if="transport.hysteriaSettings.masquerade.type === 'proxy'">
        <th>
          {{ $t('com.NetworkHysteria.label_masquerade_url') }}
          <hint v-html="$t('com.NetworkHysteria.hint_masquerade_url')"></hint>
        </th>
        <td>
          <input type="text" class="input_25_table" v-model="transport.hysteriaSettings.masquerade.url" autocomplete="off" autocorrect="off" autocapitalize="off" />
        </td>
      </tr>
      <tr v-if="transport.hysteriaSettings.masquerade.type === 'proxy'">
        <th>
          {{ $t('com.NetworkHysteria.label_masquerade_rewrite_host') }}
          <hint v-html="$t('com.NetworkHysteria.hint_masquerade_rewrite_host')"></hint>
        </th>
        <td>
          <input type="checkbox" v-model="transport.hysteriaSettings.masquerade.rewriteHost" />
        </td>
      </tr>
      <tr v-if="transport.hysteriaSettings.masquerade.type === 'proxy'">
        <th>
          {{ $t('com.NetworkHysteria.label_masquerade_insecure') }}
          <hint v-html="$t('com.NetworkHysteria.hint_masquerade_insecure')"></hint>
        </th>
        <td>
          <input type="checkbox" v-model="transport.hysteriaSettings.masquerade.insecure" />
        </td>
      </tr>
      <tr v-if="transport.hysteriaSettings.masquerade.type === 'string'">
        <th>
          {{ $t('com.NetworkHysteria.label_masquerade_content') }}
          <hint v-html="$t('com.NetworkHysteria.hint_masquerade_content')"></hint>
        </th>
        <td>
          <input type="text" class="input_25_table" v-model="transport.hysteriaSettings.masquerade.content" autocomplete="off" autocorrect="off" autocapitalize="off" />
        </td>
      </tr>
      <tr v-if="transport.hysteriaSettings.masquerade.type === 'string'">
        <th>
          {{ $t('com.NetworkHysteria.label_masquerade_status_code') }}
          <hint v-html="$t('com.NetworkHysteria.hint_masquerade_status_code')"></hint>
        </th>
        <td>
          <input type="number" class="input_6_table" v-model.number="transport.hysteriaSettings.masquerade.statusCode" autocomplete="off" autocorrect="off" autocapitalize="off" />
        </td>
      </tr>
    </template>
  </tbody>
</template>

<script lang="ts">
  import { defineComponent, ref, computed } from 'vue';
  import Hint from '@main/Hint.vue';
  import { XrayStreamSettingsObject } from '@/modules/CommonObjects';
  import { XrayStreamHysteriaSettingsObject, XrayHysteriaMasqueradeObject, XrayUdpHopObject, XrayFinalMaskObject, XraySalamanderObject } from '@/modules/TransportObjects';

  export default defineComponent({
    name: 'NetworkHysteria',
    components: {
      Hint
    },
    props: {
      transport: XrayStreamSettingsObject
    },
    setup(props) {
      const transport = ref<XrayStreamSettingsObject>(props.transport ?? new XrayStreamSettingsObject());
      const congestionOptions = XrayStreamHysteriaSettingsObject.congestionOptions;

      // Ensure congestion defaults to '' (auto) when undefined
      if (transport.value.hysteriaSettings && transport.value.hysteriaSettings.congestion === undefined) {
        transport.value.hysteriaSettings.congestion = '';
      }

      const udphopEnabled = computed({
        get: () => !!transport.value.hysteriaSettings?.udphop,
        set: (val) => {
          if (!transport.value.hysteriaSettings) return;
          if (val) {
            transport.value.hysteriaSettings.udphop = new XrayUdpHopObject();
          } else {
            transport.value.hysteriaSettings.udphop = undefined;
          }
        }
      });

      const salamanderEnabled = computed({
        get: () => !!(transport.value.udpmasks && transport.value.udpmasks.length > 0),
        set: (val) => {
          if (val) {
            const finalMask = new XrayFinalMaskObject();
            finalMask.settings = new XraySalamanderObject();
            transport.value.udpmasks = [finalMask];
          } else {
            transport.value.udpmasks = undefined;
          }
        }
      });

      const salamanderPassword = computed({
        get: () => transport.value.udpmasks?.[0]?.settings?.password ?? '',
        set: (val) => {
          if (transport.value.udpmasks && transport.value.udpmasks.length > 0) {
            if (!transport.value.udpmasks[0].settings) {
              transport.value.udpmasks[0].settings = new XraySalamanderObject();
            }
            transport.value.udpmasks[0].settings.password = val;
          }
        }
      });

      const masqueradeTypes = XrayHysteriaMasqueradeObject.typeOptions;

      const masqueradeEnabled = computed({
        get: () => !!transport.value.hysteriaSettings?.masquerade,
        set: (val) => {
          if (!transport.value.hysteriaSettings) return;
          if (val) {
            transport.value.hysteriaSettings.masquerade = new XrayHysteriaMasqueradeObject();
          } else {
            transport.value.hysteriaSettings.masquerade = undefined;
          }
        }
      });

      return {
        transport,
        congestionOptions,
        udphopEnabled,
        salamanderEnabled,
        salamanderPassword,
        masqueradeEnabled,
        masqueradeTypes
      };
    }
  });
</script>
