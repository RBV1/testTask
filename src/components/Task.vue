<template lang="html">
  <div class="uk-card uk-card-default uk-animation-slide-bottom-small">
      <div class="uk-card-header">
          <button @click="$emit('deleteTask', task)" class="delete-card uk-button uk-button-text" uk-icon="close"></button>
          <div class="uk-flex-middle" uk-grid>
              <div class="">
                  <h3 class="uk-card-title uk-margin-remove-bottom">{{ task.title }}</h3>
                  <p class="uk-text-meta uk-margin-remove-top"><time>{{ task.date | formatDate }}</time></p>
              </div>
          </div>
          <div class="uk-flex-middle">
            <div :class="classObject">{{ task.priority }}</div>
            <div class="uk-label category" >{{ task.category }}</div>
          </div>
      </div>
      <div class="uk-card-body">
          <p>{{ task.description }}</p>
      </div>
  </div>
</template>

<script>
import moment from 'moment'

export default {

  props: ['task'],
  filters: {
    formatDate: function (value) {
      return moment(value).format('MMMM D, YYYY')
    }
  },

  computed: {
    classObject () {
      let priority = parseInt(this.priority)
      return {
        'uk-label-success': priority === 2,
        'uk-label-warning': priority === 3,
        'uk-label-danger': priority === 4,
        'uk-label': true
      }
    }
  }
}
</script>

<style lang="less">
  .category {
    color: #333;
    font-size: 12px;
    font-weight: 500;
    background-color: #f8f8f8;
  }

  .delete-card {
    position: absolute;
    right: 10px;
    top: 5px;

    &:hover,
    &:focus {
      opacity: .5;
    }

    &::before {
      display: none;
    }
  }

</style>
