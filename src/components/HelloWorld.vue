<template>
  <div class="container">

    <div style="display: flex; justify-content: end">
      <b-button  style=" display: block" @click="showForm()">Add client</b-button>

      <b-modal v-model="showModal" size="xl" title="Add client">
        <div class="row">
          <div class="col">
            <label for="firstName">First Name:</label>
            <input type="text" v-model="first_name" class="form-control" id="firstName">
          </div>
          <div class="col">
            <label for="firstName">Last Name:</label>
            <input type="text" v-model="last_name" class="form-control" id="lastName">
          </div>
          <div class="col">
            <label for="firstName">DOB:</label>
            <input type="date" v-model="dob" class="form-control" id="DOB">
          </div>
        </div>
        <div class="row">
          <div class="col">
            <label for="firstName">Email:</label>
            <input type="email" v-model="email" class="form-control" id="email">
          </div>
          <div class="col">
            <label for="firstName">Phone:</label>
            <input type="number" v-model="phone" class="form-control" id="phone">
          </div>
          <div class="col">
            <label for="firstName">Address:</label>
            <input type="text" v-model="address" class="form-control" id="address">
          </div>
        </div>
        <br>
        <div class="row">
          <div class="col">
            <h5>Payments</h5>
          </div>
          <div class="col" style="display: flex; justify-content: end">
            <button :disabled="limitePagos" type="button" ref="btnAddFila" class="btn btn-success"
              @click="agregarFila()">+</button>
          </div>
        </div>
        <div class="row" v-for="(fila, index) in arrayPAyments" :key="index">
          <div class="col">
            <label for="firstName">Transaction Id:</label>
            <input v-model="fila.transaction_id" type="text" class="form-control" id="transactionID">
          </div>
          <div class="col">
            <label for="firstName">Amount:</label>
            <input v-model="fila.payment" type="number" class="form-control" id="amount">
          </div>
          <div class="col">
            <label for="firstName">Date:</label>
            <input v-model="fila.payment_date" type="date" class="form-control" id="date">
          </div>
          <div class="col-1 mt-4">
            <button type="button" class="btn btn-danger btn-sm" @click="eliminarFila(index)">delete</button>
          </div>
        </div>

        <template #modal-footer>
          <div class="w-100">
            <b-button variant="primary" class="float-right" @click="guardar()">
              guardar
            </b-button>
            
          </div>
        </template>

      </b-modal>
    </div>

    <table class="table">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">First Name</th>
          <th scope="col">Last Name</th>
          <th scope="col">dob</th>
          <th scope="col">email</th>
          <th scope="col">phone</th>
          <th scope="col">address</th>
          <th scope="col">payments</th>
          <th scope="col">total payments</th>
          <th scope="col">Actions</th>
        </tr>
      </thead>
      <tbody class="table-group-divider">
        <tr v-for="item in tableData" :key="item.id">
          <th scope="row">{{ item.id }}</th>
          <td>{{ item.first_name }}</td>
          <td>{{ item.last_name }}</td>
          <td>{{ item.dob }}</td>
          <td>{{ item.email }}</td>
          <td>{{ item.phone }}</td>
          <td>{{ item.address }}</td>
          <td>{{ item.payments }}</td>
          <td>{{ item.total_payments }}</td>
          <td>
            <button type="button" class="btn" @click="editClient(item)" >
              <b-icon icon="pencil-fill" scale="2" variant="success" ></b-icon>
            </button>
            <button type="button"  class="btn" @click="deleteClient(item.id)">
              <b-icon icon="trash-fill" scale="2" variant="danger" ></b-icon>
            </button>
          </td>
        </tr>
      </tbody>
    </table>

  </div>
</template>

<script>
import Swal from 'sweetalert2';
import axios from 'axios';

