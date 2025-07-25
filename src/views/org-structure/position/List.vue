<script setup lang="ts" name="Position">
import { Search, Plus, ArrowDown } from '@element-plus/icons-vue'
import type { Position } from './type'
import Detail from './Detail.vue'
import Form from './Form.vue'
interface PositionSearch extends Position {
  keywords?: string
}

const { queryParam, onSearch, getList, loading, dataList, pagination, remove, resetFilter } = useList<
  Position,
  PositionSearch
>({
  baseApi: '/iam/position'
})

getList()

const formRef = ref()
const openForm = (id?: string) => {
  formRef.value?.open(id)
}
const detailRef = ref()
const openDetail = (id: string) => {
  detailRef.value?.open(id)
}

const updatePermission = checkPermission('update')
const deletePermission = checkPermission('delete')
</script>

<template>
  <div class="list-page">
    <el-space wrap class="list-operation">
      <el-button v-has-permission="'create'" :icon="Plus" type="primary" @click="openForm()">
        {{ $t('operation.create') }}
      </el-button>
      <el-space>
        <el-input v-model="queryParam.name" clearable :placeholder="$t('position.name')" @change="onSearch" />
        <el-input v-model="queryParam.code" clearable :placeholder="$t('position.code')" @change="onSearch" />
        <el-button :icon="Search" type="primary" @click="onSearch">{{ $t('operation.search') }}</el-button>
        <el-button :title="$t('title.reset')" @click="resetFilter">{{ $t('operation.reset') }}</el-button>
      </el-space>
    </el-space>

    <el-table ref="tableRef" v-loading="loading" class="list-body" :data="dataList" stripe height="100%">
      <el-table-column prop="name" :label="$t('position.name')" />
      <el-table-column prop="code" :label="$t('position.code')" />
      <el-table-column :label="$t('position.user')">
        <template #default="{ row }">
          {{ [...new Set(row.userNames ?? [])].join('、') }}
        </template>
      </el-table-column>
      <el-table-column prop="gradeName" :label="$t('position.gradeName')" />
      <el-table-column :label="$t('position.dataPermissionType')">
        <template #default="{ row }">
          <el-tag :color="row.dataPermissionTypeLabel?.ext?.color" effect="dark" type="info">
            {{ row.dataPermissionTypeLabel?.label }}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="updateTime" :label="$t('baseField.updateTime')" width="165" />
      <el-table-column :label="$t('operation.label')" width="180" fixed="right">
        <template #default="{ row }">
          <el-space>
            <el-button v-has-permission="'detail'" text bg type="primary" size="small" @click="openDetail(row.id)">
              {{ $t('operation.detail') }}
            </el-button>
            <el-dropdown v-has-permission="['update', 'delete']">
              <el-button text bg type="primary" size="small">
                {{ $t('operation.more') }}
                <el-icon :size="16" style="margin-left: 5px">
                  <arrow-down />
                </el-icon>
              </el-button>
              <template #dropdown>
                <el-dropdown-menu>
                  <el-dropdown-item v-if="updatePermission" @click="openForm(row.id)">
                    {{ $t('operation.update') }}
                  </el-dropdown-item>
                  <el-dropdown-item v-if="deletePermission" @click="remove(row.id, row.name)">
                    {{ $t('operation.delete') }}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </template>
            </el-dropdown>
          </el-space>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      v-if="pagination.total"
      v-model:current-page="pagination.current"
      v-model:page-size="pagination.pageSize"
      size="small"
      background
      layout="total, sizes, prev, pager, next, jumper"
      :total="pagination.total"
      @size-change="getList()"
      @current-change="getList()"
    />

    <Detail ref="detailRef" />
    <Form ref="formRef" @complete="getList()" />
  </div>
</template>

<style lang="scss" scoped></style>
