<template>
  <div id="systemCombo">
    <!-- <Select
      :value="currentValue"
      clearable
      filterable
      @on-change="setCurrentValue">
      <Option value="" label="全部" v-if="needAll"></Option>
      <Option :value="s.id" :label="s.name" v-for="s in systemList" :key="s.id">
        <template v-if="showDesc">
          <span>{{s.name}}</span>
          <span style="float:right;color:#ccc">{{s.description}}</span>
        </template>
      </Option>
    </Select> -->
    <select class="my-combo" :value="currentValue" @change="setCurrentValue($event.target.value)">
      <option value="" v-if="needAll">全部</option>
      <option :value="s.id" v-for="s in systemList" key="s.id">{{s.name}}</option>
    </select>
  </div>
</template>
<script>
import sysApis from '../apis';

export default {
  name: 'systemCombo',
  props: {
    value: {
      type: [String],
      default: ''
    },
    needAll: {
      type: Boolean,
      default: false
    },
    showDesc: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      currentValue: this.value,
      systemList: []
    };
  },
  mounted () {
    this.$nextTick(() => {
      this.getSystemList();
    });
  },
  methods: {
    getSystemList () {
      this.systemList = [];
      this.$http.post(sysApis.sys.system.find, {
        pageSize: 50
      }).then(response => {
        if (response.body.success) {
          this.systemList = response.body.obj.list;
          if (!this.needAll && this.systemList.length > 0) {
            this.setCurrentValue(response.body.obj.list[0].id);
          }
        } else {
          this.$Modal.error({
            title: '提示',
            content: response.body.msg
          });
        }
      }, response => {
        this.$Modal.error({
          title: '提示',
          content: '网络不通，无法加载系统列表！'
        });
      });
    },
    setCurrentValue (val) {
      this.currentValue = val;
      this.$emit('input', val);
    }
  },
  watch: {
    value (val) {
      this.setCurrentValue(val);
    }
  }
};
</script>
<style>
</style>
