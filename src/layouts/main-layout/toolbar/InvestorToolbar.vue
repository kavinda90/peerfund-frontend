<template>
    <!--begin::Toolbar-->
    <div id="kt_app_toolbar" class="app-toolbar py-3 py-lg-6">
      <!--begin::Toolbar container-->
      <div
        id="kt_app_toolbar_container"
        class="app-container d-flex flex-stack"
        :class="{
          'container-fluid': toolbarWidthFluid,
          'container-xxl': !toolbarWidthFluid,
        }"
      >
        <KTPageTitle />
        <!--begin::Actions-->
        <div class="d-flex align-items-center gap-2 gap-lg-3">
          <!--begin::Secondary button-->
          <router-link
            to="/investor/funds"
            class="btn btn-sm fw-bold bg-body btn-color-gray-700 btn-active-color-primary"
            >Fund Account</router-link
          >
          <!--end::Secondary button-->
          <!--begin::Primary button-->
          <router-link
            to="/investor/invest"
            class="btn btn-sm fw-bold btn-primary"
            >Invest</router-link
          >
          <!-- <RouterLink to="/investor/invest">
            <a
              href="/investor/invest"
              class="btn btn-sm fw-bold btn-primary"
              >Invest</a
            >
          </RouterLink> -->
          <!--end::Primary button-->
        </div>
        <!--end::Actions-->
      </div>
      <!--end::Toolbar container-->
    </div>
    <!--end::Toolbar-->
  </template>
  
  <script lang="ts">
  import { defineComponent } from "vue";
  import { toolbarWidthFluid } from "@/core/helpers/config";
  import KTPageTitle from "@/layouts/main-layout/toolbar/PageTitle.vue";
  
  export default defineComponent({
    name: "investor-layout-toolbar",
    components: {
      KTPageTitle,
    },
    setup() {
      return {
        toolbarWidthFluid,
      };
    },
  });
  </script>
  