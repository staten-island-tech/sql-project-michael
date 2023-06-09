<script>
import { ref } from "vue";
import { supabase } from "../supabase";
export default {
  name: "log",
  components: {},
  setup() {
    const data = ref([]);
    const datal = ref(null);

    const getdata = async () => {
      try {
        const { data: workouts, error } = await supabase
          .from("workouts")
          .select("*");
        if (error) throw error;
        data.value = workouts;
        datal.value = true;
        console.log(data.value);
      } catch (error) {
        console.warn(error.message);
      }
      return { data, datal };
    };
    getdata();
  },
};
</script>

<template>
  <div v-if="datal">
    <div>
      <p>{{ workouts.workoutday.value }}</p>
      <p>{{ workouts.workouttimec }}</p>
      <p>{{ workouts.workouttimel }}</p>
      <p>{{ workouts.workouttimeh }}</p>
    </div>
  </div>
</template>
