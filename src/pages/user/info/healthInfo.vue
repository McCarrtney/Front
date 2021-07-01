<template>
  <a-card :loading="loading" style="margin-top: 24px" :bordered="false" :body-style="{padding: '24px'}">
    <a-tabs default-active-key="1">
      <a-tab-pane key="1" tab="表格显示">
        <a-table :columns="columns" :data-source="data" bordered>
          <template
            v-for="col in ['date', 'height', 'weight', 'bp_high', 'bp_low', 'lung']"
            :slot="col"
            slot-scope="text, record"
          >
            <div :key="col">
              <a-input
                v-if="record.editable"
                style="margin: -5px 0"
                :value="text"
                @change="e => handleChange(e.target.value, record.key, col)"
              />
              <template v-else>
                {{ text }}
              </template>
            </div>
          </template>
          <template slot="operation" slot-scope="text, record">
            <div class="editable-row-operations">
              <span v-if="record.editable">
                <a @click="() => save(record.key)">Save</a>
                <a-popconfirm title="Sure to cancel?" @confirm="() => cancel(record.key)">
                  <a>Cancel</a>
                </a-popconfirm>
              </span>
              <span v-else>
                <a :disabled="editingKey !== ''" @click="() => edit(record.key)">Edit</a>
              </span>
            </div>
          </template>
        </a-table>
      </a-tab-pane>
      <a-tab-pane key="2" tab="图表显示">
        <template>
          <div>
            <v-chart :forceFit="true" :height="height" :data="dateCmputed" :scale="scale">
              <v-tooltip />
              <v-axis />
              <v-line position="date*height" />
              <v-point position="date*height" shape="circle" />
            </v-chart>
          </div>
        </template>
      </a-tab-pane>
    </a-tabs>
  </a-card>
</template>
<script>
import {format} from 'date-fns'
const columns = [
  {
    title: '日期',
    dataIndex: 'date',
    width: '15%',
    scopedSlots: { customRender: 'date' },
  },
  {
    title: '身高',
    dataIndex: 'height',
    width: '15%',
    scopedSlots: { customRender: 'height' },
    sorter: (a, b) => a.height - b.height,
    sortDirections: ['descend', 'ascend'],
  },
  {
    title: '体重',
    dataIndex: 'weight',
    width: '15%',
    scopedSlots: { customRender: 'weight' },
    sorter: (a, b) => a.weight - b.weight,
    sortDirections: ['descend', 'ascend'],
  },
  {
    title: '血压（高压）',
    dataIndex: 'bp_high',
    width: '15%',
    scopedSlots: { customRender: 'bp_high' },
    sorter: (a, b) => a.bp_high - b.bp_high,
    sortDirections: ['descend', 'ascend'],
  },
  {
    title: '血压（低压）',
    dataIndex: 'bp_low',
    width: '15%',
    scopedSlots: { customRender: 'bp_low' },
    sorter: (a, b) => a.bp_low - b.bp_low,
    sortDirections: ['descend', 'ascend'],
  },
  {
    title: '肺活量',
    dataIndex: 'lung',
    width: '15%',
    scopedSlots: { customRender: 'lung' },
    sorter: (a, b) => a.lung - b.lung,
    sortDirections: ['descend', 'ascend'],
  },
  {
    title: '操作',
    dataIndex: 'operation',
    scopedSlots: { customRender: 'operation' },
  },
];
const beginDay = new Date().getTime()
const begin = format(new Date(beginDay - 1000 * 60 * 60 * 24 * 15), 'yyyy-MM-dd');
const end = format(new Date(beginDay + 1000 * 60 * 60 * 24 * 15), 'yyyy-MM-dd');
const scale = [{
  dataKey: 'date',
  min: begin,
  max: end,
  },{
  dataKey: 'height',
  min: 150,
  max: 250,
}];

const data = [];
for (let i = 0; i < 100; i++) {
  data.push({
    key: i.toString(),
    height: 170+i,
    weight: 70+i,
    bp_high: 120+i,
    bp_low: 70+i,
    lung: 4000+i,
    date: format(new Date(beginDay + 1000 * 60 * 60 * 24 * i), 'yyyy-MM-dd')
    // name: `Edrward ${i}`,
    // age: 32,
    // address: `London Park no. ${i}`
  });
}

export default {
  data() {
    this.cacheData = data.map(item => ({ ...item }));
    return {
      data,
      columns,
      editingKey: '',
      scale,
      height: 400,
    };
  },
  computed: {
    dateCmputed:function(){
      const newData = [...this.data];
      return newData.filter(function(item){
        return item.date>=begin && item.date<=end
      })
    }
  },
  methods: {
    handleChange(value, key, column) {
      const newData = [...this.data];
      const target = newData.filter(item => key === item.key)[0];
      if (target) {
        target[column] = value;
        this.data = newData;
      }
    },
    edit(key) {
      const newData = [...this.data];
      const target = newData.filter(item => key === item.key)[0];
      this.editingKey = key;
      if (target) {
        target.editable = true;
        this.data = newData;
      }
    },
    save(key) {
      const newData = [...this.data];
      const newCacheData = [...this.cacheData];
      const target = newData.filter(item => key === item.key)[0];
      const targetCache = newCacheData.filter(item => key === item.key)[0];
      if (target && targetCache) {
        delete target.editable;
        this.data = newData;
        Object.assign(targetCache, target);
        this.cacheData = newCacheData;
      }
      this.editingKey = '';
    },
    cancel(key) {
      const newData = [...this.data];
      const target = newData.filter(item => key === item.key)[0];
      this.editingKey = '';
      if (target) {
        Object.assign(target, this.cacheData.filter(item => key === item.key)[0]);
        delete target.editable;
        this.data = newData;
      }
    },
  },
};
</script>
<style scoped>
.editable-row-operations a {
  margin-right: 8px;
}
</style>
