<template>
      <v-container
        fluid
        grid-list-lg
      >
        <v-layout row wrap>
          <v-flex xs8>
            <v-card>
              <v-card-title primary-title>
              <div>
                <div class="headline"> Prestamos Vigentes {{cantidadPrestamos}} </div>
              </div>
            </v-card-title>
              <canvas id="prestamos_tipo" ></canvas>
            </v-card>
          </v-flex>

          <v-flex xs4>
            <v-card >
                <v-list two-line>
                  <template v-for="(item, index) in items">
                    <v-subheader
                      v-if="item.header"
                      :key="item.header"
                    >
                      {{ item.header }}
                    </v-subheader>

                    <v-divider
                      v-else-if="item.divider"
                      :inset="item.inset"
                      :key="index"
                    ></v-divider>

                    <v-list-tile
                      v-else
                      :key="item.title"
                      avatar
                   
                    >
                      <v-list-tile-avatar>
                        <v-icon>{{item.icon}}</v-icon>
                      </v-list-tile-avatar>

                      <v-list-tile-content>
                        <v-list-tile-title v-html="item.title"></v-list-tile-title>
                        <v-list-tile-sub-title v-html="item.subtitle"></v-list-tile-sub-title>
                      </v-list-tile-content>
                    </v-list-tile>
                  </template>
                </v-list>
            </v-card>
          </v-flex>

          <v-flex xs6 >
            <v-card >
              <v-card-title primary-title>
              <div>
                <div class="headline"> Prestamos por Estado </div>
              </div>
            </v-card-title>
              <canvas id="prestamos_estado" ></canvas>
            </v-card>
          </v-flex>
          <v-flex xs6>
            <v-card >
              <v-card-title primary-title>
              <div>
                <div class="headline"> Prestamos por Producto </div>
              </div>
            </v-card-title>
              <canvas id="prestamos_producto" ></canvas>
            </v-card>
          </v-flex>
          <v-flex xs6>
            <v-card >
              <v-card-title primary-title>
              <div>
                <div class="headline"> Prestamos Realizados 2018 </div>
              </div>
            </v-card-title>
              <canvas id="prestamos_mes_2018" ></canvas>
            </v-card>
          </v-flex>
          <v-flex xs6>
            <v-card>
              <v-card-title>
                <div>
                  <div class="headline"> Prestamos Desembolsados 2018 </div>
                </div>
              </v-card-title>
              <canvas id="prestamos_desembolsados_mes_2018" ></canvas>
            </v-card>
          </v-flex>
          <v-flex xs6>
            <v-card >
              <v-card-title primary-title>
              <div>
                <div class="headline"> Prestamos Realizados 2019 </div>
              </div>
            </v-card-title>
              <canvas id="prestamos_mes" ></canvas>
            </v-card>
          </v-flex>
          <v-flex xs6>
            <v-card >
              <v-card-title primary-title>
              <div>
                <div class="headline"> Prestamos Desembolsados 2019

                  <v-btn small @click="download"

                      :disabled="dialog"
                      :loading="dialog"
                      icon
                  ><v-icon color="green">fa-file-excel-o</v-icon>
                  </v-btn>
                   </div> 
                 
                  <v-dialog
                    v-model="dialog"
                    hide-overlay
                    persistent
                    width="300"
                  >
                  <v-card
                  color="primary"
                  dark
                  >
                  <v-card-text>
                      Por favor espere
                      <v-progress-linear
                      indeterminate
                      color="white"
                      class="mb-0"
                      ></v-progress-linear>
                  </v-card-text>
                  </v-card>
                  </v-dialog>
              </div>
            </v-card-title>
              <canvas id="prestamos_desembolsados_mes" ></canvas>
            </v-card>
          </v-flex>
          <v-flex xs12>
            <v-card >
              <v-card-title primary-title>
              <div>
                <div class="headline"> Detalle Desembolsos 2019 </div>
              </div>
            </v-card-title>
            <v-card-text>
              <v-layout row wrap>
              <v-flex xs12>
                <v-tabs
                  slot="extension"
                  v-model="tab"
      
                  grow
                >
                  <v-tabs-slider ></v-tabs-slider>

                  <v-tab
                    v-for="(item, index) in productos_mes"
                    :key="index"
                    @click="graficarProducto(item)"
                  >
                    {{ getMonth(item.mes)}}
                  </v-tab>
                </v-tabs>
              </v-flex>
              <v-flex xs6>
                <v-card>
                  <v-card-title>
                      <div class="headline"> Sector Activo </div>
                  </v-card-title>
                  <canvas id="productos_activo"  ></canvas>
                </v-card>
              </v-flex>
              <v-flex xs6>
                <v-card>
                  <v-card-title>
                      <div class="headline"> Sector Pasivo </div>
                  </v-card-title>
                   <canvas id="productos_pasivo" ></canvas>
                </v-card>
              </v-flex>
              </v-layout>
            </v-card-text>
            </v-card>
          </v-flex>
        </v-layout>
         
      </v-container>
  

