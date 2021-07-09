<template>
    <a-card style = "width:100%" title="已接诊病人信息">
        <a-table :columns="columns" :data-source="data" >

            <template v-for="col in ['user_id','name','address']" :slot="col" slot-scope="text">
              <div :key="col">
                {{text}}
              </div>
            </template>

            <template slot="operation" slot-scope="text,record">
                <div key="operation">
                    <a-button type="primary" @click="showModal(text)">
                    详情
                    </a-button>
                    <a-modal
                      title="详细信息"
                      width = 60%
                      cloasble= clo
                      :visible="list.get(text)"
                      :footer="null"
                      :closable="clo"
                    >
                    <template>
                      <a-card title ="患者信息" style="text-align=left">
                        <p style="fontSize: 16px;color: rgba(0, 0, 0, 0.85); marginBottom: 16px;fontWeight: 500">
                          患者ID: {{record.user_id}}
                        </p>
                        <a-card title="姓名">
                          <h style="fontSize: 16px;">
                          {{record.name}}
                          </h>
                        </a-card>
                        <a-card title="地址" :style="{ marginTop: '16px' }">
                          <h style="fontSize: 16px;">
                          {{record.address}}
                          </h>
                        </a-card>
                        <a-card title="开具处方记录" :style="{ marginTop: '16px' }">
                          <h style="fontSize: 16px;">
                          {{record.pres_list}}
                          </h>
                        </a-card>
                      </a-card>
                    </template>
                    <br/>
                    <br/>  
                      <div style="text-align:right">
                      <a-button type="primary" @click="handleCancel(text)">
                        返回
                      </a-button>
                      </div>
                    </a-modal>
                </div>
            </template>
        </a-table>
    </a-card>
</template>

<script>

const columns = [
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
    title: '地址',
    dataIndex: 'address',
    width: '30%',
    align: 'center',
    scopedSlots: { customRender: 'address' },
  },
  /*{
    title: '处方号',
    dataIndex: 'pres_list',
    width: '50%',
    align: 'center',
    scopedSlots: { customRender: 'pres_list' },
  },*/
  /*{
    title:'头像',
    dataIndex:'photo',
    render:(record) => (<img src={require(record)}/>),
    scopedSlots: { customRender: 'photo' },
  },*/
  {
    title: '详情',
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
    user_id: 100000+i,
    address: '杭州',
    name : '张三',
    pres_list: '10111',
    operation: 100000+i,
    photo: '/src/assets/img/logo.png',
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
      clo:false,

      ModalText: 'Content of the modal',
      list,
    };
  },
  methods: {
    showModal(text) {
      this.list.add(text,true);
    },

    handleCancel(text) {
      console.log('Clicked cancel button');
      this.list.add(text,false);
    },
  },
};

</script>

<style scoped>
.editable-row-operations a {
  margin-right: 8px;
}
.left{
    width: 60%;
    float: left;
    text-align: center;
}
.right{
   width:40%;
   float: right;
    text-align: center;
}
</style>