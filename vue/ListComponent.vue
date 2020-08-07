<template>
  <div>
    <v-card tile style="background:none">
      <v-card-title class="primary pa-0" style="min-height:50px">
        <div v-if="!isMobile" class="col-12 pa-0">
          <v-btn
            class="ma-2"
            tile
            small=""
            @click="pagination.filters.p_tagged = !pagination.filters.p_tagged"
            :color="(pagination.filters.p_tagged) ? 'accent' : 'error'"
          >
            <v-icon left>mdi-tag-off-outline</v-icon>Pending to Tag
          </v-btn>
          <v-btn
            class="ma-2"
            tile
            small=""
            @click="pagination.filters.tagged = !pagination.filters.tagged"
            :color="(pagination.filters.tagged) ? 'accent' : 'error'"
          >
            <v-icon left>mdi-tag-outline</v-icon>Tagged
          </v-btn>
          <v-btn
            class="ma-2"
            tile
            small=""
            @click="pagination.filters.p_invoice = !pagination.filters.p_invoice"
            :color="(pagination.filters.p_invoice) ? 'accent' : 'error'"
          >
            <v-icon left>mdi-newspaper-variant-outline</v-icon>Pending to Invoice
          </v-btn>
          <v-btn class="ma-2" small="" tile color="error" @click="import_dailog = true">
            <v-icon left>mdi-download</v-icon>Import
          </v-btn>
          <v-btn class="ma-2" small="" tile color="error" :disabled="selectedRows.length != 1" @click="split_dailog = true">
            <v-icon left>mdi-arrow-split-vertical</v-icon>Split
          </v-btn>
          <v-btn
            class="ma-2"
            tile
            color="error"
            small=""
            v-if="selectedRows.length > 0 && hasInvoices.length == 0"
            :disabled="selectedRows.length <= 0 || hasInvoices.length >= 1"
            @click="generateInvoices"
          >
            <v-icon left>mdi-newspaper-plus</v-icon>Generate Invoice
          </v-btn>
          <v-btn
            class="ma-2"
            tile
            color="error"
            small=""
            v-if="selectedRows.length > 0 && hasInvoices.length == 0"
            @click="deleteTransactions"
          >
            <v-icon left>mdi-delete-variant</v-icon>Delete Transaction
          </v-btn>
          <v-btn
            class="ma-2"
            tile
            color="error"
            small=""
            v-if="selectedRows.length > 0 && hasInvoices.length > 0"
            @click="deleteInvoices"
          >
            <v-icon left>mdi-newspaper-minus</v-icon>Delete Invoice
          </v-btn>
        </div>
        <v-row class="px-5 pb-2 justify-space-between" v-if="!isMobile">
          <div style="width:80px">
            <v-select
              dense
              flat
              hide-details
              solo
              :items="alphabets"
              v-model="pagination.filters.alphabet"
            ></v-select>
          </div>
          <div style="width:400px">
            <v-text-field
              v-model="search"
              append-icon="mdi-magnify"
              label="Search"
              single-line
              hide-details
              dense
              solo
            ></v-text-field>
          </div>
        </v-row>
        <!--MOBILE VIEW -->
        <div v-if="isMobile" :style="getStyles">
          <v-row no-gutters>
            <v-col cols="2" class="px-1">
              <v-select
                dense
                flat
                hide-details
                solo
                :items="alphabets"
                v-model="pagination.filters.alphabet"
              ></v-select>
            </v-col>
            <v-col cols="9" class="px-1">
              <v-text-field
                v-model="search"
                append-icon="mdi-magnify"
                label="Search"
                single-line
                hide-details
                dense
                solo
                clearable
              ></v-text-field>
            </v-col>
            <v-col cols="1" class="px-1">
              <v-menu bottom left>
                <template v-slot:activator="{ on, attrs }">
                  <v-btn dark icon v-bind="attrs" v-on="on">
                    <v-icon>mdi-filter</v-icon>
                  </v-btn>
                </template>
                <v-list dense>
                  <v-list-item @click="pagination.filters.p_tagged = !pagination.filters.p_tagged"
                  :class="{'v-list-item--active' : pagination.filters.p_tagged}"
                  color="primary"
                  >
                    <v-list-item-icon>
                      <v-icon>mdi-tag-off-outline</v-icon>
                    </v-list-item-icon>
                    <v-list-item-content>
                      <v-list-item-title>Pending To Tag</v-list-item-title>
                    </v-list-item-content>
                  </v-list-item>
                  <v-list-item @click="pagination.filters.tagged = !pagination.filters.tagged"
                  :class="{'v-list-item--active' : pagination.filters.tagged}"
                  color="primary"
                  >
                    <v-list-item-icon>
                      <v-icon>mdi-tag-outline</v-icon>
                    </v-list-item-icon>
                    <v-list-item-content>
                      <v-list-item-title>Tagged</v-list-item-title>
                    </v-list-item-content>
                  </v-list-item>
                  <v-list-item
                    :class="{'v-list-item--active' : pagination.filters.p_invoice}"
                    color="primary"
                    @click="pagination.filters.p_invoice = !pagination.filters.p_invoice"
                  >
                    <v-list-item-icon>
                      <v-icon>mdi-newspaper-variant-outline</v-icon>
                    </v-list-item-icon>
                    <v-list-item-content>
                      <v-list-item-title>Pending to Invoice</v-list-item-title>
                    </v-list-item-content>
                  </v-list-item>
                </v-list>
              </v-menu>
            </v-col>
          </v-row>
          <div class="d-flex justify-space-around" style="position:fixed;bottom:10px;width:95%">
            <v-btn fab small color="error" @click="import_dailog = true" v-if="selectedRows.length == 0">
              <v-icon>mdi-download</v-icon>
            </v-btn>
            <v-btn fab small color="error" @click="split_dailog = true" v-if="selectedRows.length == 1">
              <v-icon>mdi-arrow-split-vertical</v-icon>
            </v-btn>
            <v-btn
              fab
              small
              color="error"
              v-if="selectedRows.length > 0 && hasInvoices.length == 0"
              :disabled="selectedRows.length <= 0 || hasInvoices.length >= 1"
              @click="generateInvoices"
            >
              <v-icon>mdi-newspaper-plus</v-icon>
            </v-btn>
            <v-btn
              fab
              small
              color="error"
              v-if="selectedRows.length > 0 && hasInvoices.length == 0"
              @click="deleteTransactions"
            >
              <v-icon>mdi-delete-variant</v-icon>
            </v-btn>
             <v-btn
              fab
              small
              color="error"
              v-if="selectedRows.length > 0 && hasInvoices.length > 0"
              @click="deleteInvoices"
            >
              <v-icon>mdi-newspaper-minus</v-icon>
            </v-btn>
          </div>
        </div>
      </v-card-title>
      <v-card-text class="pa-0" :style="getStyles">
        <v-data-table
          :headers="headers"
          class="elevation-1"
          :search="search"
          :items="items.data"
          :server-items-length="items.total"
          :options.sync="pagination"
          :loading="loading"
          mobile-breakpoint="100"
          dense
          :show-expand="isMobile"
          item-key="trans_id"
          :expanded.sync="expanded"
          single-expand=""
          :footer-props="{
            'items-per-page-options': pagination_settings.perPageList
          }"
        >
          <template v-slot:item.checkbox="{item}">
            <v-simple-checkbox v-model="item.selected" :ripple="false"></v-simple-checkbox>
          </template>
          <template v-slot:item.trans_date="data">
            <span>{{ getFormatedDate(data.value) }}</span>
          </template>
          <template v-slot:item.rev_amount="data">
            <span
              :class="{'error--text' : (data.value < 0) , 'primary--text' : (data.value > 0)}"
            >{{data.value}}</span>
          </template>
          <template v-slot:item.bank_id="data">
            <span
              :title="(data.item.bank_id) ? data.item.bank.bank_name : ''"
            >{{(data.item.bank_id) ? data.item.bank.bank_code : ''}}</span>
          </template>
          <template v-slot:item.project_id="data">
            <v-select
              dense
              flat
              :items="project_list"
              v-model="data.item.project_id"
              solo
              hide-details
              @change="updateTransaction(data.item)"
              small
              class="body-2"
            ></v-select>
          </template>
          <template v-slot:item.item_id="data">
            <v-select
              dense
              flat
              :items="item_list"
              v-model="data.item.item_id"
              solo
              hide-details
              @change="updateTransaction(data.item)"
              class="body-2"
            ></v-select>
          </template>
          <template v-slot:item.vendor_id="data">
            <v-select
              dense
              flat
              :items="vendor_list"
              v-model="data.item.vendor_id"
              solo
              hide-details
              @change="updateTransaction(data.item)"
              class="body-2"
            ></v-select>
          </template>
          <template v-slot:item.invoice="data">
            <a href="javascript:;" v-if="data.value">{{data.value.title}}</a>
          </template>
          <template v-slot:expanded-item="{ headers,item}" v-if="isMobile">
            <td :colspan="headers.length">
              <v-row dense>
                <v-col cols="4">
                  <v-select
                    dense
                    flat
                    :items="project_list"
                    v-model="item.project_id"
                    outlined
                    label="Project"
                    hide-details
                    @change="updateTransaction(item)"
                    x-small
                    class="body-2"
                  ></v-select>
                </v-col>
                <v-col cols="4">
                  <v-select
                    dense
                    flat
                    :items="item_list"
                    v-model="item.item_id"
                    outlined
                    label="Item"
                    hide-details
                    @change="updateTransaction(item)"
                    class="body-2"
                    x-small
                  ></v-select>
                </v-col>
                <v-col cols="4">
                  <v-select
                    dense
                    flat
                    :items="vendor_list"
                    v-model="item.vendor_id"
                    outlined
                    label="Vendor"
                    hide-details
                    @change="updateTransaction(item)"
                    class="body-2"
                  ></v-select>
                </v-col>
              </v-row>
            </td>
          </template>
        </v-data-table>
      </v-card-text>
    </v-card>
    <v-dialog v-model="import_dailog" max-width="500" persistent :fullscreen="isMobile">
      <v-card :loading="import_dailog_loading">
        <v-card-title class="headline">Import Transactions</v-card-title>
        <v-card-text>
          <v-row dense>
            <v-col cols="6">
              <v-select
                label="Bank"
                :items="banks"
                v-model="selected_bank"
                hide-details
                item-text="bank_name"
                item-value="bank_id"
                outlined
                dense
              ></v-select>
            </v-col>
            <v-col cols="6">
              <v-file-input
                label="Select Csv File"
                accept=".csv"
                v-model="selected_file"
                @change="updateFile"
                dense
              ></v-file-input>
            </v-col>
          </v-row>
          <v-row dense>
            <v-col cols="4" v-for="(item,index) in db_column" :key="index">
              <v-select
                outlined
                :label="item.label"
                :items="imported_columns"
                hide-details
                dense
                v-model="db_column[index].value"
                :error-messages="db_column[index].error"
              ></v-select>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-btn color="red darken-1" text @click="import_dailog = false">Cancel</v-btn>
          <v-spacer></v-spacer>
          <v-btn
            v-if="selected_file && selected_bank"
            color="green darken-1"
            text
            @click="importData"
            :loading="import_dailog_loading"
          >Import</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="split_dailog" max-width="390" persistent :fullscreen="isMobile" :loading="split_dailog_loading">
      <v-card>
        <v-card-title class="headline">Split Transaction</v-card-title>
        <v-card-text v-if="selectedRows.length">
          <v-row dense>
            <v-col><b>Transaction Date</b></v-col>
            <v-col>{{ getFormatedDate(selectedRows[0].trans_date) }}</v-col>
          </v-row>
          <v-row dense>
            <v-col><b>Amount</b></v-col>
            <v-col>
              <span
              :class="{'error--text' : (selectedRows[0].rev_amount < 0) , 'primary--text' : (selectedRows[0].rev_amount > 0)}">
              {{ selectedRows[0].rev_amount }}
              </span>
            </v-col>
          </v-row>
          <v-row dense>
            <v-col><b>Description</b></v-col>
            <v-col>{{ selectedRows[0].description }}</v-col>
          </v-row>
          <v-row>
            <v-col>
              <v-text-field dense=""
              outlined=""
              hide-details=""
              v-model="split_amount"
              type="number"
              label="Split Amount"
              :autofocus="split_dailog"
              ></v-text-field>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-btn color="red darken-1" text @click="split_dailog = false">Cancel</v-btn>
          <v-spacer></v-spacer>
          <v-btn color="green darken-1" v-if="!isNaN(split_amount)" @click="splitBill" text :loading="split_dailog_loading">Split</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import axios from "../plugins/axios";