export default {
  name: 'HelloWorld',
  
  data() {
    return {
      tableData: [], // Arreglo para almacenar los datos de la tabla
      id: null,
      first_name: null,
      last_name: null,
      dob: null,
      email: null,
      phone: null,
      address: null,
      arrayPAyments: [],
      paymentDelete: [],
      showModal : false,
      

    };

  },

  computed: {
    limitePagos() {
      return (this.arrayPAyments.length === 5) ? true : false
    },
  },

  mounted() {
    this.fetchData(); // Llama a la función para obtener los datos al cargar el componente
  },
  methods: {
    resetForm() {
      this.first_name = null;
      this.last_name = null;
      this.dob = null;
      this.email = null;
      this.phone = null;
      this.address = null;
      this.arrayPAyments = [];
    },
    showForm() {
      this.resetForm();
      this.showModal = true;
      this.id = null;
    },
    async fetchData() {
      await axios.get('http://127.0.0.1:8000/api/total')
        .then(response => {
          this.tableData = response.data; // Almacena los datos en el arreglo tableData
        })
        .catch(error => {
          console.error('Error al obtener los datos:', error);
        });
    },

    agregarFila() {
      const datos = {
        id : null,
        transaction_id: '',
        payment: '',
        payment_date: '',
      }
      if (this.arrayPAyments.length <= 4) {
        this.arrayPAyments.push(datos)
      }
    },


    eliminarFila(id) {
      const arrayDelete = this.arrayPAyments.splice(id, 1)[0];
      this.paymentDelete.push(arrayDelete);
      console.log(this.paymentDelete);
    },
    async guardar() {
      const misDatos = {
        'id': this.id,
        "firstName": this.first_name,
        "lastName": this.last_name,
        "dob": this.dob,
        "email": this.email, 
        "phone": this.phone,
        "address": this.address,
        arrayPagos: this.arrayPAyments,
        arrayDelete: this.paymentDelete,
      }
      console.log(misDatos);
      const response= await axios
      .post('http://127.0.0.1:8000/api/clientes-insert', misDatos)
     
      if(response.status===200){
        this.fetchData();
        this.showModal=false;
        Swal.fire({
        icon: 'success',
        title: 'Guardado',
        text: 'El cliente se ha guardado exitosamente.'
        });
        }else{
          return 'error';
        }
    },
    editClient(item) {
      // Mostrar la alerta de confirmación
      Swal.fire({
        title: 'Confirmar edición',
        text: '¿Estás seguro de editar este cliente?',
        icon: 'question',
        showCancelButton: true,
        confirmButtonText: 'Sí',
        cancelButtonText: 'No'
      }).then((result) => {
        // Si el usuario hizo clic en "Sí", cargar los datos del cliente en el formulario
        if (result.isConfirmed) {
          this.showModal = true;

          axios.get(`http://127.0.0.1:8000/api/total/${item.id}/details`)
            .then(response => {
              const clientData = response.data;
              this.id = clientData.id;
              this.first_name = clientData.first_name;
              this.last_name = clientData.last_name;
              this.dob = clientData.dob;
              this.email = clientData.email;
              this.phone = clientData.phone;
              this.address = clientData.address;
              this.arrayPAyments = response.data.payments;

              console.log(clientData);
              console.log(this.arrayPAyments);
            })
            .catch(error => {
              console.error('Error al obtener los datos del cliente:', error);
            });
        }
      });
    },


    deleteClient(id) {
    // Mostrar la alerta de confirmación
    Swal.fire({
      title: '¿Estás seguro de eliminar esta fila?',
      text: 'Esta acción eliminará al cliente permanentemente.',
      icon: 'warning',
      showCancelButton: true,
      confirmButtonText: 'Sí',
      cancelButtonText: 'No'
    }).then((result) => {
      // Si el usuario hizo clic en "Sí", eliminar la fila
      if (result.isConfirmed) {
        // Realizar la solicitud DELETE al backend
        axios.delete(`http://127.0.0.1:8000/api/total/${id}/delete-client`)
          .then(response => {
            if (response.status === 200) {
              // Eliminar la fila correspondiente en tableData
              const index = this.tableData.findIndex(item => item.id === id);
              if (index !== -1) {
                this.tableData.splice(index, 1);
              }
              // Mostrar una alerta de éxito
              Swal.fire(
                'Eliminado',
                'El cliente ha sido eliminado correctamente.',
                'success'
              );
            } else {
              console.error('Error al eliminar el cliente');
              // Mostrar una alerta de error
              Swal.fire(
                'Error',
                'Ocurrió un error al eliminar el cliente.',
                'error'
              );
            }
          })
          .catch(error => {
            console.error('Error al eliminar el cliente:', error);
            // Mostrar una alerta de error
            Swal.fire(
              'Error',
              'Ocurrió un error al eliminar el cliente.',
              'error'
            );
          });
      }
    });
  }


    

  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
