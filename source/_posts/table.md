---
title: table前端组件封装
top_img: '/CDN/wallpaper/cover_19.jpg'
cover: '/CDN/wallpaper/cover_19.jpg'
tags:
  - Css
categories:
  - 前端
abbrlink: 739
---

## el-table组件封装

首先创建一个vue文件,目前封装的table可支持前端自动分页、工具插槽、过滤、排序等功能

### html部分
```html
<template>
  <div style="border: 1px solid #e0e0e0">
    <div class="table-tool" v-if="bShowTool">
      <slot name="tool"></slot>
    </div>
    <el-table ref="refTable" v-bind="attrs" v-on="listeners">
      <el-table-column type="expand" v-if="bShowExpand" />
      <el-table-column
        type="selection"
        v-if="bShowSelection"
        width="35px"
        :reserve-selection="true"
        :selectable="selectable"
      />
      <el-table-column
        type="index"
        :index="getIndex"
        v-if="bShowIndex"
        :showOverflowTooltip="true"
      >
        <template slot="header">#</template>
      </el-table-column>
      <el-table-column
        v-for="(column, index) in columns"
        :key="`${index}-${column.prop}`"
        :type="column.type"
        :width="column.width"
        :min-width="column.minWidth"
        :align="column.align"
        :label="TR(column.headerName)"
        :column-key="column.prop"
        :prop="column.prop"
        :fixed="column.fixed"
        :filters="column.filters"
        :filter-multiple="column.filterMultiple"
        :filtered-value="column.filteredValue"
        :showOverflowTooltip="column.showOverflowTooltip"
        :render-header="column.renderHeader"
        :sortable="column.sortable"
      >
        <template slot-scope="scope">
          <span v-if="column.render">
            <ExpandSlot
              :render="column.render"
              :row="scope.row"
              :index="scope.$index"
            ></ExpandSlot>
          </span>
          <span v-else-if="column.formatter">
            {{
              column.formatter(
                scope.row,
                scope.column,
                scope.row[column.prop],
                scope.$index
              )
            }}
          </span>
          <span v-else>
            {{ scope.row[column.prop] }}
          </span>
        </template>
      </el-table-column>
    </el-table>
    <el-row v-if="bShowPage">
      <slot name="footer"></slot>
      <el-pagination
        class="table-pagination"
        :small="bPageSmall"
        :layout="layout"
        :total="curTotal"
        :page-sizes="pageSizes"
        :page-size.sync="curPageSize"
        :current-page.sync="curPage"
      >
      </el-pagination>
    </el-row>
  </div>
</template>
```
### js部分
```js
<script>
import utils from '@/utils'; //通用函数放入utils文件中

export default {
  inheritAttrs: false,
  name: 'DTable',
  props: {
    bShowTool: {
      type: Boolean,
      default: true
    },
    bShowExpand: {
      type: Boolean,
      default: false
    },
    bShowSelection: {
      type: Boolean,
      default: true
    },
    selectable: {
      type: Function,
      default: () => {
        return true;
      }
    },
    bShowIndex: {
      type: Boolean,
      default: false
    },
    bShowPage: {
      type: Boolean,
      default: true
    },
    // 搜索时，支持哪些列可以搜索
    searchkeys: Array,
    // 搜索词
    searchValue: String,
    // 前端分页时的所有数据
    allData: {
      type: Array,
      default: () => []
    },
    data: Array,
    columns: Array,
    maxHeight: [Number, String],
    bMaxHeight: {
      type: Boolean,
      default: true
    },
    rowkey: {
      type: [Function, String],
      default: row => {
        return row.id;
      }
    },
    treeProps: {
      type: Object,
      default() {
        return {
          children: 'children',
          hasChildren: 'has_children'
        };
      }
    },
    fixed: {
      type: Boolean,
      default: true
    },
    pageStoreKey: {
      type: String,
      default: 'PAGE_SIZE'
    },
    total: {
      type: Number,
      default: 0
    },
    pageSize: Number,
    currentPage: {
      type: Number,
      default: 1
    },
    bPageSmall: Boolean,
    layout: {
      type: String,
      default: 'total, sizes, prev, pager, next, jumper'
    },
    pageSizes: {
      type: Array,
      default: () => [5, 10, 50, 100]
    },
    filterKeys: Object
  },

  data() {
    return {
      curPageSize: this.initPageSize(),
      curPage: this.currentPage,
      curData: [],
      curTotal: 0,
      filters: this.filterKeys || '',
      orderType: '',
      orderProp: '',
      headerCellStyle: {
        height: '38px',
        'font-weight': 600,
        color: '#333333',
        padding: '0',
        'background-color': '#E8EFF8'
      },
      cellStyle: {
        height: '38px',
        padding: '0'
      }
    };
  },

  mounted() {
    window.addEventListener('resize', this.getTableHeight);
    this.getTableHeight();
  },

  computed: {
    attrs() {
      let attrs = {
        ...this.$attrs,
        data: this.curData,
        'row-key': this.rowkey,
        stripe: true,
        fixed: this.fixed,
        'tooltip-effect': 'light',
        'header-cell-style': this.headerCellStyle,
        'cell-style': this.cellStyle,
        'tree-props': this.treeProps
      };
      if (this.bMaxHeight) {
        attrs = {
          ...attrs,
          'max-height': this.maxHeight || this._maxHeight
        };
      }
      return attrs;
    },

    listeners() {
      let listeners = {
        ...this.$listeners,
        'sort-change': this.handleSortChange,
        'filter-change': this.handleFilterChange
      };
      return listeners;
    },

    // 前端自己分页
    bLocalPage() {
      return this.allData.length > 0;
    }
  },

  watch: {
    data: {
      immediate: true,
      handler() {
        this.curData = this.data;
        this.curTotal = this.total;
      }
    },
    allData() {
      this.getCurData();
    },

    curPage(val) {
      this.getCurData();
      this.$emit('update:currentPage', val);
    },
    currentPage(val) {
      this.curPage = val;
    },
    curPageSize(val) {
      this.getCurData();
      utils.setLocalStore(this.pageStoreKey, val);
      this.$emit('update:pageSize', val);
      this.$emit('size-change', val);
    }
  },

  methods: {
    initPageSize() {
      let size = 10;
      if (this.pageSize) {
        size = this.pageSize;
      } else {
        size = utils.getLocalStore(this.pageStoreKey, 10);
      }
      utils.setLocalStore(this.pageStoreKey, size);
      return size;
    },

    getIndex(index) {
      return (this.curPage - 1) * this.curPageSize + index + 1;
    },

    getTableHeight() {
      this._maxHeight = this.maxHeight || window.innerHeight - 250;
    },

    handleSortChange({ prop, order }) {
      let orderKey = '';
      if (order) {
        if (order === 'ascending') {
          orderKey = prop;
        } else {
          orderKey = `-${prop}`;
        }
      }
      this.orderProp = prop;
      this.orderType = order;
      this.getCurData();
      this.$emit('on-sort', orderKey, prop, order);
    },

    handleFilterChange(filters) {
      let exist = false;
      let filterKey = Object.keys(filters)[0];
      if (!this.filters) {
        this.filters = filters;
      } else {
        for (let key in this.filters) {
          if (key === filterKey) {
            exist = true;
            this.filters[key] = filters[key];
          }
        }
        if (!exist) {
          this.filters[filterKey] = filters[filterKey];
        }
      }
      this.getCurData();
      this.$emit('on-filter', this.filters);
    },

    toggleRowSelection(row, selected) {
      if (!this.bLocalPage) {
        // 选中\取消状态，row必须为当前的data的数据，
        // row又是引用类型的数据，element ui源码是用statusArr.indexOf(row)，所以当界面数据数据后，会找不到数据。
        for (const rowData of this.$refs.refTable.selection) {
          if (rowData.id === row.id) {
            row = rowData;
            break;
          }
        }
      }
      this.$refs.refTable.toggleRowSelection(row, selected);
    },

    doClearSelectedData() {
      this.$refs.refTable.clearSelection();
    },

    getStoreData() {
      return this.$refs.refTable.store;
    },

    getCurData() {
      if (!this.bLocalPage) return;
      let data = utils.deepClone(this.allData);
      if (this.filters) {
        Object.keys(this.filters).forEach(colName => {
          data = data.filter(item => {
            return (
              !this.filters[colName].length ||
              this.filters[colName].includes(item[colName])
            );
          });
        });
      }

      if (this.searchValue) {
        const value = this.searchValue.toLowerCase();
        data = data.filter(item => {
          let bFind = false;
          this.searchkeys.forEach(key => {
            bFind =
              bFind || String(item[key]).toLowerCase().indexOf(value) !== -1;
          });
          return bFind;
        });
      }

      if (this.orderType) {
        data.sort(utils.createSortFunc(this.orderProp, this.orderType));
      }

      this.curTotal = data.length;
      if (this.bShowPage) {
        // 支持分页
        this.curData = data.slice(
          (this.curPage - 1) * this.curPageSize,
          this.curPage * this.curPageSize
        );
      } else {
        this.curData = data;
      }
    }
  },

  destroyed() {
    window.removeEventListener('resize', this.getTableHeight);
  }
};
</script>
```
### 样式部分
```css
<style lang="scss">
.table-tool {
  display: flex;
  align-items: center;
  justify-content: space-between;
  min-height: 40px;
  line-height: 22px;
  padding: 0 16px;
}
.el-table {
  font-size: 13px;
}
.table-pagination {
  float: right;
  padding: 10px;
  min-height: 48px;
}
</style>
```

