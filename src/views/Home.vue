<template>
  <div class="home">
    <h3 class="pt-3">บันทึกรายรับ-รายจ่าย</h3>
    <div class="box">
      <div>
        <b-form inline>
          <label style="margin-left: 15%">รายการ </label>
          <b-form-input
            class="subject-box"
            v-model="subject"
            type="text"
          ></b-form-input>
          <b-form-group>
            <b-form-radio-group style="margin-left: 150px">
              <b-form-radio v-model="type" name="some-radios" value="revenue"
                >รายรับ</b-form-radio
              >
              <b-form-radio v-model="type" name="some-radios" value="expenses"
                >รายจ่าย</b-form-radio
              >
            </b-form-radio-group>
          </b-form-group>
        </b-form>
        <b-row class="pt-1">
          <b-col sm="3"> <label style="padding-left: 40px">จำนวน </label></b-col
          ><b-col sm="4">
            <b-form-input
              v-model="count"
              class="num-box"
              type="number"
            ></b-form-input> </b-col
          ><b-col sm="2">
            <label style="padding-right: 70px">บาท </label></b-col
          >
        </b-row>
        <b-row class="pt-1">
          <b-col sm="3">
            <label style="padding-left: 40px">วันที่ </label></b-col
          ><b-col sm="5">
            <b-form-datepicker v-model="date"></b-form-datepicker>
          </b-col>
        </b-row>
        <div class="mt-3 pb-3">
          <b-button
            class="mr-1"
            style="background-color: green"
            @click="save()"
            :disabled="subject == '' || type == 0 || count == 0 || date == ''"
            >บันทึก</b-button
          ><b-button style="background-color: red" @click="reset()"
            >รีเซ็ต</b-button
          >
        </div>
      </div>
    </div>
    <b-tabs content-class="mt-3" align="center">
      <b-tab title="รายการบันทึก" active
        ><div>
          <h4>ตารางบันทึกรายรับ-รายจ่าย</h4>
          <p>ยอดเงินคงเหลือ : {{ this.total }}</p>
          <b-table
            sortBy="วันที่"
            sortDesc
            striped
            hover
            :items="jsonData"
          ></b-table></div
      ></b-tab>
      <b-tab title="กราฟ">
        <graph-bar
          :width="600"
          :height="400"
          :axis-min="0"
          :axis-max="50000"
          :labels="['ยอดรวม']"
          :values="values"
        >
          <note :text="'Bar Chart ของรายรับเทียบกับรายจ่าย'"></note>
          <legends :names="names" :filter="true"></legends>
        </graph-bar>
      </b-tab>
    </b-tabs>
  </div>
</template>

<script>
import json from "../json/data.json";
// @ is an alias to /src

export default {
  name: "Home",
  components: {},
  data() {
    return {
      names: ["รายรับ", "รายจ่าย"],
      values: [[this.reTotal], [this.exTotal]],
      subject: "",
      type: 0,
      jsonData: json.data,
      count: 0,
      date: "",
      total: 0,
      exTotal: 0,
      reTotal: 0,
    };
  },
  methods: {
    reset() {
      (this.subject = ""), (this.type = 0), (this.count = 0), (this.date = "");
    },
    save() {
      var saveType;
      if (this.type == "revenue") {
        this.saveType = "รายรับ";
      } else if (this.type == "expenses") {
        this.saveType = "รายจ่าย";
      }
      var saveData = {
        วันที่: this.date,
        ประเภท: this.saveType,
        รายการ: this.subject,
        จำนวนเงิน: this.count,
      };
      this.jsonData.push(saveData);
      this.calculateTotal();
      this.reset();
    },
    calculateTotal() {
      this.total = 0;
      this.exTotal = 0;
      this.reTotal = 0;
      for (var i = 0; i < this.jsonData.length; i++) {
        if (this.jsonData[i].ประเภท == "รายจ่าย") {
          this.total = this.total - parseInt(this.jsonData[i].จำนวนเงิน);
          this.exTotal = parseInt(this.jsonData[i].จำนวนเงิน) + this.exTotal;
        } else {
          this.total = this.total + parseInt(this.jsonData[i].จำนวนเงิน);
          this.reTotal = this.reTotal + parseInt(this.jsonData[i].จำนวนเงิน);
        }
      }
      this.values = [[this.reTotal], [this.exTotal]];
    },
  },
  mounted() {
    this.calculateTotal();
  },
};
</script>
<style>
.pagination-box {
  width: 150px;
}
.num-box {
  margin-top: 3px;
  height: 70% !important;
}
.subject-box {
  width: 60% !important;
  height: 10% !important;
  margin: 10px;
  background-color: #cfe1ff !important;
}
.box {
  margin-top: 20px;
  width: 30%;
  margin-left: 35%;
  /* height: 200px; */
  border: 1px solid black;
  margin-bottom: 50px;
}
</style>