<template>
    <div class="student-table">
        <div class="filters">
            <font-awesome-icon icon="fa-solid fa-search" />
            <b-form-input id="input-1" v-model=name type="text" placeholder="Nombres"></b-form-input>
            <b-form-input id="input-2" v-model=a_paterno type="text" placeholder="Apellido Paterno"></b-form-input>
            <b-form-input id="input-3" v-model=a_materno type="text" placeholder="Apellido Materno"></b-form-input>
            <b-button id="search" variant="primary" @click="searchFilter(name, a_paterno, a_materno)">Buscar
            </b-button>
            <b-button id="search" variant="outline-info" @click="clearFilter()">Limpiar
            </b-button>
        </div>

        <b-table responsive striped hover :key=tableKey :items=items :fields=fields label-sort-asc="" label-sort-desc=""
            label-sort-clear="">
            <template #cell(opciones)="student">
                <b-button id="delete" variant="danger" @click="deleteStudent(student)">Eliminar</b-button>
            </template>
        </b-table>
    </div>
</template>

<script>
import axios from "axios"

export default {
    data() {
        return {
            tableKey: 0,
            name: "",
            a_paterno: "",
            a_materno: "",
            id: [],
            fields: [
                { key: 'id', thClass: 'd-none', tdClass: 'd-none' },
                {
                    key: 'nombres',
                    sortable: true
                },
                {
                    key: 'a_paterno',
                },
                {
                    key: 'a_materno',
                },
                {
                    key: 'direccion',
                    sortable: false
                },
                {
                    key: 'fecha_nac',
                },
                {
                    key: 'detalle',
                },
                {
                    key: 'opciones',
                }
            ],
            items: [],
            details: [],
            filtered: [],
        }
    },
    methods: {
        makeRequest(url) {
            axios
                .get(url)
                .then(result => {
                    this.items = result.data
                    this.items.map(item => {
                        item.a_paterno = item.apepaterno
                        item.a_materno = item.apematerno

                    })
                })
        },
        getAllStudents() {
            const url = "http://localhost:8080/escolares"
            axios
                .get(url)
                .then(data => {
                    //Filter if is deleted
                    data.data = data.data.filter(
                        item => item.student.eliminado == false
                    )
                    data.data.map(item => { //Setting Properties
                        item.nombres = item.student.nombres
                        item.a_paterno = item.student.apepaterno
                        item.a_materno = item.student.apematerno
                        item.direccion = item.student.direccion
                        item.fecha_nac = item.student.fecha_nac
                        item.detalle = item.observacion
                    })
                    this.items = data.data
                })
        },
        clearFilter() {
            this.name = ""
            this.a_paterno = ""
            this.a_materno = ""
            this.getAllStudents()
        },
        reRender() {
            this.tableKey += 1
            window.location.reload()
        },
        deleteStudent(data) {
            var id = data.item.id
            const url = "http://localhost:8080/escolares/delete/" + id
            axios
                .delete(url)
                /* eslint-disable */
                .then((response) => {
                    this.reRender()
                })
                .catch(error => console.log(error))
        },
        searchFilter(nombres, ape_paterno, ape_materno) {
            /* eslint-disable */
            const urlNombreCompleto = "http://localhost:8080/escolares/completos/" + nombres + "/" + ape_paterno + "/" + ape_materno
            const urlSolo = "http://localhost:8080/escolares/individual/?nombres=" + "&" + "ape_paterno=" + ape_paterno + "&" + "ape_materno=" + ape_materno
            const urlNombrePaterno = "http://localhost:8080/escolares/nombrepaterno/" + nombres + "/" + ape_paterno
            const urlNombreMaterno = "http://localhost:8080/escolares/nombrematerno/" + nombres + "/" + ape_materno
            const urlSoloApellidos = "http://localhost:8080/escolares/soloapellidos/" + ape_paterno + "/" + ape_materno

            //Comparaciones para los filtros
            if (this.name != "") {
                if (this.a_paterno != "" && this.a_materno != "") {
                    this.makeRequest(urlNombreCompleto)
                }
                if (this.a_paterno == "" || this.a_materno == "") { //Solo nombre
                    this.makeRequest(urlSolo)
                }
            }
            else {
                if (this.a_paterno != "" || this.a_materno != "") {
                    if (this.a_paterno != "" && this.a_materno != "") {
                        this.makeRequest(urlSoloApellidos)
                    }
                    else if (this.a_paterno != "" && this.name != "") {
                        this.makeRequest(urlNombrePaterno)
                    }
                    else if (this.a_materno != "" && this.name != "") {
                        this.makeRequest(urlNombreMaterno)
                    }
                    else if (this.a_paterno != "" || this.a_materno != "") {
                        this.makeRequest(urlSolo) //Solo Apellidos el que sea cualquiera vale
                    }
                }
                else {
                    this.reRender()
                }
            }
        }
    },
    mounted() {
        this.getAllStudents()
    },
}
</script>

<style>
tbody tr,
#delete {
    font-size: 13.5px;
}

thead th {
    font-size: 15px;
}

.filters {
    display: flex;
    gap: 20px;
    margin-bottom: 20px;
    align-items: center;
}
</style>