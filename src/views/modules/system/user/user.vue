<template>
  <a-card :borderd="false">
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="48">
          <a-col :md="6" :sm="24">
            <a-form-item label="用户名">
              <a-input placeholder="请输入用户名" v-model="queryForm.username" allowClear />
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="24">
            <a-form-item label="状态">
              <a-select placeholder="请选择状态" v-model="queryForm.status" allowClear>
                <a-select-option value="0">启用</a-select-option>
                <a-select-option value="1">禁用</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="24">
            <span class="table-page-search-submitButtons">
              <a-button v-if="$auth('sys:user:view')" type="primary" @click="query" v-action:query>查询</a-button>
              <a-button
                v-if="$auth('sys:user:add')"
                type="primary"
                style="margin-left: 8px"
                @click="addUser"
                v-action:add
                >添加</a-button
              >
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <a-table
      :columns="columns"
      rowKey="id"
      :data-source="data"
      :pagination="pagination"
      @change="tableChange"
      :loading="loading"
    >
      <div slot="expandedRowRender" slot-scope="record" style="margin: 0">
        <a-descriptions title="用户信息" :column="{ xxl: 3, xl: 3, lg: 3, md: 3, sm: 2, xs: 1 }">
          <a-descriptions-item label="用户名">
            {{ record.username }}
          </a-descriptions-item>
          <a-descriptions-item label="电话">
            {{ record.phone }}
          </a-descriptions-item>
          <a-descriptions-item label="邮箱">
            {{ record.email }}
          </a-descriptions-item>
          <a-descriptions-item label="性别">
            <a-tag :color="record.sex == 1 ? 'purple' : 'cyan'">
              {{ record.sex == 1 ? '男' : '女' }}
            </a-tag>
          </a-descriptions-item>
          <a-descriptions-item label="创建时间">
            {{ record.createTime }}
          </a-descriptions-item>
          <a-descriptions-item label="登陆IP">
            {{ record.loginIp }}
          </a-descriptions-item>
        </a-descriptions>
      </div>
      <span slot="sex" slot-scope="text, record">
        <a-tag :color="record.sex == 1 ? 'purple' : 'cyan'">
          {{ record.sex == 1 ? '男' : '女' }}
        </a-tag>
      </span>
      <span slot="status" slot-scope="text, record">
        <a-tag :color="record.status == 0 ? 'green' : 'red'">
          {{ record.status == 0 ? '启用' : '禁用' }}
        </a-tag>
      </span>
      <span slot="action" slot-scope="text, record">
        <a-button v-if="$auth('sys:user:update')" type="primary" size="small" @click="onEdit(record)" v-action:edit
          >编辑</a-button
        >
        <a-divider type="vertical" />
        <a-button v-if="$auth('sys:user:del')" type="primary" size="small" @click="onDelete(record)" v-action:delete
          >删除</a-button
        >
      </span>
    </a-table>
  </a-card>
</template>
<script>
import { user } from '@/api/system'
export default {
  name: 'User',
  data() {
    return {
      queryForm: {},
      columns: [
        { title: '用户名', dataIndex: 'username', ellipsis: true },
        { title: '电话', dataIndex: 'phone', ellipsis: true },
        { title: '邮箱', dataIndex: 'email', ellipsis: true },
        {
          title: '性别',
          dataIndex: 'sex',
          ellipsis: true,
          scopedSlots: { customRender: 'sex' },
        },
        {
          title: '状态',
          dataIndex: 'status',
          ellipsis: true,
          scopedSlots: { customRender: 'status' },
        },
        { title: '创建时间', dataIndex: 'createTime', ellipsis: true },
        {
          title: '操作',
          width: '160px',
          dataIndex: 'action',
          align: 'right',
          scopedSlots: { customRender: 'action' },
        },
      ],
      data: [],
      pagination: {
        total: 0,
        defaultCurrent: 1,
        defaultPageSize: 20,
        showTotal: (total, range) => `第${range[0]}-${range[1]}条/总共 ${total} 条数据`,
        showSizeChanger: true,
        pageSizeOptions: ['20', '40', '60', '100'],
      },
      loading: false,
    }
  },
  methods: {
    query() {
      this.pagination.defaultCurrent = 1
      this.getUserList()
    },
    addUser() {},
    tableChange(e) {},
    onEdit(record) {},
    onDelete(record) {},
    getUserList() {
      this.loading = true
      const params = {
        current: this.pagination.defaultCurrent,
        pageSize: this.pagination.defaultPageSize,
      }
      const obj = {
        username: this.queryForm.username,
        status: this.queryForm.status,
      }
      user(params, obj).then((res) => {
        this.data = res.result.records
        this.pagination.total = res.result.total
        this.loading = false
      })
    },
  },
  created: function () {
    this.getUserList()
  },
}
</script>
