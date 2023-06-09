<template>
  <div class="create">
    <form @submit.prevent="submitlog">
      <h2>Record Workout</h2>
      <div>
        <label for="workout-timec">When did you come into the gym?</label>
        <input
          id="workout-timec"
          type="Time"
          requiered
          v-model="workouttimec"
        />
      </div>
      <div>
        <label for="workout-timel">When did you leave the gym?</label>
        <input
          id="workout-timel"
          type="Time"
          requiered
          v-model="workouttimel"
        />
      </div>
      <div>
        <label for="workout-timeh">How many Hours were you at the gym?</label>
        <input
          id="workout-timeh"
          type="number"
          requiered
          v-model="workouttimeh"
        />
      </div>
      <div>
        <label for="workoutday">When day did you come into the gym?</label>
        <select id="workoutday" requiered v-model="workoutday">
          <option value="select-day">Select Day</option>
          <option value="Monday">Monday</option>
          <option value="Tuesday">Tuesday</option>
          <option value="Wednesday">Wednesday</option>
          <option value="Thursday">Thursday</option>
          <option value="Friday">Friday</option>
          <option value="Saturday">Saturday</option>
          <option value="Sunday">Sunday</option>
        </select>
      </div>
      <div v-if="workouttimeh > 0"><h3>Great Job!</h3></div>
      <div v-if="workouttimeh < 1"><h3>I Believe In You!</h3></div>
      <button type="submit">Add To Workout Log</button>
    </form>
  </div>
  <form @submit.prevent="delData">
    <select id="deleterow" requiered v-model="deleterow">
      <option value="delete-log">Delete Log</option>
      <option value="Monday">Monday</option>
      <option value="Tuesday">Tuesday</option>
      <option value="Wednesday">Wednesday</option>
      <option value="Thursday">Thursday</option>
      <option value="Friday">Friday</option>
      <option value="Saturday">Saturday</option>
      <option value="Sunday">Sunday</option>
    </select>
    <button type="submit">Remove form Workout Log</button>
  </form>
  <div v-if="dataloaded === true">
    <p>{{ workoutday }}</p>
    <p>{{ workouttimec }}</p>
    <p>{{ workouttimel }}</p>
    <p>{{ workouttimeh }}</p>
    <!-- <p>{{ workout.workoutday.value }}</p>
    <p>{{ workouts.workouttimec }}</p>
    <p>{{ workouts.workouttimel }}</p>
    <p>{{ workouts.workouttimeh }}</p> -->
  </div>
</template>

<script>
import { ref, resolveDirective, toRefs, watch } from "vue";
import { supabase } from "../supabase";
export default {
  name: "create",
  props: {
    Data: Object,
    id: Number,
  },
  setup() {
    const workouttimec = ref("");
    const workouttimel = ref("");
    const workoutday = ref("select-day");
    const workouttimeh = ref("");
    const log = ref([]);
    const errorMsg = ref(null);
    const deleterow = ref("delete-log");

    const submitlog = async () => {
      try {
        const { error } = await supabase.from(`workouts`).insert([
          {
            workoutday: workoutday.value,
            workouttimec: workouttimec.value,
            workouttimel: workouttimel.value,
            workouttimeh: workouttimeh.value,
            log: log.value,
          },
        ]);
        if (error) throw error;
        workoutday.value = "select-day";
        workouttimec.value = null;
        workouttimel.value = null;
        workouttimeh.value = null;
      } catch (error) {
        errorMsg.value = `Error: ${error.message}`;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };
    const delData = async () => {
      try {
        const { error } = await supabase
          .from(`workouts`)
          .delete()
          .eq(`workoutday`, deleterow.value);
        if (error) throw error;
        deleterow.value = "delete-log";
      } catch (error) {
        errorMsg.value = `Error: ${error.message}`;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };
    const data = ref([]);
    let dataloaded = ref(null);

    const getData = async () => {
      try {
        const { data: workouts, error } = await supabase
          .from("workouts")
          .select("*");
        data.value = workouts;
        dataloaded.value = true;
        console.log(workouts);
        if (error) {
          console.log(error);
        }
      } catch (error) {
        console.log(error);
      }
      return { data, dataloaded };
    };
    getData();

    return {
      workoutday,
      workouttimec,
      workouttimel,
      workouttimeh,
      submitlog,
      log,
      delData,
      deleterow,
      dataloaded,
    };
  },
};
</script>
