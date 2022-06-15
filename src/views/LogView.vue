<template>
  <div>Log</div>
  <el-table :data="reversedLogs">
    <el-table-column prop="formula" label="Formula"></el-table-column>
    <el-table-column prop="answer" label="Answer"></el-table-column>
    <el-table-column width="80px">
      <template v-slot="scope">
        <el-button
          type="primary"
          @click="execSetInput(scope.row.answer)"
          style="width: 100%"
          >復元</el-button
        >
      </template>
    </el-table-column>
    <el-table-column width="80px">
      <template v-slot="scope">
        <el-button
          type="danger"
          @click="execDeleteLog(scope.row.index)"
          style="width: 100%"
          >削除</el-button
        >
      </template>
    </el-table-column>
  </el-table>
</template>

<script lang="ts">
import { defineComponent, computed } from "vue";

export default defineComponent({
  props: ["logs"],
  setup(props, context) {
    const reversedLogs = computed(() => {
      return [...props.logs].reverse();
    });
    const execSetInput = (numStr: string) => {
      context.emit("setInput", numStr);
    };
    const execDeleteLog = (index: number) => {
      context.emit("deleteLog", index);
    };
    return {
      reversedLogs,
      execSetInput,
      execDeleteLog,
    };
  },
});
</script>

<style scoped></style>