import { parseParams, getAlphabets } from "../plugins/helper";
import { mapState } from "vuex";
export default {
  name: "Transaction",
  data() {
    return {
      import_dailog: false,
      import_dailog_loading: false,
      split_dailog : false,
      split_dailog_loading: false,
      banks: [],
      selected_bank: null,
      split_amount: '',
      search: "",
      items: {
        current_page: 1,
        per_page: 10,
        data: [],
      },
      loading: false,
      db_column: [
        { label: "Trans. Date", field: "trans_date", value: "", error: null },
        { label: "Description", field: "description", value: "", error: null },
        { label: "Amount", field: "amount", value: "", error: null },
      ],
      imported_columns: [],
      selected_file: null,
      project_list: [],
      item_list: [],
      vendor_list: [],
      pagination: {
        itemsPerPage: 10,
        sortBy: ["trans_code"],
        page: 1,
        filters: {
          p_tagged: true,
          tagged: false,
          p_invoice: false,
          alphabet: "",
        },
      },
      expanded: [],
      alphabets: getAlphabets(),
    };
  },
  computed: {
    ...mapState("utils", ["pagination_settings"]),
    isMobile() {
      return this.$vuetify.breakpoint.xsOnly;
    },
    headers() {
      if (this.isMobile) {
        return [
          { value: "checkbox", text: "#", sortable: false },
          { value: "bank_id", text: "Bank" },
          { value: "trans_date", text: "Trans. Date", sortable: true },
          { value: "rev_amount", text: "Amount", sortable: false },
          { text: "", value: "data-table-expand" },
        ];
      } else {
        return [
          { value: "checkbox", text: "#", sortable: false },
          { value: "bank_id", text: "Bank" },
          { value: "trans_date", text: "Trans. Date", sortable: true },
          { value: "rev_amount", text: "Amount", sortable: false },
          { value: "project_id", text: "Project", width: "150px" },
          { value: "item_id", text: "Item", width: "150px" },
          { value: "vendor_id", text: "Vendor", width: "150px" },
          { value: "invoice", text: "Invoice", sortable: false },
          { value: "description", text: "Description", sortable: false },
          { value: "docx", text: "Documents", sortable: false },
        ];
      }
    },
    selectedRows() {
      return this.items.data.filter(function (item) {
        return item.selected == true;
      });
    },
    hasInvoices() {
      return this.items.data.filter(function (item) {
        return item.invoice != null && item.selected == true;
      });
    },
    getStyles() {
      if (this.isMobile) {
        return {
          maxWidth: "95%",
          margin: "0 auto",
        };
      } else {
        return {};
      }
    },
    pageData() {
      let paginationData = this.pagination;
      paginationData.search = this.search;
      return paginationData;
    },
  },
  watch: {
    search: {
      handler() {
        this.getTransaction(this.pageData);
      },
    },
    pagination: {
      handler() {
        this.getTransaction(this.pageData);
      },
      deep: true,
    },
    import_dailog: {
      handler(val) {
        if (!val) {
          this.selected_file = null;
          this.selected_bank = null;
          this.imported_columns = [];
          this.db_column = this.db_column.map(function (item) {
            item.error = null;
            return item;
          });
        }
      },
    },
    split_dailog : {
      handler(val) {
       if (!val) {
         this.split_amount = null
       }
      }
    }
  },
  methods: {
    getFormatedDate(date) {
      return new Date(date).toLocaleDateString();
    },
    updateFile() {
      let _self = this;
      _self.imported_columns = [];
      try {
        let formData = new FormData();
        formData.append("csv_file", this.selected_file);
        axios
          .post("parse_csv", formData, {
            headers: {
              "Content-Type": "multipart/form-data",
            },
          })
          .then(function (response) {
            if (response.data.status) {
              let data = response.data;
              for (let key in data.data) {
                _self.imported_columns.push(data.data[key]);
              }
            }
          });
      } catch (e) {
        console.log(e);
      }
    },
    importData() {
      let _self = this;
      this.import_dailog_loading = "accent";
      this.db_column = this.db_column.map(function (item) {
        item.error = null;
        return item;
      });
      let formData = new FormData();
      formData.append("csv_file", this.selected_file);
      formData.append("bank_id", this.selected_bank);
      formData.append("mapping", JSON.stringify(this.db_column));
      axios
        .post("import_transaction", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then(function (response) {
          if (response.data.status) {
            _self.getTransaction();
            _self.import_dailog_loading = false;
            _self.import_dailog = false;
          } else {
            for (let key in response.data.errors) {
              let obj = _self.db_column.find(
                (o) => o.field == response.data.errors[key].field
              );
              if (obj) {
                obj.error = response.data.errors[key].message;
              }
            }
            _self.import_dailog_loading = false;
          }
        })
        .catch(function () {
          console.log("FAILURE!!");
          _self.import_dailog_loading = false;
        });
    },
    deleteTransactions(){
      let _self = this
      let ids = []
      this.selectedRows.map(function(item){
        ids.push(item.trans_id)
      })
      axios.post("delete_transaction",{
        ids : ids
      }).then(function(){
        _self.getTransaction()
      })
      .catch(function(){
        console.log('FAILURE!!');
      });
    },
    splitBill(){
      let _self = this
      let sData = {}
      this.split_dailog_loading = 'accent';
      this.selectedRows.map(function(item){
        sData.trans_id = item.trans_id
      })
      sData.amount = _self.split_amount
      axios.post("split_transaction",sData).then(function(){
        _self.split_dailog_loading = false;
        _self.getTransaction();
        _self.split_dailog = false;
      })
      .catch(function(){
        console.log('FAILURE!!');
        _self.split_dailog_loading = false;
      });
    },
     deleteInvoices(){
      let _self = this
      let ids = []
      this.hasInvoices.map(function(item){
        ids.push(item.invoice_id)
      })
      axios.post("delete_invoice",{
        ids : ids
      }).then(function(){
        _self.getTransaction()
      })
      .catch(function(){
        console.log('FAILURE!!');
      });
    },
    generateInvoices() {
      let _self = this;
      let ids = [];
      this.selectedRows.map(function (item) {
        ids.push(item.trans_id);
      });
      axios
        .post("generate_invoice", {
          ids: ids,
        })
        .then(function (response) {
          console.log(response);
          _self.getTransaction();
        })
        .catch(function () {
          console.log("FAILURE!!");
        });
    },
    getTransaction() {
      let _self = this;
      this.loading = "accent";
      let url = parseParams(this.pageData);
      axios
        .get("transactions?" + url)
        .then((response) => {
          _self.items = response.data.data;
          _self.loading = false;
        })
        .catch(function (error) {
          console.log(error);
          _self.loading = false;
        });
    },
    getTransactionSelectors() {
      let _self = this;
      axios
        .get("transactions_selectors")
        .then((response) => {
          if (response.data.status) {
            _self.project_list = response.data.data.projects;
            _self.item_list = response.data.data.items;
            _self.vendor_list = response.data.data.vendors;
          }
        })
        .catch(function () {});
    },
    updateTransaction(item) {
      axios
        .post("edit_transaction", item)
        .then((response) => {
          console.log(response);
        })
        .catch(function () {});
    },
  },
  mounted: function () {
    let _self = this;
    this.getTransactionSelectors();
    axios.get("banks").then((response) => {
      _self.banks = response.data;
    });
  },
};
</script>

<style>
</style>