### 通用函数封装
```js
/**
 * 根据数组的某项进行排序
 * key: 第几项或者某个字段名
 * order: ascending或descending
 */
Common.createSortFunc = (key, order = 'ascending') => {
  return function (a, b) {
    let av = a[key];
    let bv = b[key];

    if (!isNaN(Number(av)) && !isNaN(Number(bv))) {
      av = Number(av);
      bv = Number(bv);
    }

    if (order === 'ascending') {
      if (av === null) {
        return -1;
      } else if (bv === null) {
        return 1;
      }
      return av > bv ? 1 : -1;
    } else if (order === 'descending') {
      if (av === null) {
        return 1;
      } else if (bv === null) {
        return -1;
      }
      return av < bv ? 1 : -1;
    } else {
      return 0;
    }
  };
};

/**
 * 浏览器本地数据读写接口
 */
Broswer.getLocalStore = function (key, defval = null) {
  if (localStorage.hasOwnProperty(key)) {
    try {
      return JSON.parse(localStorage.getItem(key));
    } catch (e) {
      console.log(e);
      return localStorage.getItem(key);
    }
  }
  return defval;
};

Broswer.setLocalStore = function (key, val) {
  localStorage.setItem(key, JSON.stringify(val));
};
```

## vxe-table 组件
- [https://vxetable.cn/#/table/start/install](https://vxetable.cn/#/table/start/install)
