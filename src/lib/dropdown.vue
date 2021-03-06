
<style lang="stylus" scoped>

@import '~@salesforce-ux/design-system/assets/styles/salesforce-lightning-design-system.min.css'
</style>

<template lang="pug">
.slds-dropdown-trigger.slds-dropdown-trigger_click(:class="{ 'slds-is-open': is_open }")
  vlds-button-icon(type="border-filled" icon="utility/down" :title="title" @click="clickButton")
  .slds-dropdown.slds-dropdown_left(:class="[dropdown_length]")
    ul.slds-dropdown__list(role="menu")
      template(v-for="i_ in items")
        li.slds-has-divider_top-space(v-if="i_.label == null" role="separator")
        li.slds-dropdown__header.slds-truncate(v-else-if="i_.value == null" :title="i_.label" role="separator")
          span.slds-text-title_caps {{ i_.label }}
        li.slds-dropdown__item(v-else role="presentation")
          a(role="menuitem" tabindex="0" @click="clickItem({ value_: i_.value })")
            span.slds-truncate(:title="i_.label")
              svg.slds-icon.slds-icon_x-small.slds-icon-text-default.slds-m-right_x-small(
                v-if="i_.left_icon != null"
                aria-hidden="true"
                v-html="getIcon({ name_: i_.left_icon })"
              )
              | {{ i_.label }}
            svg.slds-icon.slds-icon_x-small.slds-icon-text-default.slds-m-left_small.slds-shrink-none(
              v-if="i_.right_icon != null"
              aria-hidden="true"
              v-html="getIcon({ name_: i_.right_icon })"
            )
</template>

<script lang="ts">
import { Vue, Component, Prop } from 'vue-property-decorator'

@Component({})
export default class extends Vue {
  is_open = false
  down_icon = require('@salesforce-ux/design-system/assets/icons/utility/down.svg')

  @Prop({ type: String })
  title: string | undefined

  @Prop({ type: Array })
  items: ({ left_icon?: string; right_icon?: string; label: string | null; value?: any } | null)[]

  @Prop({ type: String })
  scroll: string | undefined

  mounted() {
    onclick = () => {
      if (this.is_open) this.is_open = false
    }
  }

  clickButton() {
    setTimeout(() => {
      this.is_open = !this.is_open
    })
  }

  clickItem({ value_ }) {
    ;(this.$listeners.select as Function)(value_)
    this.is_open = false
  }

  getIcon({ name_ }: { name_: string }) {
    return require(`@salesforce-ux/design-system/assets/icons/${name_}.svg`)
  }

  get dropdown_length() {
    let length: number | undefined
    if (this.scroll === 'large') length = 10
    else if (this.scroll === 'medium') length = 7
    else if (this.scroll === 'small') length = 5

    if (length == null) return null

    const has_icon = this.items.some(_ => {
      if (_ == null) return false
      return _.left_icon != null || _.right_icon != null
    })

    return `slds-dropdown_length-${has_icon ? 'with-icon-' : ''}${length}`
  }
}
</script>
