<template>
  <div class="page-container">
    <md-app md-waterfall md-mode="overlap">
      <md-app-toolbar class="md-large">
        <div class="md-toolbar-row">
          <span class="md-title">
            <img
              alt="Vue logo"
              :src="
                isDefaultImage
                  ? 'https://billte.ch/wp-content/uploads/2019/12/logo-white.png'
                  : 'https://billte.ch/wp-content/uploads/2019/12/logo-orange.png'
              "
              width="auto"
          /></span>
        </div>
      </md-app-toolbar>

      <md-app-content>
        <div>
          <form novalidate class="md-layout" @submit.prevent="validateUser">
            <md-card class="md-layout-item md-size-50 md-small-size-100">
              <md-card-header>
                <div class="md-title"></div>
              </md-card-header>

              <md-card-header>
                <div class="md-title">Invoice Receiver</div>
              </md-card-header>
              <md-card-content>
                <div class="md-layout md-gutter">
                  <div class="md-layout-item md-small-size-100">
                    <md-field :class="getValidationClass('firstName')">
                      <label for="first-name">First Name</label>
                      <md-input
                        name="first-name"
                        id="first-name"
                        autocomplete="given-name"
                        v-model="form.firstName"
                        :disabled="sending"
                      />
                      <span class="md-error" v-if="!$v.form.firstName.required"
                        >The first name is required</span
                      >
                      <span
                        class="md-error"
                        v-else-if="!$v.form.firstName.minlength"
                        >Invalid first name</span
                      >
                    </md-field>
                  </div>

                  <div class="md-layout-item md-small-size-100">
                    <md-field :class="getValidationClass('lastName')">
                      <label for="last-name">Last Name</label>
                      <md-input
                        name="last-name"
                        id="last-name"
                        autocomplete="family-name"
                        v-model="form.lastName"
                        :disabled="sending"
                      />
                      <span class="md-error" v-if="!$v.form.lastName.required"
                        >The last name is required</span
                      >
                      <span
                        class="md-error"
                        v-else-if="!$v.form.lastName.minlength"
                        >Invalid last name</span
                      >
                    </md-field>
                  </div>
                </div>

                <div class="md-layout md-gutter">
                  <div class="md-layout-item md-small-size-100">
                    <md-field>
                      <label>Company Name</label>
                      <md-input
                        name="company"
                        id="company"
                        autocomplete="given-name"
                        v-model="form.company"
                        :disabled="sending"
                      />
                    </md-field>
                  </div>
                </div>

                <md-field :class="getValidationClass('email')">
                  <label for="email">Email</label>
                  <md-input
                    type="email"
                    name="email"
                    id="email"
                    autocomplete="email"
                    v-model="form.email"
                    :disabled="sending"
                  />
                  <span class="md-error" v-if="!$v.form.email.required"
                    >The email is required</span
                  >
                  <span class="md-error" v-else-if="!$v.form.email.email"
                    >Invalid email</span
                  >
                </md-field>
                <div class="md-layout-item md-small-size-100">
                  <md-field :class="getValidationClass('city')">
                    <label for="city">City</label>
                    <md-input
                      type="number"
                      id="city"
                      name="city"
                      autocomplete="city"
                      v-model="form.city"
                      :disabled="sending"
                    />
                    <span class="md-error" v-if="!$v.form.city.required"
                      >The city is required</span
                    >
                    <span class="md-error" v-else-if="!$v.form.city.maxlength"
                      >Invalid city</span
                    >
                  </md-field>
                  <md-autocomplete
                    v-model="selectedCountry"
                    :md-options="countries"
                  >
                    <label>Country</label>
                  </md-autocomplete>
                </div>
              </md-card-content>

              <md-card-header>
                <div class="md-title">Invoice Details</div>
              </md-card-header>

              <md-card-content>
                <md-field>
                  <label>PO Number</label>
                  <md-input
                    name="POnumber"
                    id="POnumber"
                    v-model="form.POnumber"
                    :disabled="sending"
                  />
                </md-field>

                <div class="md-layout md-gutter">
                  <div class="md-layout-item md-small-size-100 label">
                    <span class="lables"> Invoice Date </span>

                    <md-field>
                      <input class="datePicker" type="date" v-model="date" />
                    </md-field>
                  </div>
                  <div class="md-layout-item md-small-size-100 label">
                    <span class="lables">Due Date</span>
                    <md-field class="datePicker">
                      <input class="datePicker" type="date" v-model="date" />
                    </md-field>
                  </div>
                </div>

                <span class="lables">Invoice Number</span>

                <md-field>
                  <label>INVC000001</label>
                  <md-input v-model="disabled" disabled></md-input>
                </md-field>

                <div class="md-layout md-gutter">
                  <div class="md-layout-item md-small-size-100">
                    <md-field>
                      <label for="currency">Currency</label>
                      <md-select
                        v-model="currency"
                        name="currency"
                        id="currency"
                      >
                        <md-option value="currency">CHF</md-option>
                      </md-select>
                    </md-field>
                  </div>
                  <div class="md-layout-item md-small-size-100">
                    <md-field>
                      <label for="status">Status</label>
                      <md-select v-model="status" name="status" id="status">
                        <md-option value="paid">PAID</md-option>
                        <md-option value="unpaid">UNPAID</md-option>
                      </md-select>
                    </md-field>
                  </div>
                </div>

                <md-field>
                  <label>Logo</label>
                  <md-file v-model="image" accept="image/*" />
                </md-field>
              </md-card-content>

              <md-card-header>
                <div class="md-title">Product Details</div>
              </md-card-header>

              <md-card-content>
                <md-field>
                  <label>Product / Service</label>
                  <md-input
                    name="ProductOrService"
                    id="ProductOrService"
                    v-model="form.ProductOrService"
                    :disabled="sending"
                  />
                </md-field>
                <md-field>
                  <label>Description</label>
                  <md-input
                    name="Description"
                    id="Description"
                    v-model="form.Description"
                    :disabled="sending"
                  />
                </md-field>

                <md-field>
                  <label>Unit Cost</label>
                  <md-input
                    name="unitCost"
                    id="unitCost"
                    v-model="form.unitCost"
                    :disabled="sending"
                  />
                </md-field>

                <md-field>
                  <label>Discount</label>
                  <md-input
                    name="discount"
                    id="discount"
                    v-model="form.discount"
                    :disabled="sending"
                  />

                  <md-select v-model="discount" name="discount" id="discount">
                    <md-option value="paid">CHF</md-option>
                    <md-option value="unpaid">%</md-option>
                  </md-select>
                </md-field>
                <md-field>
                  <label>VAT</label>

                  <md-select v-model="vat" name="vat" id="vat">
                    <md-option value="vatApplied">0.0%</md-option>
                    <md-option value="vatApplied">2.5%</md-option>
                    <md-option value="vatApplied">3.7%</md-option>
                    <md-option value="vatApplied">7.7%</md-option>
                  </md-select>
                </md-field>

                <div class="form-group" v-for="(input, k) in inputs" :key="k">
                  <md-field>
                    <label>Product / Service</label>
                    <md-input
                      name="ProductOrService"
                      id="ProductOrService"
                      v-model="input.productOrService"
                      :disabled="sending"
                    />
                  </md-field>
                  <md-field>
                    <label>Description</label>
                    <md-input
                      name="Description"
                      id="Description"
                      v-model="input.description"
                      :disabled="sending"
                    />
                  </md-field>

                  <md-field>
                    <label>Unit Cost</label>
                    <md-input
                      name="unitCost"
                      id="unitCost"
                      v-model="input.unitCost"
                      :disabled="sending"
                    />
                  </md-field>

                  <md-field>
                    <label>Discount</label>
                    <md-input
                      name="discount"
                      id="discount"
                      v-model="input.discount"
                      :disabled="sending"
                    />

                    <md-select
                      v-model="input.discount"
                      name="discount"
                      id="discount"
                    >
                      <md-option value="paid">CHF</md-option>
                      <md-option value="unpaid">%</md-option>
                    </md-select>
                  </md-field>
                  <md-field>
                    <label>VAT</label>

                    <md-select v-model="input.vat" name="vat" id="vat">
                      <md-option value="vatApplied">0.0%</md-option>
                      <md-option value="vatApplied">2.5%</md-option>
                      <md-option value="vatApplied">3.7%</md-option>
                      <md-option value="vatApplied">7.7%</md-option>
                    </md-select>
                  </md-field>

                  <md-button
                    class="md-fab md-primary"
                    :disabled="sending"
                    @click="add(k)"
                    v-show="k == inputs.length - 1"
                  >
                    <md-icon>add</md-icon>
                  </md-button>

                  <md-button
                    class="md-primary md-raised md-accent"
                    :disabled="sending"
                    @click="remove(k)"
                    v-show="k || (!k && inputs.length > 1)"
                    >Remove Product
                  </md-button>
                </div>

                <br />

                <span class="lables">Total</span>

                <md-field value="{Total Variable After Calculation}">
                </md-field>
              </md-card-content>

              <md-progress-bar md-mode="indeterminate" v-if="sending" />
              <br />
              <md-card-header>
                <div class="md-title">Invoice Totals</div>
              </md-card-header>

              <md-card-content>
                <span class="lables">Total ( Net )</span>

                <md-field value="{Total Variable After Calculation}">
                </md-field>

                <md-field>
                  <label>Discount</label>
                  <md-input
                    name="discount"
                    id="discount"
                    v-model="form.discount"
                    :disabled="sending"
                  />

                  <md-select v-model="discount" name="discount" id="discount">
                    <md-option value="paid">CHF</md-option>
                    <md-option value="unpaid">%</md-option>
                  </md-select>
                </md-field>
                <md-field>
                  <label>VAT</label>

                  <md-select v-model="vat" name="vat" id="vat">
                    <md-option value="vatApplied">0.0%</md-option>
                    <md-option value="vatApplied">2.5%</md-option>
                    <md-option value="vatApplied">3.7%</md-option>
                    <md-option value="vatApplied">7.7%</md-option>
                  </md-select>
                </md-field>

                <br />
              </md-card-content>

              <md-progress-bar md-mode="indeterminate" v-if="sending" />
              <br />

              <md-card-actions>
                <md-button
                  style="margin: 0rem 2rem 2rem 0rem"
                  type="submit"
                  class="md-raised md-primary"
                  :disabled="sending"
                  >Create Invoice</md-button
                >
              </md-card-actions>
            </md-card>

            <md-snackbar :md-active.sync="userSaved"
              >The user {{ lastUser }} was saved with success!</md-snackbar
            >
          </form>
        </div>
      </md-app-content>
    </md-app>
  </div>
