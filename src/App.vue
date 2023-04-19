<template>
  <table>
    <thead>
      <tr>
        <th>KEY</th>
        <th>CELL1</th>
        <th>CELL2</th>
        <th>CELL3</th>
        <th>CELL4</th>
        <th>CELL5</th>
        <th>CELL6</th>
        <th>CELL7</th>
        <th>CELL8</th>
        <th>CELL9</th>
      </tr>
    </thead>
    <tbody>
      <tr
        v-for="row in tableData"
        :key="row.key"
        @click="selectRow(row)"
        :class="{ selected: row === selectedRow }"
      >
        <td>
          <!-- @click.stop 停止冒泡事件 不然整個tr都會亮 -->
          <span
            class="star"
            @click.stop="selectStar(row)"
            :class="{ selected: row.selected }"
          ></span
          >{{ row.key }}
        </td>
        <td>{{ row.cell1 }}</td>
        <td>{{ row.cell2 }}</td>
        <td>{{ row.cell3 }}</td>
        <td>{{ row.cell4 }}</td>
        <td>{{ row.cell5 }}</td>
        <td>{{ row.cell6 }}</td>
        <td>{{ row.cell7 }}</td>
        <td>{{ row.cell8 }}</td>
        <td>{{ row.cell9 }}</td>
      </tr>
    </tbody>
  </table>
</template>

<script>
export default {
  data() {
    return {
      tableData: [], // 表格內容
      selectedRow: null, // 選定點擊的那一列
    };
  },
  beforeMount() {},
  async mounted() {
    // 由於只做單一頁面，故沒有額外封裝fetch。
    /* 將data1、data2、data3取出來後讓他附值在tableData中 */
    console.time();
    try {
      const dataFiles = ["data1.json", "data2.json", "data3.json"];
      const responses = await Promise.all(
        dataFiles.map((file) => fetch(`/data/${file}`))
      );
      const dataArray = await Promise.all(
        responses.map((response) => response.json())
      );

      this.tableData = dataArray[0];

      // 建立一個包含 tableData 每一個元素的 key 的陣列
      const tableDataKeys = this.tableData.map((row) => row.key);

      for (const row2 of dataArray[1]) {
        // 搜尋 key 值是否存在於 tableDataKeys 陣列中
        const index = tableDataKeys.indexOf(row2.key);
        if (index !== -1) {
          // 若存在，直接使用 tableData[index] 更新資料
          this.tableData[index].cell8 = row2.cell8;
        }
      }

      const tableDataCell4 = this.tableData.map((row) => row.cell4);
      for (const row3 of Object.values(dataArray[2])) {
        const index = tableDataCell4.indexOf(row3.cell4);
        if (index !== -1) {
          this.tableData[index].cell9 = row3.cell9;
        }
      }
    } catch (error) {
      console.error("取得檔案失敗:", error);
    }

    console.timeEnd();
  },
  methods: {
    /* selectRow為選定點擊的那一頁 */
    selectRow(row) {
      /* 依據selectedRow決定是否新增selected的className */
      this.selectedRow = row === this.selectedRow ? null : row;
    },
    /* selectStar為選定點擊的星星 */
    selectStar(row) {
      /* 新增key做為是否需要加上selected的依據 */
      row.selected = !row.selected;
    },
  },
};
</script>

<style scoped>
/* 已經在App.vue內引入了 main.css所以不用寫。 */
</style>
