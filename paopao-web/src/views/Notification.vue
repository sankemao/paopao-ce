<template>
    <div>
        <main-nav title="提醒" />

        <n-list class="main-content-wrap messages-wrap" bordered>
            <div v-if="loading" class="skeleton-wrap">
                <message-skeleton :num="pageSize" />
            </div>

            <div v-else>
                <div class="empty-wrap" v-if="list.length === 0">
                    <n-empty size="large" description="暂无数据" />
                </div>

                <n-list-item v-for="m in list" :key="m.id">
                    <message-item :message="m" />
                </n-list-item>
            </div>

            <template #footer>
                <div class="pagination-wrap" v-if="totalPage > 0">
                    <n-pagination
                        :page="page"
                        @update:page="updatePage"
                        :page-slot="!store.state.collapsedRight ? 8 : 5"
                        :page-count="totalPage"
                    />
                </div>
            </template>
        </n-list>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useStore } from 'vuex';
import { useRoute } from 'vue-router';
import { getMessages } from '@/api/user';
const route = useRoute();
const store = useStore();
const loading = ref(false);
const page = ref(+route.query.p || 1);
const pageSize = ref(10);
const totalPage = ref(0);
const list = ref([]);

const loadMessages = () => {
    loading.value = true;
    getMessages({
        page: page.value,
        page_size: pageSize.value,
    })
        .then((res) => {
            loading.value = false;
            list.value = res.list;
            totalPage.value = Math.ceil(res.pager.total_rows / pageSize.value);
        })
        .catch((err) => {
            loading.value = false;
        });
};
const updatePage = (p) => {
    page.value = p;
    loadMessages();
};
onMounted(() => {
    loadMessages();
});
</script>

<style lang="less" scoped>
.pagination-wrap {
    padding: 10px;
    display: flex;
    justify-content: center;
    overflow: hidden;
}
</style>