</template>

<script>
import { validationMixin } from "vuelidate";
import {
  required,
  email,
  minLength,
  maxLength,
} from "vuelidate/lib/validators";

import Vue from "vue";
import VueMaterial from "vue-material";
import "vue-material/dist/vue-material.min.css";
import "vue-material/dist/theme/default.css";

Vue.use(VueMaterial);

export default {
  name: "FormValidation",
  mixins: [validationMixin],
  // components: { DatePicker },

  data: () => ({
    inputs: [
      {
        name: "",
        party: "",
      },
    ],

    selectedCountry: 'Switzerland',
     countries: [
        'Algeria',
        'Argentina',
        'Brazil',
        'Canada',
        'Italy',
        'Japan',
        'United Kingdom',
        'United States',
        'Switzerland'
      ],

    isDefaultImage: true,
    single: null,
    created() {
      window.addEventListener("scroll", this.handleScroll);
    },
    destroyed() {
      window.removeEventListener("scroll", this.handleScroll);
    },
    menuVisible: false,

    date: new Date().toISOString().slice(0, 10),

    form: {
      firstName: null,
      lastName: null,
      gender: null,
      city: null,
      email: null,
      phone: null,
      company: null,
      POnumber: null,
    },
    userSaved: false,
    sending: false,
    lastUser: null,
  }),

  validations: {
    form: {
      firstName: {
        required,
        minLength: minLength(3),
      },
      lastName: {
        required,
        minLength: minLength(3),
      },
      city: {
        required,
        maxLength: maxLength(3),
      },

      gender: {
        required,
      },
      email: {
        required,
        email,
      },
    },
  },

  methods: {
    add() {
      this.inputs.push({
        productOrService: "",
        description: "",
        discount: "",
        vat: "",
      });
    },

    remove(index) {
      this.inputs.splice(index, 1);
    },

    handleScroll() {
      console.log(window.scrollY);
      if (window.scrollY > 100) {
        return (this.isDefaultImage = false);
      }
      if (window.scrollY <= 100) {
        if (!this.defaultImage) {
          return (this.isDefaultImage = true);
        }
      }
    },
    getValidationClass(fieldName) {
      const field = this.$v.form[fieldName];

      if (field) {
        return {
          "md-invalid": field.$invalid && field.$dirty,
        };
      }
    },
    clearForm() {
      this.$v.$reset();
      this.form.firstName = null;
      this.form.lastName = null;
      this.form.city = null;
      this.form.gender = null;
      this.form.email = null;
    },
    saveUser() {
      this.sending = true;

      // Instead of this timeout, here you can call your API
      window.setTimeout(() => {
        this.lastUser = `${this.form.firstName} ${this.form.lastName}`;
        this.userSaved = true;
        this.sending = false;
        this.clearForm();
      }, 1500);
    },
    validateUser() {
      this.$v.$touch();

      if (!this.$v.$invalid) {
        this.saveUser();
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.md-title {
  font-weight: 900;
  font-size: 3rem;
  line-height: 3rem;
  color: #3e8f92;
}
.md-progress-bar {
  position: absolute;
  right: 0;
  left: 0;
}

.datePicker {
  border: none;
  font-family: "sans-serif";
}

.lables {
  font-size: 1.5rem;
  line-height: 4rem;
  font-weight: 600;
  color: #3e6592;
}
.md-app {
  max-height: 90vh;
  border: 1px solid rgba(#000, 0.12);
  top: 0;
}
.md-app-toolbar {
  background: #db6a3d;
}
</style>
