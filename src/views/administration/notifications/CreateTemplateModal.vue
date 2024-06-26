<template>
  <b-modal
    id="createTemplateModal"
    size="md"
    hide-header-close
    no-stacking
    :title="$t('admin.create_template')"
  >
    <b-form-group
      id="fieldset-1"
      :label="this.$t('message.name')"
      label-for="input-1"
      label-class="required"
    >
      <b-form-input
        id="input-1"
        v-model="name"
        class="required"
        required
        trim
      />
    </b-form-group>
    <b-form-group
      id="fieldset-2"
      :label="this.$t('admin.publisher_class')"
      label-for="input-2"
      label-class="required"
    >
      <b-form-input
        id="input-2"
        v-model="publisherClass"
        class="required"
        required
        trim
      />
    </b-form-group>
    <b-form-group
      id="fieldset-3"
      :label="this.$t('message.description')"
      label-for="input-3"
      label-class="required"
    >
      <b-form-textarea
        id="input-3"
        v-model="description"
        class="required"
        rows="4"
        required
        trim
      />
    </b-form-group>
    <b-form-group
      id="fieldset-4"
      :label="this.$t('admin.mime_type')"
      label-for="input-4"
      label-class="required"
    >
      <b-form-input
        id="input-4"
        v-model="mimeType"
        class="required"
        required
        trim
      />
    </b-form-group>
    <b-form-group
      id="fieldset-5"
      :label="this.$t('admin.template')"
      label-for="input-5"
      label-class="required"
    >
      <b-form-textarea
        id="input-5"
        v-model="template"
        class="required"
        rows="10"
        required
        trim
      />
    </b-form-group>
    <b-form-group>
      <c-switch
        id="input-7"
        color="primary"
        v-model="publishScheduled"
        label
        v-bind="labelIcon"
        class="optional"
      />
      {{ $t('admin.scheduled_notification') }}
    </b-form-group>
    <template v-slot:modal-footer="{ cancel }">
      <b-button size="md" variant="secondary" @click="cancel()">{{
        $t('message.close')
      }}</b-button>
      <b-button size="md" variant="primary" @click="createTemplate()">{{
        $t('message.create')
      }}</b-button>
    </template>
  </b-modal>
</template>

<script>
import permissionsMixin from '../../../mixins/permissionsMixin';
import EventBus from '../../../shared/eventbus';
import configPropertyMixin from '../mixins/configPropertyMixin';
import { Switch as cSwitch } from '@coreui/vue';

export default {
  mixins: [permissionsMixin, configPropertyMixin],
  components: {
    cSwitch,
  },
  mounted() {
    EventBus.$on('admin:templates:cloneTemplate', (template) => {
      this.name = `${template.name} - clone`;
      this.publisherClass = template.publisherClass;
      this.description = template.description;
      this.mimeType = template.templateMimeType;
      this.template = template.template;
      this.publishScheduled = template.publishScheduled;
    });
    this.$root.$on('bv::modal::hide', (_, modalId) => {
      if (modalId == 'createTemplateModal') {
        this.resetValues();
      }
    });
  },
  data() {
    return {
      name: null,
      publisherClass: null,
      description: null,
      mimeType: null,
      template: null,
      publishScheduled: null,
    };
  },
  methods: {
    createTemplate: function () {
      let url = `${this.$api.BASE_URL}/${this.$api.URL_NOTIFICATION_PUBLISHER}`;
      this.axios
        .put(url, {
          name: this.name,
          description: this.description,
          publisherClass: this.publisherClass,
          template: this.template,
          templateMimeType: this.mimeType,
          publishScheduled: this.publishScheduled,
          defaultPublisher: false,
        })
        .then(() => {
          this.$emit('refreshTable');
          this.$toastr.s(this.$t('admin.template_created'));
        })
        .catch(() => {
          this.$toastr.w(this.$t('condition.unsuccessful_action'));
        });
      this.$root.$emit('bv::hide::modal', 'createTemplateModal');
    },
    resetValues: function () {
      this.name = null;
      this.publisherClass = null;
      this.description = null;
      this.mimeType = null;
      this.template = null;
      this.publishScheduled = null;
    },
    expandView: function () {},
  },
};
</script>
