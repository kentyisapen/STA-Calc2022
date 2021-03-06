<template>
  <el-main style="width: 600px">
    <el-row>
      <el-col :span="24">
        <el-input
          v-model="input"
          placeholder="(1 + 3) * 4 ..."
          style="height: 50px"
          @keyup.enter="calc()"
        >
          <template #append>=</template>
        </el-input>
      </el-col>
    </el-row>
    <el-container>
      <el-row style="width: 100%">
        <el-col :span="12">
          <el-button
            v-for="num in nums"
            :key="num"
            v-on:click="inputNum(num)"
            class="input_number_button"
            >{{ num }}</el-button
          >
        </el-col>
        <el-col :span="8">
          <el-button
            v-for="mark in marks"
            :key="mark"
            class="input_mark_button"
            v-on:click="inputMark(mark)"
            >{{ mark }}</el-button
          >
        </el-col>
        <el-col :span="4">
          <el-button
            v-for="spMark in spMarks"
            :key="spMark"
            type="primary"
            class="input_special_mark_button"
            v-on:click="inputSpecialMark(spMark)"
            >{{ spMark }}</el-button
          >
        </el-col>
      </el-row>
    </el-container>
    <el-row style="margin-top: 100px">
      <LogView
        :logs="logs"
        @setInput="setInput"
        @deleteLog="deleteLog"
        @deleteAllLog="deleteAllLog"
      ></LogView>
    </el-row>
  </el-main>
</template>

<script lang="ts">
interface Log {
  index: number;
  formula: string;
  answer: string;
}

import { ElNotification } from "element-plus";
import { defineComponent, ref } from "vue";
import { ElMessage, ElMessageBox } from "element-plus";
import LogView from "../components/Top/LogView.vue";

export default defineComponent({
  setup() {
    const input = ref("");
    const nums: number[] = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
    const marks: string[] = ["+", "-", "*", "/", "%", "^", "(", ")", "."];
    const spMarks: string[] = ["=", "AC", "CE"];
    const pattern2 = /^[\d*/\-+()%.^\s]+/;
    const logs = ref<Log[]>([]);
    const logIndex = ref(0);
    const logLength = ref(100);
    const inputNum = (num: number): void => {
      input.value += num;
    };
    const inputMark = (mark: string): void => {
      input.value += mark;
    };
    const inputSpecialMark = (spMark: string): void => {
      if (spMark === "=") {
        calc();
      } else if (spMark === "AC") {
        allClear();
      } else if (spMark === "CE") {
        input.value = input.value.slice(0, -1);
      }
    };
    const allClear = (): void => {
      input.value = "";
    };
    const openError = (): void => {
      ElNotification({
        title: "Error",
        message: "数式が不正です！",
        type: "error",
        showClose: false,
      });
    };
    const calc = (): void => {
      const res = input.value.match(pattern2);
      let temp = "";
      //一旦正規表現で数値と所定の記号以外を弾く
      if (!res) {
        openError();
        return;
      }
      //その後evalで実行し、エラーが起きたらnotification
      try {
        temp = String(eval(res[0].replace("^", "**")));
      } catch (error) {
        openError();
        return;
      }
      const log: Log = {
        index: logIndex.value++,
        formula: input.value,
        answer: temp,
      };
      logs.value.push(log);
      while (logs.value.length > logLength.value) {
        logs.value.shift();
      }
      localStorage.setItem("calc_logs", JSON.stringify(logs.value));
      localStorage.setItem("log_index", String(logIndex.value));
      input.value = temp;
    };
    const setInput = (numStr: string) => {
      input.value = numStr;
    };
    const deleteLog = (index: number) => {
      logs.value = logs.value.filter((log) => !(log.index == index));
      localStorage.setItem("calc_logs", JSON.stringify(logs.value));
    };
    const deleteAllLog = () => {
      ElMessageBox.confirm("すべてのログを削除しますか？", "Warning", {
        confirmButtonText: "OK",
        cancelButtonText: "Cancel",
        type: "warning",
      }).then(() => {
        const deleteTargetLogLength = logs.value.length;
        logs.value = [];
        localStorage.setItem("calc_logs", JSON.stringify(logs.value));
        ElMessage({
          type: "success",
          message: deleteTargetLogLength + "件のログを削除しました",
        });
      });
    };

    if (localStorage.log_index == null) {
      localStorage.setItem("log_index", "0");
    } else {
      logIndex.value = Number(localStorage.log_index);
    }
    if (localStorage.calc_logs == null) {
      localStorage.setItem("calc_logs", "[]");
    } else {
      logs.value = JSON.parse(localStorage.calc_logs);
    }
    return {
      input,
      nums,
      marks,
      spMarks,
      inputNum,
      inputMark,
      inputSpecialMark,
      allClear,
      calc,
      logs,
      setInput,
      deleteLog,
      deleteAllLog,
    };
  },
  components: { LogView },
});
</script>

<style lang="css" scoped>
.el-main {
  overflow: hidden;
}
.input_number_button {
  width: calc(33% - 4px);
  height: 40px;
  margin: 2px;
}
.input_mark_button {
  width: calc(50% - 4px);
  height: 40px;
  margin: 2px;
}

.input_special_mark_button {
  width: calc(100% - 4px);
  height: 40px;
  margin: 2px;
}
</style>
