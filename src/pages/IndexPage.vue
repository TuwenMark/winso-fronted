<template>
  <a-input-search
    v-model:value="searchParams.searchText"
    placeholder="input search text"
    enter-button="Search"
    size="large"
    @search="onSearch"
  />
  <MyDivider />
  <a-tabs v-model:activeKey="activeKey" centered @change="onTabsChange">
    <a-tab-pane key="post" tab="文章">
      <PostList :post-list="postList" />
    </a-tab-pane>
    <a-tab-pane key="picture" tab="图片">
      <PictureList :picture-list="pictureList" />
    </a-tab-pane>
    <a-tab-pane key="user" tab="用户">
      <UserList :user-list="userList" />
    </a-tab-pane>
  </a-tabs>
</template>

<script setup lang="ts">
import { ref, watchEffect } from "vue";
import PostList from "@/components/PostList.vue";
import PictureList from "@/components/PictureList.vue";
import UserList from "@/components/UserList.vue";
import MyDivider from "@/components/MyDivider.vue";
import { useRoute, useRouter } from "vue-router";
import myAxios from "@/plugins/myAxios";

const postList = ref([]);
const pictureList = ref([]);
const userList = ref([]);

const initSearchParams = {
  searchText: "",
  pageSize: 10,
  pageNum: 1,
};
const searchParams = ref(initSearchParams);
const router = useRouter();
const route = useRoute();
const activeKey = route.params.category;

const loadDataOld = (value: string) => {
  myAxios
    .post("/post/list/page/vo", { ...searchParams.value })
    .then((res: any) => {
      postList.value = res.records;
    });
  myAxios
    .post("/picture/list/page/vo", { ...searchParams.value })
    .then((res: any) => {
      pictureList.value = res.records;
    });
  myAxios
    .post("/user/list/page/vo", {
      ...searchParams.value,
      userName: value,
    })
    .then((res: any) => {
      userList.value = res.records;
    });
};

const loadData = (params: any) => {
  myAxios
    // .post("/search/list/page/all", {
    //   ...params,
    // })
    .post("/search/list/page/all/sync", {
      ...params,
    })
    .then((res: any) => {
      postList.value = res.postList;
      pictureList.value = res.pictureList;
      userList.value = res.userList;
    });
};

loadData(searchParams.value);

const onSearch = (value: string) => {
  loadData(searchParams.value);
  router.push({
    query: searchParams.value,
  });
  console.log(searchParams);
};
const onTabsChange = (key: string) => {
  router.push({
    path: `/${key}`,
    query: searchParams.value,
  });
};

// watchEffect(() => {
//   searchParams.value = {
//     ...initSearchParams,
//     searchText: route.query.searchText,
//   } as any;
// });
</script>
