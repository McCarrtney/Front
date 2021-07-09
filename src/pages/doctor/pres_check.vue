<!--
只有传了text，再传record才能起效！
-->
<template>
    <a-card style = "width:100%" title="已创建处方信息">
        <a-table :columns="columns" :data-source="data" >

            <template v-for="col in ['pres_id', 'user_id','name']" :slot="col" slot-scope="text">
              <div :key="col">
                {{text}}
              </div>
            </template>

            <template v-for="col in ['medicine','description']" :slot="col" slot-scope="text">
              <div :key="col">
                {{ text }}
              </div>
            </template>

            <template slot="operation" slot-scope="text,record">
                <div key="operation">
                    <a-button type="primary" @click="showModal_edit(text,record.key)">
                    编辑
                    </a-button>
                    <a-modal
                      title="处方信息"
                      okText = "提交修改"
                      width = 60%
                      :visible="list.get(text)"
                      :confirm-loading="confirmLoading"
                      @ok="handleOk_save(text,record.key)" 
                      @cancel="handleCancel_cancel(text,record.key)"
                    >
                      <a-card title ="处方单">
                        <p style="fontSize: 16px;color: rgba(0, 0, 0, 0.85); marginBottom: 16px;fontWeight: 500">
                          处方ID: {{record.pres_id}}
                        </p>

                        <a-card title="患者ID">
                          <h style="fontSize: 16px;">
                          {{record.user_id}}
                          </h>
                        </a-card>
                        <a-card title="患者姓名">
                          <h style="fontSize: 16px;">
                          {{record.name}}
                          </h>
                        </a-card>
                        <a-card title="药品单" :style="{ marginTop: '16px' }">
                          <a-input
                          v-if="record.editable"
                          style="margin: 0px 0"
                          :value="record.medicine"
                          @change="e => handleChange(e.target.value, record.key, 'medicine')"
                          />
                        </a-card>
                        <a-card title="病情" :style="{ marginTop: '16px' }">
                          <a-input
                          v-if="record.editable"
                          style="margin: 0px 0"
                          :value="record.description"
                          @change="e => handleChange(e.target.value, record.key, 'description')"
                          />
                        </a-card>
                        <a-card title="备注" :style="{ marginTop: '16px' }">
                          <a-input
                          v-if="record.editable"
                          style="margin: 0px 0"
                          :value="record.notes"
                          @change="e => handleChange(e.target.value, record.key, 'notes')"
                          />
                        </a-card>
                      </a-card>
                    </a-modal>
                </div>
            </template>

            <p slot="expandedRowRender" slot-scope="record" style="margin: 0">
                备注：
                {{ record.notes }}
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
    align: 'center',
    scopedSlots: { customRender: 'pres_id' },
  },
  {
    title: '患者编号',
    dataIndex: 'user_id',
    width: '15%',
    align: 'center',
    scopedSlots: { customRender: 'user_id' },
  },
  {
    title: '患者姓名',
    dataIndex: 'name',
    width: '15%',
    align: 'center',
    scopedSlots: { customRender: 'name' },
  },
  {
    title: '药品单',
    dataIndex: 'medicine',
    width: '30%',
    align: 'center',
    scopedSlots: { customRender: 'medicine' },
  },
  {
    title: '病情',
    dataIndex: 'description',
    width: '50%',
    align: 'center',
    scopedSlots: { customRender: 'description' },
  },
  {
    title: '编辑',
    dataIndex: 'operation',
    align: 'center',
    scopedSlots: { customRender: 'operation' },
  },
];

const data = [];
var list = new Object();
list.add = function(key,value){this[key]=value};
list.get = function(key){return this[key]};

for (let i = 0; i < 100; i++) {
  data.push({
    key: i.toString(),
    pres_id: 100000+i,
    user_id: 100000+i,
    operation: 100000+i,
    name : '张三',
    description: "头晕脑涨",
    medicine: "六味地黄丸,蒙脱石散",
    notes:"情况有反复，注意提醒过段时间复诊",
    // name: `Edrward ${i}`,
    // age: 32,
    // address: `London Park no. ${i}`
  });
  list.add(100000+i,false);
}


export default {
  data() {
    this.cacheData = data.map(item => ({ ...item }));
    return {
      data,
      columns,
      editingKey: '',

      ModalText: 'Content of the modal',
      list,
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
    showModal_edit(text,key) {
      this.list.add(text,true);
      const newData = [...this.data];
      const target = newData.filter(item => key === item.key)[0];
      this.editingKey = key;
      if (target) {
        target.editable = true;
        this.data = newData;
      }
    },
    handleOk_save(text,key) {
      this.list.add(text,false);

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

    handleCancel_cancel(text,key) {
      console.log('Clicked cancel button');
      this.list.add(text,false);

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
