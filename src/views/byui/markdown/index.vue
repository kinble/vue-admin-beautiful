<template>
  <div class="markdown-container markdown-body">
    <el-select @change="handleChange" v-model="value" placeholder="请选择">
      <el-option
        v-for="(item, index) in options"
        :key="index"
        :label="item.label"
        :value="item.value"
      >
      </el-option>
    </el-select>
    <div v-html="prettierList"></div>
  </div>
</template>

<script>
import { getPrettierList } from "@/api/markdown";
import marked from "marked";
import "github-markdown-css/github-markdown.css";

export default {
  name: "News",
  data() {
    return {
      listLoading: true,
      elementLoadingText: "正在加载...",
      options: [
        {
          value: "0",
          label: "Prettier",
        },
      ],
      value: "0",
      prettierList: null,
    };
  },
  created() {
    this.fetchData();
  },
  mounted() {},
  methods: {
    handleChange(val) {
      this.value = val;
      this.fetchData();
    },
    fetchData() {
      this.listLoading = true;
      switch (this.value) {
        case "0":
          getPrettierList(this.pagination)
            .then((res) => {
              this.prettierList = marked(res.data || "", {
                renderer: new marked.Renderer(),
                gfm: true,
                tables: true,
                breaks: false,
                pedantic: false,
                sanitize: false,
                smartLists: true,
                smartypants: false,
              });
            })
            .then(() => {
              $(".markdown-container a").each(function () {
                if (typeof $(this).attr("href") !== "undefined") {
                  if ($(this).attr("href").indexOf("http") === -1) {
                    $(this).removeAttr("href");
                  }
                }
              });
            });
          break;
      }
      setTimeout(() => {
        this.listLoading = false;
      }, 500);
    },
  },
};
</script>

<style lang="scss" scoped>
.markdown-container {
}
</style>
