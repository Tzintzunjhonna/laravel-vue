<template>
  <div>
    

    <form @submit.prevent="editarNota(nota)" v-if="editarActivo">
        <h4>Editar nota</h4>
        <div class="form-group">
            <input type="text" placeholder="Nombre" class="form-control mb-2" v-model="nota.nombre">
            <input type="text" placeholder="Descripción" class="form-control mb-2" v-model="nota.descripcion">
            <button class="btn btn-success" type="submit">Guardar</button>
            <button class="btn btn-danger" type="submit" @click="cancelarEdicion()">Cancelar</button>
        </div>
    </form>

    <form @submit.prevent="agregar" v-else>
        <h4>Agregar notas</h4>
        <div class="form-group">
            <input type="text" placeholder="Nombre" class="form-control mb-2" v-model="nota.nombre">
            <input type="text" placeholder="Descripción" class="form-control mb-2" v-model="nota.descripcion">
            <button class="btn btn-primary" type="submit">Agregar</button>
        </div>
    </form>
    <hr class="mt-3">

    <h4>Listado de notas</h4>
    
    <table class="table m-0 table-colored-bordered table-bordered-success">

        <thead class="thead-ligth">
            <tr>
                <th class="text-center">Nombre</th>
                <th class="text-center">Descripción</th>
                <th class="text-center">Fecha</th>
                <th class="text-center">Opción</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="(item, index) in notas" :key="index">
                <td class="text-justify">
                    <template>
                        {{item.nombre}}
                    </template>
                </td>
                <td class="text-justify">
                    <template>
                        {{item.descripcion}}
                    </template>
                </td>
                <td class="text-justify">
                    <template>
                        {{item.updated_at}}
                    </template>
                </td>
                <td class="text-justify">
                    <template class="mb-2">
                        <button class="btn btn-danger btn-sm" @click="eliminarNota(item, index)">Eliminar</button>
                    </template>
                     <template class="mb-2"> 
                        <button class="btn btn-warning btn-sm" @click="editarFormulario(item)">Editar</button>
                    </template>
                    
                </td>
                
            </tr>
        </tbody>
    </table>

    
  </div>
</template>

<script>
export default {

    data() {
        return {
            notas: [],
            nota: {
                nombre: '', 
                descripcion: ''
            },
            editarActivo: false,
        }
    },
    created(){
        axios
            .get('/notas')
            .then(res => {
                this.notas = res.data; 
            });
    },
    methods:{
        agregar() {

            if(this.nota.nombre.trim() === '' || this.nota.descripcion.trim() === ''){
                alert('Debes completar todos los campos antes de guardar');
                return;
            }            
            const params = {
                nombre: this.nota.nombre,
                descripcion: this.nota.descripcion
            }
            this.nota.nombre = '';
            this.nota.descripcion= '';

            axios
                .post('/notas', params)
                .then(res => {
                    this.notas.push(res.data)
                })
        },
        eliminarNota(item, index) {
            const confirmacion = confirm(`Eliminar nota ${item.nombre}`);
            if(confirmacion){
                axios
                    .delete(`/notas/${item.id}`)
                    .then(() => {
                        this.notas.splice(index, 1); //Eliminar solo un elemento especifico
                    })
            }
        },
        editarFormulario(item) {
            this.editarActivo = true;
            this.nota.nombre = item.nombre;
            this.nota.descripcion = item.descripcion;
            this.nota.id = item.id;
        },
        editarNota(nota) {
            const params = {
                nombre: nota.nombre,
                descripcion: nota.descripcion
            };

            axios   
                .put(`/notas/${nota.id}`, params)
                .then(res => {
                    this.editarActivo = false;
                    const index = this.notas.findIndex(
                        item => item.id === nota.id  // Buscar el Id que corrsponda al Id en especifico 
                    );
                    this.notas[index] = res.data;
                })
        },
        cancelarEdicion() {
            this.editarActivo = false;
            this.nota =  { nombre: '', descripcion: '' }
        },

    },

}
</script>

<style>

</style>