<template>
  <a-input-search
    v-model:value="value"
    placeholder="input search text"
    enter-button
    suffix="Min. 3 characters"
    @search="onSearch"
  />
  <a-table :columns="columns" :data-source="data" :scroll="{ x: 2000 }">
    <template #bodyCell="{ column }: any">
      <template v-if="column.key === 'action'">
        <a>Delete</a>
      </template>
    </template>
    <template #expandedRowRender="{ record }: any">
      <p style="margin: 0">
        {{ record.description }}
      </p>
    </template>
  </a-table>
</template>
<script lang="ts">
import { defineComponent, ref } from "vue";
import { message } from "ant-design-vue";
import axios from "axios";

type Contacts = {
[x: string]: any;
  date: string;
  description: string;
  id: number;
  key: number;
  liable: {
    name: string
  };
  name: string;
  pipeline: {
    name: string
  };
  price: number;
};

const columns = [
  { title: "Название", dataIndex: "name", key: "name", fixed: true },
  { title: "Статус", dataIndex: "pipeline", key: "pipeline" },
  { title: "Ответственный", dataIndex: "liable", key: "liable" },
  { title: "Дата создания", dataIndex: "date", key: "date" },
  { title: "Бюджет", dataIndex: "price", key: "price" },
];

export default defineComponent({
  setup() {
    let data = ref([{}]);
    const token =
      "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImp0aSI6ImE1ZmNjYzJkZGEyNmFmNTZlN2UxZTE0YWE1YjI4ODc0NGY0MjE2MGZhYWFjNTk4OGFmZTVhNmRkODVmNzBlOGJjMzUxMzI1MTRkNzJlMDRhIn0.eyJhdWQiOiJiMWI5ZTE4OC03MDU2LTQ2YjQtYWJiYi00N2I5MzM1YWNhZjQiLCJqdGkiOiJhNWZjY2MyZGRhMjZhZjU2ZTdlMWUxNGFhNWIyODg3NDRmNDIxNjBmYWFhYzU5ODhhZmU1YTZkZDg1ZjcwZThiYzM1MTMyNTE0ZDcyZTA0YSIsImlhdCI6MTY4MjE1Mzk4NSwibmJmIjoxNjgyMTUzOTg1LCJleHAiOjE2ODIyNDAzODUsInN1YiI6Ijk1Mjg1NTQiLCJhY2NvdW50X2lkIjozMTAxNzgwMiwiYmFzZV9kb21haW4iOiJhbW9jcm0ucnUiLCJ2ZXJzaW9uIjoidjEiLCJzY29wZXMiOlsicHVzaF9ub3RpZmljYXRpb25zIiwiZmlsZXMiLCJjcm0iLCJmaWxlc19kZWxldGUiLCJub3RpZmljYXRpb25zIl19.mes5s_ZUvV42m95LfI2pI8HuuaTKMHxGpKDO0aZJRerp-pH7U1udhkTGOXMk7Dhv34PN763XSclNzS9YkQsDQmCzkagAULByCsCkUwk_WFRqtCIvkD-wbkpEcfQNYRV7pPjeDnrMISsUfIKNx4Z4oTuoHPZ4x6zV6PmWvPl5FmFLlM32D4jGrH28PH2yTy63fUP0gfYoHXaQaeKVvt7IECwx_lDmz5apAigNEB1dqzthW8BNeEs_2x9yJQC_CF0EwMdI_abejDyeAch7ZqVhCaiDPj6w2EV3AoZAmWXi5QxjhmQZVC0JCXMlLTATuR1Ee0ygii0-8rDCJhT14ooDQA";
    const value = ref<string>("");
    const query = ref<string>("");
    const error = ref<string | null>(null);

    const onSearch = (searchValue: string) => {
      if (searchValue.length === 0 || searchValue.length >= 3) {
        query.value = searchValue;
        searchContacts();
      } else {
        message.warning("Введите как минимум 3 символа");
      }
    };

    const searchContacts = () => {
      axios
        .get("http://localhost:3000/api/leads", {
          params: {
            query: query.value,
          },
          headers: {
            Authorization: `Bearer ${token}`,
          },
        })
        .then((response) => {
          data.value = response.data.map((item: Contacts) => {
            const phone = item.contact.custom_fields_values[0].values[0].value;
            const email = item.contact.custom_fields_values[1].values[0].value;
            return {
              key: item.id,
              id: item.id,
              price: item.price,
              name: `Сделка ${item.id}`,
              date: item.date,
              description: `ФИ ${item.contact.name} | Phone ${phone} | email ${email}`,
              liable: item.liable.name,
              pipeline: item.pipeline.name,
            };
          });
        })
        .catch((error) => {
          error.value = error.message;
        });
    };

    searchContacts();
    return {
      value,
      data,
      columns,
      error,
      onSearch,
    };
  },
});
</script>
