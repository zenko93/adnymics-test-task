<template>
  <div>
    <div class="app-header">
      <h1>Conflicts</h1>
      <img src="../assets/images/Ambox_important_blue.svg" class="app-img" alt="!">
    </div>
    <table class="app-table">
      <thead class="app-col">
        <td class="app-row">Time Period</td>
        <td>Campains</td>
        <td>Conflicts</td>
      </thead>
      <tr v-for="{ period, name, conflict } in conflicts" :key="name">
        <td class="app-row">{{ period }}</td>
        <td>{{ name }}</td>
        <td>{{ conflict }}</td>
      </tr>
    </table>
  </div>
</template>

<script>
import CampainsStatus from "../../mockData.json";

export default {
  name: "Conflicts",
  computed: {
    sameDate() {
      return CampainsStatus.data.filter((item) => {
        return CampainsStatus.data.some(
          (someItem) =>
            someItem.start_date === item.start_date &&
            someItem.end_date === item.end_date &&
            someItem.id !== item.id
        );
      });
    },
    conflicts() {
      return this.sameDate.reduce((acc, item, itemIdx, arr) => {
        const companyWithSamePeriod = arr.find(
          (findItem, idx) =>
            findItem.start_date === item.start_date &&
            findItem.end_date === item.end_date &&
            findItem.id !== item.id &&
            idx > itemIdx
        );

        if (!companyWithSamePeriod) {
          return acc;
        }

        const conflictGender = item.targeting.gender
          .filter((gender) =>
            companyWithSamePeriod.targeting.gender.includes(gender)
          )
          .join(" and ");

        const customerType = item.targeting.customer_type
          .filter((type) =>
            companyWithSamePeriod.targeting.customer_type.includes(type)
          )
          .join(" and ");

        const conflictText = conflictGender
          ? `Gender: ${conflictGender} ${this.choosePreposition(conflictGender)} assigned to two or more campaigns`
          : `Customer Type: ${customerType} ${this.choosePreposition(customerType)} assigned to two or more campaings`;

        acc.push({
          period: `${item.start_date} - ${item.end_date}`,
          name: `${item.name} and ${companyWithSamePeriod.name}`,
          conflict: conflictText,
        });

        return acc;
      }, []);
    },
  },
  methods: {
    choosePreposition(conflict) {
      return `${conflict.includes("and") ? "are" : "is"}`;
    },
  },
};
</script>

<style scoped>
table {
  border-collapse: collapse;
  padding: 4px;
}

.app-header {
  display: flex;
  align-items: center;
}

.app-img {
  width: 50px;
  height: 50px;
}

h1 {
  color: blue;
  padding-right: 12px;
}

thead {
  border-bottom: 1px solid black;
}

td {
  padding: 16px 8px;
}

tr:nth-child(2n + 1) {
  background-color: gray;
}
</style>
