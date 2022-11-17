<template>
    <div class="students-form">
        <h2 class="title">Registrar Estudiante</h2>
        <b-form @submit="onSubmit" @reset="onReset" v-if="show">
            <label for="input-1">Nombres:</label>
            <b-form-input id="input-1" v-model=form.name type="text" placeholder="Su nombre es..." required>
            </b-form-input>

            <label for="input-2">Apellido Paterno:</label>
            <b-form-input id="input-2" v-model=form.a_paterno type="text" placeholder="Apellido paterno..." required>
            </b-form-input>

            <label for="input-3">Apellido Materno:</label>
            <b-form-input id="input-3" v-model=form.a_materno type="text" placeholder="Apellido materno..." required>
            </b-form-input>

            <label for="example-datepicker">Fecha de Nacimiento</label>
            <b-form-datepicker id="example-datepicker" v-model=form.age :max=max class="mb-2"></b-form-datepicker>

            <label for="input-4">Dirección:</label>
            <b-form-input id="input-4" v-model=form.address type="text" placeholder="Dirección..." required>
            </b-form-input>

            <div class="details">
                <b-row>
                    <b-col sm="2">
                        <label for="textarea" id="label-textarea">Detalles:</label>
                    </b-col>
                    <b-col sm="10">
                        <b-form-textarea id="textarea" size="sm" placeholder="Detalles del estudiante..."
                            v-model=form.details>
                        </b-form-textarea>
                    </b-col>
                </b-row>
            </div>

            <div class="buttons">
                <b-button type="submit" variant="success">Registrar</b-button>
                <b-button type="reset" variant="outline-primary">Limpiar Formulario</b-button>
            </div>
        </b-form>
    </div>
</template>

<script>
import axios from "axios"

export default {
    data() {
        const now = new Date()
        const thisDay = new Date(now.getFullYear(), now.getMonth(), now.getDate())
        const thisDate = new Date(thisDay)

        return {
            max: thisDate,
            form: {
                name: "",
                a_paterno: "",
                a_materno: "",
                address: "",
                age: "",
                details: ""
            },
            foods: [{ text: 'Select One', value: null }, 'Carrots', 'Beans', 'Tomatoes', 'Corn'],
            show: true
        }
    },
    methods: {
        onSubmit(event) {
            event.preventDefault()
            //Make the insert request
            const url = "http://localhost:8080/escolares"
            const studentBody = {
                student: {
                    nombres: this.form.name,
                    apepaterno: this.form.a_paterno,
                    apematerno: this.form.a_materno,
                    fecha_nac: this.form.age,
                    direccion: this.form.address,
                    eliminado: false,
                    details: this.form.details
                },
                detail: {
                    observacion: this.form.details
                }
            }


            axios.post(url, studentBody)
                .then(
                    data => console.log(data) //Check the insertion
                )
                .catch(
                    error => console.log(error) //Check the error
                )
        },
        onReset(event) { //Restart of the Formulary
            event.preventDefault()
            this.form.name = ""
            this.form.a_paterno = ""
            this.form.a_materno = ""
            this.form.age = ""
            this.form.address = ""
            this.form.details = ""
            this.$nextTick(() => {
                this.show = true
            })
        }
    }
}
</script>

<style>
.title {
    margin-top: 10px;
    font-weight: bold;
}

form {
    display: flex;
    flex-direction: column;
    margin: 0 15%;
}

label {
    text-align: left;
    margin-bottom: 6.5px;
    font-size: 13px;
    margin-top: 10px;
    font-weight: bold;
}

#input-1,
#input-2,
#input-3,
#input-4 {
    font-size: 14.5px;
}

.details {
    margin-top: 15px;
}

#textarea {
    margin-top: 5px;
    margin-bottom: 15px;
}

.buttons {
    display: flex;
    flex-direction: row;
    gap: 15px;
    justify-content: center;
    margin-bottom: 15px;
}

.buttons button {
    font-size: 14px;
}
</style>