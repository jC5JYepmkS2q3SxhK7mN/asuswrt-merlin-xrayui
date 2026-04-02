<template>
  <tr>
    <th>
      {{ $t('com.Backup.manager') }}
      <hint v-html="$t('com.Backup.hint')"></hint>
    </th>
    <td>
      <span class="row-buttons">
        <input class="button_gen button_gen_small" type="button" :value="$t('com.Backup.backup')" @click.prevent="show_backup_modal()" />
      </span>
      <modal ref="modal" :title="$t('com.Backup.manager')" width="500">
        <table class="FormTable modal-form-table">
          <tbody>
            <tr v-for="b in backups" :key="b">
              <td>
                {{ b }}
              </td>
              <td>
                <span class="row-buttons">
                  <input class="button_gen button_gen_small" type="button" :value="$t('com.Backup.download')" @click.prevent="download(b)" />
                  <input class="button_gen button_gen_small" type="button" :value="$t('com.Backup.restore')" @click.prevent="restore(b)" />
                  <a class="button_gen button_gen_small" href="#" @click.prevent="clear(b)" title="delete">&#10005;</a>
                </span>
              </td>
            </tr>
          </tbody>
        </table>
        <template v-slot:footer>
          <span class="backup-create-row">
            <input type="text" class="input_text" v-model="backupName" :placeholder="$t('com.Backup.name_placeholder')" maxlength="50" style="width: 200px; margin-right: 5px" />
            <input class="button_gen button_gen_small" type="button" :value="$t('com.Backup.backup')" @click.prevent="create_backup()" />
          </span>
          <input class="button_gen button_gen_small" type="button" :value="$t('com.Backup.clear')" @click.prevent="clear()" v-if="backups.length" />
        </template>
      </modal>
    </td>
  </tr>
</template>
<script lang="ts">
  import { defineComponent, ref, inject, Ref } from 'vue';
  import Modal from '@main/Modal.vue';
  import Hint from '@main/Hint.vue';
  import engine, { EngineResponseConfig, SubmitActions } from '@/modules/Engine';
  import { useI18n } from 'vue-i18n';

  export default defineComponent({
    name: 'Backup',
    components: {
      Modal,
      Hint
    },
    setup() {
      const { t } = useI18n();
      const modal = ref();
      const backups = ref<string[]>([]);
      const backupName = ref('');
      const uiResponse = inject<Ref<EngineResponseConfig>>('uiResponse')!;

      const restore = async (backup: string) => {
        if (!confirm(t('com.Backup.restore_confirm'))) return;

        await engine.executeWithLoadingProgress(async () => {
          await engine.submit(SubmitActions.restoreBackup, { file: backup }, 5000);
        });
      };
      const download = (backup: string) => {
        if (backup) {
          const url = `/ext/xrayui/backup/${backup}`;
          window.location.href = url;
        }
      };

      const create_backup = async () => {
        const name = backupName.value.trim();
        await engine.executeWithLoadingProgress(async () => {
          await engine.submit(SubmitActions.createBackup, name ? { name } : null, 2000);
        });
        backupName.value = '';
      };
      const clear = async (backup?: string) => {
        let backup_payload = null;
        if (backup) {
          backup_payload = { backup: backup };
          if (!confirm(t('com.Backup.clear_confirm', [backup]))) return;
        } else {
          if (!confirm(t('com.Backup.clear_all_confirm'))) return;
        }

        await engine.executeWithLoadingProgress(async () => {
          await engine.submit(SubmitActions.clearBackup, backup_payload, 2000);
        });
      };

      const show_backup_modal = () => {
        backups.value = uiResponse.value?.xray?.backups || [];
        modal.value.show();
      };

      return {
        backups,
        backupName,
        modal,
        create_backup,
        clear,
        restore,
        download,
        show_backup_modal
      };
    }
  });
</script>
