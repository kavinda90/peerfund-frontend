<template>
  <div class="card">
    <div class="card-header border-0 pt-6">
      <!--begin::Card title-->
      <div class="card-title">
        <!--begin::Search-->
        <div class="d-flex align-items-center position-relative my-1">
          <KTIcon
              icon-name="magnifier"
              icon-class="fs-1 position-absolute ms-6"
          />
          <input
              type="text"
              v-model="search"
              @input="searchItems()"
              class="form-control form-control-solid w-250px ps-15"
              placeholder="Search Loans"
          />
        </div>
        <!--end::Search-->
      </div>
      <!--begin::Card title-->
      <!--begin::Card toolbar-->
      <div class="card-toolbar">
        <!--begin::Toolbar-->
        <div
            v-if="selectedIds.length === 0"
            class="d-flex justify-content-end"
            data-kt-customer-table-toolbar="base"
        >
          <!--begin::Export-->
          <button
              type="button"
              class="btn btn-light-primary"
              data-bs-toggle="modal"
              data-bs-target="#kt_customers_export_modal"
          >
            <KTIcon icon-name="exit-up" icon-class="fs-2"/>
            Export
          </button>
          <!--end::Export-->
        </div>
        <!--end::Toolbar-->
        <!--begin::Group actions-->
        <div
            v-else
            class="d-flex justify-content-end align-items-center"
            data-kt-customer-table-toolbar="selected"
        >
          <div class="fw-bold me-5">
            <span class="me-2">{{ selectedIds.length }}</span
            >Selected
          </div>
          <button
              type="button"
              class="btn btn-danger"
              @click="deleteFewCustomers()"
          >
            Delete Selected
          </button>
        </div>
        <!--end::Group actions-->
        <!--begin::Group actions-->
        <div
            class="d-flex justify-content-end align-items-center d-none"
            data-kt-customer-table-toolbar="selected"
        >
          <div class="fw-bold me-5">
            <span
                class="me-2"
                data-kt-customer-table-select="selected_count"
            ></span
            >Selected
          </div>
          <button
              type="button"
              class="btn btn-danger"
              data-kt-customer-table-select="delete_selected"
          >
            Delete Selected
          </button>
        </div>
        <!--end::Group actions-->
      </div>
      <!--end::Card toolbar-->
    </div>
    <div class="card-body pt-0">
      <Datatable
          @on-sort="sort"
          @on-items-select="onItemSelect"
          :data="tableData"
          :header="tableHeader"
          :enable-items-per-page-dropdown="true"
          :checkbox-enabled="false"
          checkbox-label="id"
      >
        <template v-slot:name="{ row: customer }">
          {{ customer.name }}
        </template>
        <template v-slot:email="{ row: customer }">
          <a href="#" class="text-gray-600 text-hover-primary mb-1">
            {{ customer.email }}
          </a>
        </template>
        <template v-slot:company="{ row: customer }">
          {{ customer.company }}
        </template>
        <template v-slot:paymentMethod="{ row: customer }">
          <img :src="customer.payment.icon" class="w-35px me-3" alt=""/>{{
            customer.payment.ccnumber
          }}
        </template>
        <template v-slot:date="{ row: customer }">
          {{ customer.date }}
        </template>
        <template v-slot:actions="{ row: customer }">
          <button
              class="btn btn-sm btn-light btn-active-light-primary"
              data-bs-target="#b_modal_loan_details"
              data-bs-toggle="modal"
              type="button"
          >
            View
            <KTIcon icon-name="exit-right-corner" icon-class="fs-5 ms-2"/>
          </button>
        </template>
      </Datatable>
    </div>
  </div>
  <LoanDetailsModal></LoanDetailsModal>
  <ExportCustomerModal></ExportCustomerModal>
  <AddCustomerModal></AddCustomerModal>
</template>

<script lang="ts">
import {getAssetPath} from "@/core/helpers/assets";
import {defineComponent, onMounted, ref} from "vue";
import Datatable from "@/components/kt-datatable/KTDataTable.vue";
import type {Sort} from "@/components/kt-datatable//table-partials/models";
import ExportCustomerModal from "@/components/modals/forms/ExportCustomerModal.vue";
import AddCustomerModal from "@/components/modals/forms/AddCustomerModal.vue";
import type {ICustomer} from "@/core/data/customers";
import customers from "@/core/data/customers";
import arraySort from "array-sort";
import {MenuComponent} from "@/assets/ts/components";
import LoanDetailsModal from "@/components/borrower/LoanDetailsModal.vue";

export default defineComponent({
  name: "customers-listing",
  components: {
    LoanDetailsModal,
    Datatable,
    ExportCustomerModal,
    AddCustomerModal,
  },
  setup() {
    const tableHeader = ref([
      {
        columnName: "Customer Name",
        columnLabel: "name",
        sortEnabled: true,
        columnWidth: 175,
      },
      {
        columnName: "Email",
        columnLabel: "email",
        sortEnabled: true,
        columnWidth: 230,
      },
      {
        columnName: "Company",
        columnLabel: "company",
        sortEnabled: true,
        columnWidth: 175,
      },
      {
        columnName: "Payment Method",
        columnLabel: "paymentMethod",
        sortEnabled: true,
        columnWidth: 175,
      },
      {
        columnName: "Created Date",
        columnLabel: "date",
        sortEnabled: true,
        columnWidth: 225,
      },
      {
        columnName: "Actions",
        columnLabel: "actions",
        sortEnabled: false,
        columnWidth: 135,
      },
    ]);
    const selectedIds = ref<Array<number>>([]);

    const tableData = ref<Array<ICustomer>>(customers);
    const initCustomers = ref<Array<ICustomer>>([]);

    onMounted(() => {
      initCustomers.value.splice(0, tableData.value.length, ...tableData.value);
    });

    const deleteFewCustomers = () => {
      selectedIds.value.forEach((item) => {
        deleteCustomer(item);
      });
      selectedIds.value.length = 0;
    };

    const deleteCustomer = (id: number) => {
      for (let i = 0; i < tableData.value.length; i++) {
        if (tableData.value[i].id === id) {
          tableData.value.splice(i, 1);
        }
      }
    };

    const search = ref<string>("");
    const searchItems = () => {
      tableData.value.splice(0, tableData.value.length, ...initCustomers.value);
      if (search.value !== "") {
        let results: Array<ICustomer> = [];
        for (let j = 0; j < tableData.value.length; j++) {
          if (searchingFunc(tableData.value[j], search.value)) {
            results.push(tableData.value[j]);
          }
        }
        tableData.value.splice(0, tableData.value.length, ...results);
      }
      MenuComponent.reinitialization();
    };

    const searchingFunc = (obj: any, value: string): boolean => {
      for (let key in obj) {
        if (!Number.isInteger(obj[key]) && !(typeof obj[key] === "object")) {
          if (obj[key].indexOf(value) != -1) {
            return true;
          }
        }
      }
      return false;
    };

    const sort = (sort: Sort) => {
      const reverse: boolean = sort.order === "asc";
      if (sort.label) {
        arraySort(tableData.value, sort.label, {reverse});
      }
    };
    const onItemSelect = (selectedItems: Array<number>) => {
      selectedIds.value = selectedItems;
    };

    return {
      tableData,
      tableHeader,
      deleteCustomer,
      search,
      searchItems,
      selectedIds,
      deleteFewCustomers,
      sort,
      onItemSelect,
      getAssetPath,
    };
  },
});
</script>