</template>
<script>
var chartActivo;
var chartPasivo;
export default {
  data(){
    return {
        prestamosTipo:null,
        prestamosProducto:null,
        prestamosVigentes:null,
        prestamosEstado:null,
        prestamosMes:null,
        prestamosDesembolsados2017:null,
        prestamosMes2017:null,
        prestamosDesembolsados:null,
        productos_mes:null,
        items: [],
        tab:null,
        config_activo:{
          type: null,
          data: null,
          options: null,
        },
        config_pasivo:{
          type: null,
          data: null,
          options: null,
        },
        dialog: false,
        loading: true,

      }
    }
  ,
  methods:{
    createChart(chartId, chartData) {
      const ctx = document.getElementById(chartId);
      const myChart = new Chart(ctx, {
        type: chartData.type,
        data: chartData.data,
        options: chartData.options,
      });
    },
    prestamosRender()
    {
      let labels = [];
      let data =[];
      let total;
      this.prestamosTipo.forEach(item => {
        labels.push(item.nombre);
        data.push(item.cantidad);
        total+=item.cantidad;
      });
      let datasets = [{
        label: 'Total '+total,
        data: data,
        backgroundColor: [
            'rgba(255, 206, 86, 1)',
            'rgba(54, 162, 235, 1)',
        ],
        borderWidth: 1
      }];
      // console.log({labels,datasets}); 
      return {type: 'pie',data:{labels, datasets}};
    },
    download: function (event) {
            // `this` inside methods point to the Vue instance
          self = this;
          self.dialog = true
          //  self.dialog = true;
          // let parameters = this.getParams();
          // parameters.excel =true;
          // console.log(parameters);
          axios({
              url: '/api/export_loans',
              method: 'GET',
              // params: parameters,
              responseType: 'blob', // important
          }).then((response) => {
              const url = window.URL.createObjectURL(new Blob([response.data]));
              const link = document.createElement('a');
              link.href = url;
              link.setAttribute('download', 'Prestamos'+moment().format()+'.xls');
              document.body.appendChild(link);
              link.click();
              self.dialog = false;
          });
      },
    productData(){
      let labels = [];
      let data =[];
      this.prestamosProducto.forEach(item => {
        labels.push(item.nombre.trim());
        data.push(item.cantidad);
      });
      let datasets = [{
        label: 'Vigentes' ,
        data: data,
        backgroundColor: [
        "#f1c40f",
        "#e74c3c",
        "#3498db",
        "#2ecc71",
        "#9b59b6",
        "#34495e",
        "#95a5a6",
        "#FF6384",
        "#1AB394",
        "#FFA365",

        ],
        hoverBackgroundColor: [
        "#f1c40f",
        "#e74c3c",
        "#3498db",
        "#2ecc71",
        "#9b59b6",
        "#34495e",
        "#95a5a6",
        "#FF6384",
        "#1AB394",
        "#FFA365" 
        ],
        borderWidth: 1
      }];
      return {type: 'pie',data:{labels, datasets}};
    },
    prestamosMeses(){
      let labels = [];
      let data =[];
      this.prestamosMes.forEach(item => {
        labels.push(this.getMonth(item.name));
        data.push(item.cantidad);
      });
      let datasets = [{
        label: '' ,
        data: data,
        backgroundColor: [
        "#f1c40f",
        "#e74c3c",
        "#3498db",
        "#2ecc71",
        "#9b59b6",
        "#34495e",
        "#95a5a6",
        "#FF6384",
        "#1AB394",
        "#FFA365",

        ],
        hoverBackgroundColor: [
        "#f1c40f",
        "#e74c3c",
        "#3498db",
        "#2ecc71",
        "#9b59b6",
        "#34495e",
        "#95a5a6",
        "#FF6384",
        "#1AB394",
        "#FFA365" 
        ],
        borderWidth: 1
      }];
      return {type: 'bar',data:{labels, datasets}};
    },
    prestamosMeses2017(){
      let labels = [];
      let data =[];
      this.prestamosMes2017.forEach(item => {
        labels.push(this.getMonth(item.name));
        data.push(item.cantidad);
      });
      let datasets = [{
        label: '' ,
        data: data,
        backgroundColor: [
        "#f1c40f",
        "#e74c3c",
        "#3498db",
        "#2ecc71",
        "#9b59b6",
        "#34495e",
        "#95a5a6",
        "#FF6384",
        "#1AB394",
        "#FFA365",

        ],
        hoverBackgroundColor: [
        "#f1c40f",
        "#e74c3c",
        "#3498db",
        "#2ecc71",
        "#9b59b6",
        "#34495e",
        "#95a5a6",
        "#FF6384",
        "#1AB394",
        "#FFA365" 
        ],
        borderWidth: 1
      }];
      return {type: 'bar',data:{labels, datasets}};
    },
    graficarProducto(item){
      console.log(item);

      let labels = [];
      let data =[];
      let cost=[];
      item.productos_activo.forEach(item => {
        labels.push(item.nombre.trim());
        data.push(item.cantidad);
        cost.push(item.monto);
      });
      let datasets = [{
        label: 'Activo',
        data: data,
        cost: cost,
        backgroundColor: [
        "#f1c40f",
        "#e74c3c",
        "#3498db",
        "#2ecc71",
        "#9b59b6",
        "#34495e",
        "#95a5a6",
        "#FF6384",
        "#1AB394",
        "#FFA365",

        ],
        hoverBackgroundColor: [
        "#f1c40f",
        "#e74c3c",
        "#3498db",
        "#2ecc71",
        "#9b59b6",
        "#34495e",
        "#95a5a6",
        "#FF6384",
        "#1AB394",
        "#FFA365" 
        ],
        borderWidth: 1
      }];
    
      this.config_activo.data = {labels, datasets};
      chartActivo.update();
      // console.log('render config activo');
      // console.log(this.config_activo);
      labels = [];
      data= [];
      cost=[];
      item.productos_pasivo.forEach(item => {
        labels.push(item.nombre.trim());
        data.push(item.cantidad);
        cost.push(item.monto);
      });
      
      datasets = [{
        label: 'Pasivo',
        data: data,
        cost: cost,
        backgroundColor: [
        "#f1c40f",
        "#e74c3c",
        "#3498db",
        "#2ecc71",
        "#9b59b6",
        "#34495e",
        "#95a5a6",
        "#FF6384",
        "#1AB394",
        "#FFA365",

        ],
        hoverBackgroundColor: [
        "#f1c40f",
        "#e74c3c",
        "#3498db",
        "#2ecc71",
        "#9b59b6",
        "#34495e",
        "#95a5a6",
        "#FF6384",
        "#1AB394",
        "#FFA365" 
        ],
        borderWidth: 1
      }];
      this.config_pasivo.data = {labels, datasets};
      chartPasivo.update();
      // this.createChart('productos_activo',this.getProducts('Activo',item.productos_activo));
      // this.createChart('productos_pasivo',this.getProducts('Pasivo',item.productos_pasivo));
      // this.createChart('prestamos_tipo',this.prestamosRender());
    },

    prestamosDesembolsadosMes(){
      let labels = [];
      let data =[];
      let cost=[]; 
      this.prestamosDesembolsados.forEach(item => {
        labels.push(this.getMonth(item.mes));
        data.push(item.cantidad);
        cost.push(item.monto);
      });
      let datasets = [{
        label: '' ,
        data: data,
        cost: cost,
        backgroundColor: [
        "#f1c41f",
        "#e74c3c",
        "#3498db",
        "#2ecc71",
        "#9b59b6",
        "#34495e",
        "#95a5a6",
        "#FF6384",
        "#1AB394",
        "#FFA365",

        ],
        hoverBackgroundColor: [
        "#f1c40f",
        "#e74c3c",
        "#3498db",
        "#2ecc71",
        "#9b59b6",
        "#34495e",
        "#95a5a6",
        "#FF6384",
        "#1AB394",
        "#FFA365" 
        ],
        borderWidth: 1
      }];
      
      return {type: 'bar',data:{labels, datasets},options:{tooltips: {
            callbacks: {
              title: function(tooltipItem, data) {
                return data['labels'][tooltipItem[0]['index']];
              },
              label: function(tooltipItem, data) {
                return data['datasets'][0]['data'][tooltipItem['index']];
              },
              afterLabel: function(tooltipItem, data) {
         
                var cost =  data['datasets'][0]['cost'][tooltipItem['index']];
                return 'Bs '+cost;
              }
            },
            
            displayColors: true
          }} };
    },
    prestamosDesembolsadosMes2017(){
      let labels = [];
      let data =[];
      let cost=[]; 
      // console.log(this.prestamosDesembolsadosMes2017);
      this.prestamosDesembolsados2017.forEach(item => {
        labels.push(this.getMonth(item.mes));
        data.push(item.cantidad);
        cost.push(item.monto);
      });
      let datasets = [{
        label: '' ,
        data: data,
        cost: cost,
        backgroundColor: [
        "#f1c40f",
        "#e74c3c",
        "#3498db",
        "#2ecc71",
        "#9b59b6",
        "#34495e",
        "#95a5a6",
        "#FF6384",
        "#1AB394",
        "#FFA365",

        ],
        hoverBackgroundColor: [
        "#f1c40f",
        "#e74c3c",
        "#3498db",
        "#2ecc71",
        "#9b59b6",
        "#34495e",
        "#95a5a6",
        "#FF6384",
        "#1AB394",
        "#FFA365" 
        ],
        borderWidth: 1
      }];
      
      return {type: 'bar',data:{labels, datasets},options:{tooltips: {
            callbacks: {
              title: function(tooltipItem, data) {
                return data['labels'][tooltipItem[0]['index']];
              },
              label: function(tooltipItem, data) {
                return data['datasets'][0]['data'][tooltipItem['index']];
              },
              afterLabel: function(tooltipItem, data) {
         
                var cost =  data['datasets'][0]['cost'][tooltipItem['index']];
                return 'Bs '+cost;
              }
            },
            
            displayColors: true
          }} };
    },
    productState(){
      let labels = [];
      let data =[];
      this.prestamosEstado.forEach(item => {
        labels.push(item.nombre.trim());
        data.push(item.cantidad);
      });
      let datasets = [{
        label: 'Prestamos Estado' ,
        data: data,
        backgroundColor: [        
        "#e74c3c",
        "#9b59b6",
        "#34495e",
        "#3498db",
        "#2ecc71",
        "#95a5a6",
        ],
        hoverBackgroundColor: [
        "#e74c3c",
        "#9b59b6",
        "#34495e",
        "#3498db",
        "#2ecc71",
        "#95a5a6", 
        ],
        borderWidth: 1
      }];
      return {type: 'pie',data:{labels, datasets}};
    },
    getData(url,parameters){
        return new Promise((resolve,reject)=>{
            this.loading = true;
            axios.get(url)
                .then((response) => {
                    this.loading = false;
                    resolve(response.data);
                });
        });
    },
    load(){
          
        return new Promise((resolve,reject)=>{   
            this.getData('api/reporte').then((data)=>{
                
                this.prestamosTipo = data.prestamos_tipo;
                this.prestamosProducto = data.prestamos_producto;
                this.prestamosVigentes = data.prestamos_vigentes;
                this.prestamosEstado = data.prestamos_estado;
                this.prestamosMes = data.prestamos_mes;
                this.prestamosDesembolsados = data.prestamos_desembolsados;
                this.prestamosMes2018 = data.prestamos_mes_2018;
                this.prestamosDesembolsados2018 = data.prestamos_desembolsados_2018;
                this.productos_mes = data.productos_mes;
                console.log(this.prestamosDesembolsados2018);
                resolve();
            });
        });
    },
    getMonth(number){
      let name='';
      switch(number){
        case 1:
            name = 'Enero';
            break; 
        case 2:
            name = 'Febrero';
            break; 
        case 3:
            name = 'Marzo';
            break; 
        case 4:
            name = 'Abril';
            break; 
        case 5:
            name = 'Mayo';
            break; 
        case 6:
            name = 'Junio';
            break; 
        case 7:
            name = 'Julio';
            break; 
        case 8:
            name = 'Agosto';
            break; 
        case 9:
            name = 'Septiembre';
            break; 
        case 10:
            name = 'Octubre';
            break; 
        case 11:
            name = 'Noviembre';
            break; 
        case 12:
            name = 'Diciembre';
            break; 
      }
      return name;
    }
    
  },
  computed:{
    cantidadPrestamos(){
      let total=0;
      if(this.prestamosVigentes){
        total = this.prestamosVigentes.cantidad;
      }
      return numeral(total).format('0,0.00');
    },
    totalPrestamos(){
      let total=0;//Bs 326,594,044.24
      if(this.prestamosVigentes)
      {
        // this.prestamosTipo.forEach(item=>{ total+=item.sub_total });
        total = this.prestamosVigentes.total;
      }
      return numeral(total).format('0,0.00');
    },
    getAmountActivo(){
      let amount=0;

      if(this.prestamosTipo)
      {
        amount= this.prestamosTipo[1].sub_total;
      }
      return numeral(amount).format('0,0.00');
    },
    getAmountPasivo(){
      let amount=0;

      if(this.prestamosTipo)
      {
        amount= this.prestamosTipo[0].sub_total;
      }
      return numeral(amount).format('0,0.00');
    }
  },
  mounted(){
    
    this.load().then(()=>{
        this.createChart('prestamos_tipo',this.prestamosRender());
        this.createChart('prestamos_producto',this.productData());
        this.createChart('prestamos_estado',this.productState());
        this.createChart('prestamos_mes',this.prestamosMeses());//2018
        this.createChart('prestamos_mes_2017',this.prestamosMeses2017());//2018
        this.createChart('prestamos_desembolsados_mes',this.prestamosDesembolsadosMes());
        this.createChart('prestamos_desembolsados_mes_2017',this.prestamosDesembolsadosMes2017());
        this.items = [
          { header: 'Prestamos Vigentes' },
          {
            icon: 'fa-user',
            title: 'Bs '+this.getAmountActivo,
            subtitle: 'Activo'
          },
          { divider: true, inset: true },
          {
            icon: 'fa-user',
            title: 'Bs '+this.getAmountPasivo,
            subtitle:  'Pasivo'
          },
          { divider: true, inset: true },
          {
            icon: 'fa-money',
            title: 'Bs '+this.totalPrestamos,
            subtitle: 'Total',
          }
        ];
        // this.config = this.getProducts('Activo',item.productos_activo)

        let labels = [];
        let data =[];
        let datasets = [{
          label: 'Activo',
          data: data,
          borderWidth: 1
        }]; 
        this.config_activo = {type: 'doughnut',data:{labels, datasets},options:{tooltips: {
            callbacks: {
              title: function(tooltipItem, data) {
                return data['labels'][tooltipItem[0]['index']];
              },
              label: function(tooltipItem, data) {
                return data['datasets'][0]['data'][tooltipItem['index']];
              },
              afterLabel: function(tooltipItem, data) {
         
                var cost =  data['datasets'][0]['cost'][tooltipItem['index']];
                return 'Bs '+cost;
              }
            },
            
            displayColors: true
          }} }
        const ctx = document.getElementById("productos_activo");
        chartActivo = new Chart(ctx, this.config_activo);

        this.config_pasivo = {type: 'doughnut',data:{labels, datasets},options:{tooltips: {
            callbacks: {
              title: function(tooltipItem, data) {
                return data['labels'][tooltipItem[0]['index']];
              },
              label: function(tooltipItem, data) {
                return data['datasets'][0]['data'][tooltipItem['index']];
              },
              afterLabel: function(tooltipItem, data) {
         
                var cost =  data['datasets'][0]['cost'][tooltipItem['index']];
                return 'Bs '+cost;
              }
            },
            
            displayColors: true
          }} }
        const ctx_p = document.getElementById("productos_pasivo");
        chartPasivo = new Chart(ctx_p, this.config_pasivo);

    });

    // console.log( moment().months(1));
  }
}
</script>
