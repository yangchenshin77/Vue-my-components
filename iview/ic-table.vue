<style lang="less">
  .ivu-table-action {
    button {
      margin-right: 8px;
      &:last-child {
        margin-right: 0;
      }
    }
    .ivu-btn.disabled,
    .ivu-btn[disabled] {
      cursor: default !important
    }
    .ivu-poptip-popper {
      text-align: initial;
    }
  }
  .my-table-page-outer {
    padding: 1em 0;
    text-align: center;
  }
</style>

<template>
  <div class="ic-table">
    <Table
      :columns="v_col"
      :data="v_data"
      :loading="loading"
      :no-data-text="emptyText"
    >
      <slot name="header" slot="header" />
      <slot name="footer" slot="footer" />
    </Table>

    <div v-if="paginateShow" class="my-table-page-outer">
      <Page :current="pageCurrent"
        :page-size="pageSize"
        :total="total"
        @on-change="changePage" />
    </div>
  </div>
</template>

<script>
export default {
  name: 'ICTable',
  props: {
    // Table data
    col: Array,
    data: Array,

    loading: {
      type: Boolean,
      default: false
    },

    // Paginate
    paginateShow: {
      type: Boolean,
      default: true
    },
    pageCurrent: {
      type: Number,
      default: 1
    },
    pageSize: {
      type: Number,
      default: 10
    },
    total: {
      type: Number,
      default: 10
    },

    // Permission
    action: {
      type: Boolean,
      default: true
    },
    permission: Object,

    // Button
    btnSize: {
      type: String,
      default: 'default'
    },
    btnTextType: {
      type: String,
      default: 'icon'
    },

    // Text
    actionText: {
      type: String,
      default: '編輯'
    },
    editText: {
      type: String,
      default: '編輯'
    },
    deleteText: {
      type: String,
      default: '刪除'
    },
    emptyText: {
      type: String,
      default: '暫無資料'
    }
  },
  data () {
    return {
      v_col: this.col,
      v_data: this.data,
      v_perm: {},
      v_perm_default: {
        edit: true,
        delete: true
      }
    }
  },
  computed: {
    perm_num () {
      return Object.keys(this.v_perm).filter(prem => {
        return this.v_perm[prem]
      }).length
    },
    perm_length () {
      return Object.keys(this.v_perm).length
    },
    isIcon () {
      return this.btnTextType === 'icon'
    }
  },
  watch: {
    col (val) { this.v_col = val },
    data (val) { this.v_data = val }
  },
  methods: {
    handleColumns () {
      if (this.action && this.perm_length !== 0) {
        const btnWidth = this.isIcon ? 75 : 100
        this.v_col = this.v_col.concat({
          title: this.actionText,
          key: 'action',
          width: this.perm_num * btnWidth,
          align: 'center',
          render: (h, params) => {
            const btnEdit = this.can('edit')
              ? this.hEditBtn(h, params) : null
            const btnDelete = this.can('delete')
              ? this.hDeleteBtn(h, params) : null
            return h('div', {
              class: ['ivu-table-action']
            }, [btnEdit, btnDelete])
          }
        })
      }
    },
    can (action) {
      return this.v_perm[action]
    },
    isBtnTextType (type) {
      return this.btnTextType === type
    },
    hEditBtn (h, params) {
      return h('Button', {
        props: {
          type: this.isIcon ? 'default' : 'success',
          ghost: !this.isIcon,
          shape: this.isIcon ? 'circle' : '',
          icon: this.isIcon ? 'md-create' : '',
          size: this.btnSize,
          disabled: params.row.disabled
        },
        on: {
          click: () => this.$emit('on-edit', params)
        }
      }, [this.isIcon ? '' : this.editText])
    },
    hDeleteBtn (h, params) {
      return h('Poptip', {
        props: {
          confirm: true,
          title: '您確定要刪除嗎?'
        },
        on: {
          'on-ok': () => this.$emit('on-delete', params)
        }
      }, [
        h('Button', {
          props: {
            type: this.isIcon ? 'default' : 'error',
            ghost: !this.isIcon,
            shape: this.isIcon ? 'circle' : '',
            icon: this.isIcon ? 'md-trash' : '',
            size: this.btnSize,
            disabled: params.row.disabled
          }
        }, [this.isIcon ? '' : this.deleteText])
      ])
    },
    hIcon (h, type) {
      return h('Icon', {
        props: {
          type
        }
      })
    },
    changePage (page) {
      this.$emit('on-change-page', page)
    }
  },
  mounted () {
    if (this.action) {
      this.v_perm = Object.assign(this.v_perm_default, this.permission || {})
      this.handleColumns()
    }
  }
}
</script>
