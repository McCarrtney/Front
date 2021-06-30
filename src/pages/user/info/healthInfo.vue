<template>
  <a-table :columns="columns" :data-source="data" bordered>
    <template
      v-for="col in ['date', 'height', 'weight', 'bp', 'lung']"
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
</template>
<script>
const columns = [
  {
    title: '日期',
    dataIndex: 'date',
    width: '18%',
    scopedSlots: { customRender: 'date' },
  },
  {
    title: '身高',
    dataIndex: 'height',
    width: '18%',
    scopedSlots: { customRender: 'height' },
    sorter: (a, b) => a.height - b.height,
    sortDirections: ['descend', 'ascend'],
  },
  {
    title: '体重',
    dataIndex: 'weight',
    width: '18%',
    scopedSlots: { customRender: 'weight' },
    sorter: (a, b) => a.weight - b.weight,
    sortDirections: ['descend', 'ascend'],
  },
  {
    title: '血压',
    dataIndex: 'bp',
    width: '18%',
    scopedSlots: { customRender: 'bp' },
  },
  {
    title: '肺活量',
    dataIndex: 'lung',
    width: '18%',
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

const data = [];
for (let i = 0; i < 100; i++) {
  data.push({
    key: i.toString(),
    height: 170+i,
    weight: 70+i,
    bp: 100+i,
    lung: 4000+i,
    // name: `Edrward ${i}`,
    // age: 32,
    // address: `London Park no. ${i}`,
  });
}
export default {
  data() {
    this.cacheData = data.map(item => ({ ...item }));
    return {
      data,
      columns,
      editingKey: '',
    };
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
