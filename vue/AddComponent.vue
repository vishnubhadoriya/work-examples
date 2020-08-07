<template>
  <div class="d-flex">
    <v-card max-width="400" class="mx-auto align-self-stretch" tile :loading="loading">
      <v-card-text class="pb-0">
        <v-container>
          <v-form>
            <v-row dense>
              <v-col cols="6">
                <v-text-field
                  label="Bank Name"
                  outlined
                  dense
                  autofocus
                  v-model="form_data.bank_name"
                  :error-messages="form_error.bank_name"
                  :hide-details="!form_error.bank_name"
                ></v-text-field>
              </v-col>
              <v-col cols="6">
                <v-text-field
                  label="Code"
                  outlined
                  dense
                  v-model="form_data.bank_code"
                  :error-messages="form_error.bank_code"
                  :hide-details="!form_error.bank_code"
                ></v-text-field>
              </v-col>
            </v-row>
            <v-row dense>
              <v-col cols="6">
                <v-text-field
                  label="Acc. Type"
                  outlined
                  dense
                  autofocus
                  v-model="form_data.ac_type"
                  :error-messages="form_error.ac_type"
                  :hide-details="!form_error.ac_type"
                ></v-text-field>
              </v-col>
              <v-col cols="6">
                <v-text-field
                  label="Acc. Group"
                  outlined
                  dense
                  v-model="form_data.ac_group"
                  :error-messages="form_error.ac_group"
                  :hide-details="!form_error.ac_group"
                ></v-text-field>
              </v-col>
            </v-row>
          </v-form>
        </v-container>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn tile color="error" class="mx-5" dark @click="saveAccount" :loading="loading">Save</v-btn>
      </v-card-actions>
    </v-card>
    <v-btn color="error" fixed fab bottom left to="/accounts" small>
      <v-icon>mdi-arrow-left</v-icon>
    </v-btn>
  </div>
</template>

<script>
import axios from "../../plugins/axios";
export default {
  name: "AccountAdd",
  data() {
    return {
      loading: false,
      form_data: {},
      form_error: {},
      p_type: [
        { text: "Fixed", value: "fixed" },
        { text: "Cost Plus", value: "cost_plus" },
        { text: "Project Management", value: "project_management" },
      ],
    };
  },
  methods: {
    saveAccount() {
      this.loading = true;
      this.form_error = {};
      let _self = this;
      let serverData = JSON.parse(JSON.stringify(this._data.form_data));
      axios
        .post("add_account", serverData)
        .then(function (res) {
          let data = res.data;
          if (!data.status) {
            for (let key in data.errors) {
              _self.form_error[key] = data.errors[key][0];
            }
          } else {
            _self.$router.push({ path: "/accounts" });
          }
          _self.loading = false;
        })
        .catch(function (error) {
          console.log("FAILURE!!", error);
          _self.loading = false;
        });
    },
  },
};
</script>

<style>
</style>