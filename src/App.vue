<template>
  <Button @click="filteredInfo = null">清空筛选</Button>
  <STable
    bordered
    :data-source="dataSource"
    :columns="columns"
    @change="handleChange"
  >
    <template
      #menuPopup="{
        column,
        filter: { setSelectedKeys, selectedKeysRef, confirm, clearFilters },
      }"
    >
      <div>
        <div style="padding: 16px 8px">
          <Input
            ref="searchInput"
            :value="selectedKeysRef.value[0]"
            :placeholder="`Search ${column.dataIndex}`"
            style="width: 188px; margin-bottom: 8px; display: block"
            @change="
              (e) => setSelectedKeys(e.target.value ? [e.target.value] : [])
            "
            @pressEnter="
              handleSearch(selectedKeysRef.value, confirm, column.dataIndex)
            "
          />
          <Button
            type="primary"
            size="small"
            style="width: 90px; margin-right: 8px"
            @click="
              handleSearch(selectedKeysRef.value, confirm, column.dataIndex)
            "
          >
            搜索
          </Button>
          <Button
            size="small"
            style="width: 90px"
            @click="handleReset(clearFilters)"
          >
            重置
          </Button>
        </div>
      </div>
    </template>
  </STable>
</template>
<script setup>
import STable from "@surely-vue/table";
import { defineComponent, ref, reactive, computed } from "vue";
import { Input, Button } from "ant-design-vue";

const filteredInfo = ref();

const menus = ref([
  {
    column: "No.",
    checked: true,
    disabled: true,
  },
  { column: "name", checked: true },
  { column: "age", checked: true },
  { column: "address", checked: true },
]);

const columns = computed(() => {
  const filtered = filteredInfo.value || {};
  return [
    {
      title: "",
      key: "No.",
      showMenu: "hover",
      width: 50,
      align: "center",
      customRender: ({ text, record, index }) => index + 1,
    },
    {
      title: "name",
      key: "name",
      dataIndex: "name",
      // filteredValue: filtered.name || null,
      width: "30%",
      autoHeight: true,
      editable: true,
      showMenu: true,
      sorter: (a, b) => a.name.length - b.name.length,
      onFilter: (value, record) =>
        record.name.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
      title: "age",
      dataIndex: "age",
      key: "age",
      showMenu: "hover",
      // filteredValue: filtered.age || null,
      onFilter: (value, record) =>
        record.age.toString().toLowerCase().includes(value.toLowerCase()),
    },
    {
      title: "address",
      dataIndex: "address",
      key: "address",
      showMenu: "hover",
      // filteredValue: filtered.address || null,
      onFilter: (value, record) =>
        record.address.toString().toLowerCase().includes(value.toLowerCase()),
    },
  ].filter(
    (item) =>
      menus.value.find(
        (menu) => menu.column === item.dataIndex || menu.column === item.key
      )?.checked
  );
});
const dataSource = ref([
  {
    key: "0",
    name: "Edward King 0",
    age: 32,
    address: "London, Park Lane no. 0",
  },
  {
    key: "1",
    name: "Edward King 1",
    age: 32,
    address: "London, Park Lane no. 1",
  },
  {
    key: "2",
    name: "Edward King 2",
    age: 32,
    address: "London, Park Lane no. 2",
  },
]);
const state = reactive({
  searchText: "",
  searchedColumn: "",
});
const searchInput = ref();
const handleSearch = (selectedKeys, confirm, dataIndex) => {
  confirm();
  state.searchText = selectedKeys[0];
  state.searchedColumn = dataIndex;
};

const handleReset = (clearFilters) => {
  clearFilters();
  state.searchText = "";
};

const handleChange = (pagination, filters) => {
  // 这里filters的值是{name:[]} 导致不能使用filteredInfo
  // filteredInfo.value = filters;
  console.log(filters)
};
</script>
<style lang="less" scope>
.filter-active {
  color: var(--surely-table-primary-color) !important;
  opacity: 1 !important;
}

.menu-popup-container {
  max-height: 200px;
  overflow-y: auto;
  padding: 4px 0;
}

.menu-popup {
  width: 100%;

  .menu-popup-item {
    width: 100%;
    padding: 4px 8px;

    &:hover {
      background-color: var(--surely-table-row-hover-bg);
    }

    &.disabled {
      color: var(--surely-table-disabled-color);
      cursor: not-allowed;
    }
  }
}
</style>
