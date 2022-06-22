<template>
  <el-col>
    <div>Log</div>

    <el-table :data="reversedLogs" scrollbar-always-on="True" height="300px">
      <el-table-column prop="formula" label="Formula"></el-table-column>
      <el-table-column prop="answer" label="Answer"></el-table-column>
      <el-table-column align="right">
        <template #header>
          <el-button type="danger" plain @click="execDeleteAllLog()"
            >すべてのログを削除</el-button
          >
        </template>
        <template #default="scope">
          <el-button type="primary" @click="execSetInput(scope.row.answer)"
            >復元</el-button
          >
          <el-button type="danger" @click="execDeleteLog(scope.row.index)"
            >削除</el-button
          >
        </template>
      </el-table-column>
    </el-table>
    <div>(最大100件、古い方から削除)</div>
  </el-col>
</template>

<script lang="ts">
import { defineComponent, computed } from "vue";

export default defineComponent({
  props: ["logs", "logLength"],
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
    const execDeleteAllLog = () => {
      context.emit("deleteAllLog");
    };
    return {
      reversedLogs,
      execSetInput,
      execDeleteLog,
      execDeleteAllLog,
    };
  },
});
</script>

<style scoped></style>
