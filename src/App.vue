<template>
  <div id="report">
    <h1>Редактирование отчета о заказах</h1>
    <h2>Информация о документах</h2>
    <h3>Юрлицо</h3>
    <select name="companyName" id="compName">
      <option :value="companyName">{{companyName}}</option>
    </select>
    <h3>Начало периода</h3>
    <span>{{reportData.start_date}}</span>
    <hr>
    <h3>Конец периода</h3>
    <span>{{reportData.end_date}}</span>
    <hr>
    <h3>Номер документа</h3>
    <span>{{reportData.doc_number}}</span>
    <hr>
    <h2>Информация о заказах</h2>
    <table v-for="(item, index) in reportData.fee_data.venue_summaries" :key="index">
      <tr>
        <th>Точка</th>
        <th>Способ оплаты</th>
        <th>Количество реализованных операций</th>
        <th>Общая сумма реализованных операций</th>
        <th>Вознаграждение исполнителя</th>
        <th>Абонентская плата</th>
      </tr>
    

      <tr >
        <td rowspan="3">{{item.venue_name}}</td>
        <td>Наличный расчет</td>
        <td>
          <input v-model.number="item.payment_type_summaries[0].order_count" type="number">
        </td>    
        <td>
          <input v-model.number="item.payment_type_summaries[0].order_sum" type="number">
        </td>    
        <td>
          <input v-model.number="item.payment_type_summaries[0].order_fee" type="number">
        </td>    
        <td rowspan="3">{{reportData.fee_data.company_summary.company_fee}}</td>        
      </tr>
      <tr>        
        <td>Безналичный расчет</td>
        <td>
          <input v-model.number="item.payment_type_summaries[1].order_count" type="number">
        </td>    
        <td>
          <input v-model.number="item.payment_type_summaries[1].order_sum" type="number">
        </td>    
        <td>
          <input v-model.number="item.payment_type_summaries[1].order_fee" type="number">
        </td>            
      </tr>
      <tr>        
        <td>Картой курьеру</td>
        <td>
          <input v-model.number="item.payment_type_summaries[2].order_count" type="number">
        </td>    
        <td>
          <input v-model.number="item.payment_type_summaries[2].order_sum" type="number">
        </td>    
        <td>
          <input v-model.number="item.payment_type_summaries[2].order_fee" type="number">
        </td>            
      </tr>
    </table>
    <h2>Итоги</h2>
    <table>
      <tr>
        <th></th>
        <th>Способ оплаты</th>
        <th>Количество реализованных операций</th>
        <th>Общая сумма реализованных операций</th>
        <th>Вознаграждение исполнителя</th>
        <th>Абонентская плата</th>
      </tr>
      <tr v-for="(item, index) in reportData.fee_data.payment_type_summaries" :key="index">
        <td v-if="index === 0" rowspan="3">Итого:</td>
        <td v-if="index === 0">Наличный расчет</td>
        <td v-else-if="index === 1">Безналичный расчет</td>
        <td v-else>Картой курьеру</td>
        <td>{{item.order_count}}</td>    
        <td>{{item.order_sum}}</td>    
        <td>{{item.order_fee}}</td>    
        <td v-if="index === 0" rowspan="3">{{reportData.fee_data.company_summary.company_fee}}</td>        
      </tr>     
      <tr>
        <td colspan="2">Итого:</td>
        <td>{{reportData.fee_data.company_summary.order_count}}</td>
        <td>{{reportData.fee_data.company_summary.order_sum}}</td>
        <td>{{reportData.fee_data.company_summary.order_fee}}</td>
        <td>{{reportData.fee_data.company_summary.company_fee}}</td>
      </tr>
   
    </table>

    <button id="saveReport" @click.prevent="sendReport">Сохранить</button>
    
  </div>
</template>

<script>

import axios from 'axios';
import data from './report'

export default {
  data() {
    return {
      reportData: data,
      allOrdersCash: 0,
      allOrdersSum: 0,
      allOrdersFee: 0,
      companyName: '' 
    };
  },
  updated() {
    this.allOrdersCash = 0
    this.allOrdersSum = 0
    this.allOrdersFee = 0

    this.reportData.fee_data.payment_type_summaries[0].order_count = this.reportData.fee_data.venue_summaries[0].payment_type_summaries[0].order_count
    this.reportData.fee_data.payment_type_summaries[0].order_sum = this.reportData.fee_data.venue_summaries[0].payment_type_summaries[0].order_sum
    this.reportData.fee_data.payment_type_summaries[0].order_fee = this.reportData.fee_data.venue_summaries[0].payment_type_summaries[0].order_fee

    this.reportData.fee_data.payment_type_summaries[1].order_count = this.reportData.fee_data.venue_summaries[0].payment_type_summaries[1].order_count
    this.reportData.fee_data.payment_type_summaries[1].order_sum = this.reportData.fee_data.venue_summaries[0].payment_type_summaries[1].order_sum
    this.reportData.fee_data.payment_type_summaries[1].order_fee = this.reportData.fee_data.venue_summaries[0].payment_type_summaries[1].order_fee

    this.reportData.fee_data.payment_type_summaries[2].order_count = this.reportData.fee_data.venue_summaries[0].payment_type_summaries[2].order_count
    this.reportData.fee_data.payment_type_summaries[2].order_sum = this.reportData.fee_data.venue_summaries[0].payment_type_summaries[2].order_sum
    this.reportData.fee_data.payment_type_summaries[2].order_fee = this.reportData.fee_data.venue_summaries[0].payment_type_summaries[2].order_fee

    this.reportData.fee_data.payment_type_summaries.forEach(element => {
      if (element.order_count) {
        this.allOrdersCash += element.order_count
      }
      if (element.order_sum) {
        this.allOrdersSum += element.order_sum
      }
      if (element.order_fee) {
        this.allOrdersFee += element.order_fee
      }
    })

    this.reportData.fee_data.company_summary.order_count = this.allOrdersCash
    this.reportData.fee_data.company_summary.order_sum = this.allOrdersSum
    this.reportData.fee_data.company_summary.order_fee = this.allOrdersFee
    
    
  },
  mounted() {    
    let companyNameRequest = this.reportData.legal_id
    axios
        .post('server2', companyNameRequest)
        .then((response) => {
          this.companyName = response
        })
        .catch((error) => {
          console.log(error);
        });    
  },
  methods: {
    sendReport() {
      let report = JSON.stringify(this.reportData)
      axios
        .post('server', report)
        .then((response) => {
          console.log(response)          
        })
        .catch((error) => {
          console.log(error);
        });
    }      
  }, 
  components: {
  }
};
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Roboto&display=swap');
body {
  font-family: 'Roboto', sans-serif;
}
#report {
  position: relative;
  left: 50%;
  width: 50%;
  transform: translateX(-50%)
}
table {
  border-collapse: collapse;
}
td {
  text-align: center;
}
td, th {
  border: 1px solid black;
}
th {
  background-color: #9e9e9a;
}
#saveReport {
  margin-top: 20px;
  width: 100px;
  height: 40px;
  background-color: #77bce7;
  border: none;
  cursor: pointer;
}

</style>
