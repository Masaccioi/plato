<template>
  <div class="create">
    <c-modal :show="show_modal"
      @submit="create"
      @cancel="show_modal = false">{{ __('create.confirm') }}</c-modal>
    <c-spinner v-show="fetching"></c-spinner>
    <c-form class="c-form-expand"
      @submit.native.prevent="show_modal = true">
      <div>
        <c-row class="padding" :flex="false">
          <c-label>{{__(title.label)}}</c-label>
          <c-badge class="warning" size="small">
            {{$validation.errors.filter(function (error) { return error.field === 'title' }).map(function (error) { return error.message }).join(' ')}}
          </c-badge>
          <c-textfield
            field="title"
            :row="title.row"
            :validate="title.validate"
            :value="title.value"
            @change="title.value = $event"></c-textfield>
        </c-row>
        <c-row class="padding" :flex="false">
          <c-label>{{__(content.label)}}</c-label>
          <c-badge class="warning" size="small">
            {{$validation.errors.filter(function (error) { return error.field === 'content' }).map(function (error) { return error.message }).join(' ')}}
          </c-badge>
          <c-multiline
            field="content"
            rows="10"
            :row="content.row"
            :validate="content.validate"
            :value="content.value"
            @change="content.value = $event"></c-multiline>
        </c-row>
      </div>
      <c-pane>
        <c-button class="primary" type="submit"
          :disabled="$validation.errors.length > 0">{{ __('create.submit') }}</c-button>
      </c-pane>
    </c-form>
  </div>
</template>

<script>
import CModal from 'platojs/components/core/modal'
import CBadge from 'platojs/components/core/badge'
import CPane from 'platojs/components/core/pane'
import CForm from 'platojs/components/core/form'
import CRow from 'platojs/components/core/row'
import CLabel from 'platojs/components/core/label'
import CTextfield from 'platojs/components/core/textfield'
import CMultiline from 'platojs/components/core/multiline'
import CSpinner from 'platojs/components/core/spinner'
import CButton from 'platojs/components/core/button'

export default {
  data () {
    return {
      show_modal: false,
      title: {
        row: 'title',
        label: 'create.title',
        value: '',
        validate: {
          required: {
            message: this.__('/validator.required', this.__('create.title'))
          },
          maxlength: {
            rule: 50,
            message: this.__('/validator.maxlength', 50)
          }
        }
      },
      content: {
        row: 'content',
        label: 'create.content',
        value: '',
        validate: {
          required: {
            message: this.__('/validator.required', this.__('create.content'))
          },
          maxlength: {
            rule: 500,
            message: this.__('/validator.maxlength', 500)
          }
        }
      }
    }
  },

  mapState: ['fetching'],
  mapActions: ['post'],

  methods: {
    create () {
      this.show_modal = false
      this.$validate().then(() => this.post({
        title: this.title.value,
        content: this.content.value
      }))
    }
  },

  watch: {
    fetching (val) {
      if (!val) {
        this.$router.push('/')
      }
    }
  },

  validator: {
    auto: true
  },

  components: {
    CModal,
    CBadge,
    CForm,
    CRow,
    CLabel,
    CTextfield,
    CMultiline,
    CSpinner,
    CPane,
    CButton
  }
}
</script>

<style src="../styles/create" scoped></style>
