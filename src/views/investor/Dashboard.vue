<template>
    <!--begin::Row-->
    <div class="row g-5 g-xl-10 mb-5 mb-xl-10">
      <!--begin::Col-->
      <div class="col-md-6 col-lg-6 col-xl-6 col-xxl-3 mb-md-5 mb-xl-10">
        <Widget1
          className="h-md-50 mb-5 mb-xl-10"
          description="Active Projects"
          bgColor="#F1416C"
          :bgImage="getAssetPath('media/patterns/vector-1.png')"
        />
  
        <Widget2
          className="h-md-50 mb-5 mb-xl-10"
          :icon="false"
          stats="357"
          description="Professionals"
          labelColor="dark"
          textColor="gray-300"
        />
      </div>
      <!--end::Col-->
  
      <!--begin::Col-->
      <div class="col-md-6 col-lg-6 col-xl-6 col-xxl-3 mb-md-5 mb-xl-10">
        <Widget3 className="h-md-50 mb-5 mb-xl-10" :chartSize="70" />
  
        <Widget4 className="h-lg-50" />
      </div>
      <!--end::Col-->
  
      <!--begin::Col-->
      <div class="col-xxl-6">
        <Widget6 className="h-md-100" />
      </div>
      <!--end::Col-->
    </div>
    <!--end::Row-->
  
    <!--begin::Row-->
    <div class="row gx-5 gx-xl-10">
  
      <!--begin::Col-->
      <div class="col-xl-12 mb-5 mb-xl-10">
        <Widget7 className="h-xl-100" />
      </div>
      <!--end::Col-->
    </div>
    <!--end::Row-->
  
  </template>
  
  <script lang="ts">
  import { getAssetPath } from "@/core/helpers/assets";
  import { defineComponent, onMounted } from "vue";
  import Widget1 from "@/components/dashboard-default-widgets/Widget1.vue";
  import Widget2 from "@/components/dashboard-default-widgets/Widget2.vue";
  import Widget3 from "@/components/dashboard-default-widgets/Widget3.vue";
  import Widget4 from "@/components/dashboard-default-widgets/Widget4.vue";
  import Widget6 from "@/components/dashboard-default-widgets/Widget6.vue";
  import Widget7 from "@/components/dashboard-default-widgets/Widget7.vue";
  import { useInvestorStore } from "@/stores/investor";
  import { useAuthStore } from "@/stores/auth";
  
  export default defineComponent({
    name: "investor-dashboard",
    components: {
      Widget1,
      Widget2,
      Widget3,
      Widget4,
      Widget6,
      Widget7,
    },
    setup() {
      const authStore = useAuthStore();
      const investStore = useInvestorStore();
      onMounted(() => {
        investStore.getAccount(authStore.user.id.toString());
      })
      return {
        getAssetPath,
      };
    },
  });
  </script>
  