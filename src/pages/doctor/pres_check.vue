<template>
    <a-card style = "width:100%" title="已创建处方信息">
        <a-table :columns="columns" :data-source="data" >

            <template v-for="col in ['pres_id', 'user_id','name']" :slot="col" slot-scope="text">
                <div :key="col">
                {{ text }}
                </div>
            </template>

            <template
            v-for="col in ['medicine','description']"
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
                        <a-popconfirm title="确认取消?" @confirm="() => cancel(record.key)">
                            <a>Cancel</a>
                        </a-popconfirm>
                    </span>
                    <span v-else>
                        <a :disabled="editingKey !== ''" @click="() => edit(record.key)">Edit</a>
                    </span>
                </div>
            </template>
            <p slot="expandedRowRender" slot-scope="record" style="margin: 0">
                备注：
                <a-input
                    v-if="record.editable"
                    style="margin: 0px 0"
                    :value="record.notes"
                    @change="e => handleChange_notes(e.target.value, record.key)"
                    />
                    <template v-else>
                        {{ record.notes }}
                    </template>
            </p>
        </a-table>
    </a-card>
</template>

<script>

const columns = [
  {
    title: '处方编号',
    dataIndex: 'pres_id',
    width: '15%',
    scopedSlots: { customRender: 'pres_id' },
  },
  {
    title: '患者编号',
    dataIndex: 'user_id',
    width: '15%',
    scopedSlots: { customRender: 'user_id' },
  },
  {
    title: '患者姓名',
    dataIndex: 'name',
    width: '10%',
    scopedSlots: { customRender: 'name' },
  },
  {
    title: '药品单',
    dataIndex: 'medicine',
    width: '30%',
    scopedSlots: { customRender: 'medicine' },
  },
  {
    title: '病情',
    dataIndex: 'description',
    width: '50%',
    scopedSlots: { customRender: 'description' },
  },
  {
    title: '编辑',
    dataIndex: 'operation',
    scopedSlots: { customRender: 'operation' },
  },
];

const data = [];
for (let i = 0; i < 100; i++) {
  data.push({
    key: i.toString(),
    pres_id: 100000+i,
    user_id: 100000+i,
    name : '张三',
    description: "头晕脑涨",
    medicine: "六味地黄丸,蒙脱石散",
    notes:"情况有反复，注意提醒过段时间复诊",
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
    handleChange_notes(value, key) {
      const newData = [...this.data];//存储这个格子里的新信息
      const target = newData.filter(item => key === item.key)[0];//查找对应的item
      if (target) {//将新值存入对应item并更新内容
        target.notes = value;
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
