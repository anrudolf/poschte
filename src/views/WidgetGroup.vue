<template>
  <div class="p-4" v-if="entity">
    <h1>Widget Group: {{ entity.label.de }}</h1>
    <div class="flex flex-wrap">
      <app-widget
        v-for="(widget, i) in entity.content"
        :key="i"
        type="product"
        :widget="{ id: widget }"
        @click="(ev) => add(widget)"
      />
    </div>
  </div>
</template>

<script>
import { ref, toRefs } from "vue";
import { useStore } from "vuex";
import { useRouter } from "vue-router";

import appWidget from "@/components/Widget.vue";

import firebase from "../firebaseInit";
const db = firebase.firestore();

export default {
  components: {
    appWidget,
  },
  props: {
    id: String,
  },
  setup(props) {
    const store = useStore();
    const router = useRouter();

    const { id } = toRefs(props);
    const entity = ref(null);

    const documentPath = `/widget-groups/${id.value}`;

    db.doc(documentPath)
      .get()
      .then((doc) => {
        entity.value = doc.data();
      })
      .catch((e) => console.log(e));

    const add = (code) => {
      store.dispatch("kasse/add", code);
      router.push("/");
    };

    return { entity, add };
  },
};
</script>