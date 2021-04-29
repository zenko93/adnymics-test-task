<template>
  <div>
    <h1>Conflicts</h1>
    <table width="100%" border="1">
      <tr>
        <td>Time Period</td>
        <td>Campains</td>
        <td>Conflicts</td>
      </tr>
      <tr v-for="{ period, name, conflict } in conflicts" :key="name">
        <td>{{ period }}</td>
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
  props: {
    msg: String,
  },
  computed: {
    sameDate() {
      const valueArr = CampainsStatus.data.map((item) => ({
        startDate: item.start_date,
        endDate: item.end_date,
        id: item.id,
      }));

      return CampainsStatus.data.filter((item) => {
        return valueArr.some(
          (someItem) =>
            someItem.startDate === item.start_date &&
            someItem.endDate === item.end_date &&
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
          ? `Gender: ${conflictGender} is assigned to two or more campaigns`
          : `Customer Type: ${customerType} are assigned to two or more campaings`;

        acc.push({
          period: `${item.start_date} - ${item.end_date}`,
          name: `${item.name} and ${companyWithSamePeriod.name}`,
          conflict: conflictText,
        });

        return acc;
      }, []);
    },
  },
};
</script>

<style scoped></style>
