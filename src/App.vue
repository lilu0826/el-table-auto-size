<template>
    <el-table ref="_elTableRef" :data="tableData" style="width: 80vw" border>
        <el-table-column prop="date" label="Date" />
        <el-table-column prop="name" label="Name" />
        <el-table-column prop="address" label="Address（自动调整列宽）" />
    </el-table>
    <el-button type="primary" style="margin-top: 20px" @click="changeData"
        >更新数据</el-button
    >
</template>

<script setup>
import { ref, onMounted, nextTick, watch } from "vue";
const _elTableRef = ref();
const tableData = ref([
    {
        date: "2016-05-03",
        name: "Tom",
        address: "No. 189, Grove St, Los Angeles",
    },
    {
        date: "2016-05-02",
        name: "Tom",
        address: "No. 189, Grove St, Los Angeles",
    },
    {
        date: "2016-05-04",
        name: "Tom",
        address: "No. 189, Grove St, Los Angeles",
    },
    {
        date: "2016-05-01",
        name: "Tom",
        address: "No. 189, Grove St, Los Angeles",
    },
]);
function doAutoSize(column) {
    if (!column) {
        return;
    }
    //拿到该列单元格
    const tds = _elTableRef.value.$el.querySelectorAll(`.${column.id}>.cell`);
    //拿到单元格内容宽度
    const tdWidths = [...tds].map((element) => {
        element.classList.add("testElementSize");
        const width =
            element.getBoundingClientRect().width || element.scrollWidth;
        element.classList.remove("testElementSize");
        //这里+2是因为element的表格可能有左右边框两个像素
        console.log("width", width);
        return Math.ceil(width) + 2;
    });
    //最大的单元格宽度
    let maxWidth = Math.max(...tdWidths);
    //设置该列宽度并更新布局
    column.realWidth = maxWidth;
    column.width = maxWidth;
    requestAnimationFrame(() => {
        _elTableRef.value.doLayout();
    });
}
onMounted(async () => {
    resizeCol();
});

async function resizeCol() {
    // 等待渲染要2次,原因未知，1次不行
    await nextTick();
    await nextTick();
    const column = _elTableRef.value.store.states.columns.value.find(
        (el) => el.property == "address"
    );
    console.log("column", column);
    doAutoSize(column);
}

watch(
    tableData,
    () => {
        resizeCol();
    },
    { deep: true }
);

function changeData() {
    tableData.value[0].address = "No. 189, Grove St, Los Angeles".repeat(
        Math.random() * 10 + 1
    );
}
</script>

<style>
.testElementSize {
    min-width: fit-content !important;
    width: fit-content !important;
    height: auto !important;
    overflow: auto !important;
    white-space: nowrap !important;
    text-overflow: unset !important;
}
</style>
