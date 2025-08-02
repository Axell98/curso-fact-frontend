<template>
    <DefaultLayout>
        <b-row class="justify-content-center">
            <b-col cols="12">
                <b-card>
                    <b-card-header>
                        <b-row class="align-items-center justify-content-between">
                            <b-col lg="3">
                                <b-card-title>💁 Clientes / 🏤 Empresas</b-card-title>
                            </b-col>
                            <b-col lg="5" class="d-flex">
                                <b-button type="button" variant="success" @click="ModalRegisterClientFinal = !ModalRegisterClientFinal">
                                    <i class="far fa-plus-square ml-3"></i> Registrar Cliente Final
                                </b-button>
                                <b-button class="mx-2" type="button" variant="success" @click="ModalRegisterClientEmpresa = !ModalRegisterClientEmpresa">
                                    <i class="far fa-plus-square ml-3"></i> Registrar Cliente Empresa
                                </b-button>
                            </b-col>
                        </b-row>
                        <b-row class="align-items-center">
                            <b-col lg="3">
                                <b-form-input
                                    type="text"
                                    id="search"
                                    v-model="search"
                                    placeholder="Buscar por nombre"
                                />
                            </b-col>
                            <b-col lg="3" md="3">
                                <b-button type="button" @click="list" variant="success">
                                    <i class="fas fa-search"></i>
                                </b-button>
                                <b-button type="button" @click="reset" variant="dark" class="mx-2">
                                    <i class="fas fa-sync"></i>
                                </b-button>
                            </b-col>
                        </b-row>
                    </b-card-header>
                    <b-card-body class="pt-0">
                        <b-table-simple responsive class="mb-0 table-centered">
                            <b-thead class="table-light">
                                <b-tr>
                                    <b-th>NOMBRE / RAZON SOCIAL</b-th>
                                    <b-th>TIPO DE CLIENTE</b-th>
                                    <b-th>TELEFONO</b-th>
                                    <b-th>TIPO DE DOCUMENTO</b-th>
                                    <b-th>N° DE DOCUMENTO</b-th>
                                    <b-th>UBIGEO</b-th>
                                    <b-th>ESTADO</b-th>
                                    <b-th>FECHA DE REGISTRO</b-th>
                                    <b-th class="text-end">ACCIÓN</b-th>
                                </b-tr>
                            </b-thead>
                            <b-tbody>
                                <b-tr v-for="(client, index) in clients" :key="index">
                                    <b-td>
                                        {{ client.full_name }}
                                    </b-td>
                                    <b-td>
                                        {{ client.type_client == 1 ? 'CLIENTE FINAL' : 'CLIENTE EMPRESA' }}
                                    </b-td>
                                    <b-td>
                                        {{ client.phone }}
                                    </b-td>
                                    <b-td>
                                        {{ client.type_document }}
                                    </b-td>
                                    <b-td>
                                        {{ client.n_document }}
                                    </b-td>
                                    <b-td>
                                        {{ client.region+ "/"+client.provincia+"/"+client.distrito }}
                                    </b-td>
                                    <b-td>
                                        <b-badge variant="primary" v-if="client.state == 1">Activo</b-badge>
                                        <b-badge variant="danger" v-if="client.state == 2">Inactivo</b-badge>
                                    </b-td>
                                    <b-td>
                                        {{ client.created_at }}
                                    </b-td>
                                    <b-td class="text-end">
                                        <a href="#" @click="editClient(client)"><i class="las la-pen text-secondary fs-22"></i></a>
                                        {{ " " }}
                                        <a href="#" @click="deleteClient(client)"><i class="las la-trash-alt text-secondary fs-22"></i></a>
                                    </b-td>
                                </b-tr>
                            </b-tbody>
                        </b-table-simple>
                        <b-pagination
                            v-model="currentPage"
                            :total-rows="totalPage"
                            :per-page="perPageRows"
                            prev-text="Previous"
                            next-text="Next"
                        />
                    </b-card-body>
                </b-card>
            </b-col>
        </b-row>
        <b-modal
            v-model="ModalRegisterClientFinal"
            :title="`🔏 ${client_selected ? 'Edición' : 'Registro'}  de Cliente Final`"
            :header-class="`bg-${themeColor}`"
            title-class="m-0 text-white"
            :ok-variant="themeColor"
            hide-footer
            centered
            size="lg"
            >
            <b-row>
                <b-col lg="5">
                    <label for="name-client" class="col-form-label text-lg-end">Nombre: </label>
                    <b-form-input
                        type="text"
                        id="name-client"
                        v-model="name"
                        placeholder="Example: Jose"
                    />
                </b-col>

                <b-col lg="5">
                    <label for="surname-client" class="col-form-label text-lg-end">Apellido: </label>
                    <b-form-input
                        type="text"
                        id="surname-client"
                        v-model="surname"
                        placeholder="Example: Jose"
                    />
                </b-col>

                <b-col lg="4">
                    <label for="email-client" class="col-form-label text-lg-end">Email: </label>
                    <b-form-input
                        type="text"
                        id="email-client"
                        v-model="email"
                        placeholder="Example: laravest@gmail.com"
                    />
                </b-col>

                <b-col lg="4">
                    <label for="phone-client" class="col-form-label text-lg-end">Telefono: </label>
                    <b-form-input
                        type="number"
                        id="phone-client"
                        v-model="phone"
                        placeholder="Example: ######"
                    />
                </b-col>

                <b-col lg="4">
                    <label for="type-document-client" class="col-form-label text-lg-end">Tipo de documento: </label>
                    <b-form-select id="type_document" v-model="type_document">
                        <option value="DNI">DNI</option>
                        <option value="RUC">RUC</option>
                        <option value="PASAPORTE">PASAPORTE</option>
                        <option value="CARNET DE EXTRANJERIA">CARNET DE EXTRANJERIA</option>
                        <option value="TARJETA MILITAR">TARJETA MILITAR</option>
                    </b-form-select>
                </b-col>

                <b-col lg="4">
                    <label for="n_document-client" class="col-form-label text-lg-end">N° de Documento: </label>
                    <b-form-input
                        type="number"
                        id="n_document-client"
                        v-model="n_document"
                        placeholder="Example: ######"
                    />
                </b-col>

                <b-col lg="4">
                    <label for="birth_date-client" class="col-form-label text-lg-end">Fecha de Nacimiento: </label>
                    <b-form-input
                        type="date"
                        id="birth_date_client"
                        name="birth_date"
                        v-model="birth_date"
                        placeholder="Example: 2011-08-19"
                    />
                </b-col>

                <b-col lg="3">
                    <label for="gender-client-c" class="col-form-label text-lg-end">Genero: </label>
                    <b-form-radio name="gender-client-c" @click="gender = 'M'" value="M" :checked="gender == 'M'">Masculino</b-form-radio>
                    {{ " " }}
                    <b-form-radio name="gender-client-c" @click="gender = 'F'" value="F" :checked="gender == 'F'">Femenino</b-form-radio>
                </b-col>

                <b-col lg="3">
                    <label for="state-client-e" class="col-form-label text-lg-end">Estado: </label>
                    <b-form-radio name="state-client-e" @click="state = 1" value="1" :checked="state == 1">Activo</b-form-radio>
                    {{ " " }}
                    <b-form-radio name="state-client-e" @click="state = 2" value="2" :checked="state == 2">Inactivo</b-form-radio>
                </b-col>

                <b-col lg="6">
                    <label for="address-client" class="col-form-label text-lg-end">Dirección: </label>
                    <b-form-input
                        type="text"
                        id="address-client"
                        v-model="address"
                        placeholder="Example: Psje Lt 18"
                    />
                </b-col>

                <b-col lg="4">
                    <label for="region-client" class="col-form-label text-lg-end">Región: </label>
                    <b-form-select id="region_list" v-model="ubigeo_region">
                            <template v-for="(REGIONE, index) in REGIONES_L" :key="index">
                            <option :value="REGIONE.id">{{ REGIONE.name }}</option>
                        </template>
                    </b-form-select>
                </b-col>

                <b-col lg="4">
                    <label for="provincia-client" class="col-form-label text-lg-end">Provincia: </label>
                    <b-form-select id="provincia_list" v-model="ubigeo_provincia">
                            <template v-for="(PROVINCIA, index) in PROVINCIAS_SELECTEDS" :key="index">
                            <option :value="PROVINCIA.id">{{ PROVINCIA.name }}</option>
                        </template>
                    </b-form-select>
                </b-col>

                <b-col lg="4">
                    <label for="distrito-client" class="col-form-label text-lg-end">Distrito: </label>
                    <b-form-select id="distrito_list" v-model="ubigeo_distrito">
                            <template v-for="(DISTRITO, index) in DISTRITOS_SELECTEDS" :key="index">
                            <option :value="DISTRITO.id">{{ DISTRITO.name }}</option>
                        </template>
                    </b-form-select>
                </b-col>

                <b-col lg="12" class="mt-3">
                    <div class="modal-footer">
                        <b-button
                            type="button"
                            variant="secondary"
                            data-bs-dismiss="modal"
                        >
                            Cerrar
                        </b-button>
                        <b-button type="button" variant="primary" @click="store">
                            {{ client_selected ? 'Editar' : 'Guardar' }}
                        </b-button>
                    </div>
                </b-col>

            </b-row>
        </b-modal>
        <b-modal
            v-model="ModalRegisterClientEmpresa"
            :title="`🔏 ${client_selected ? 'Edición' : 'Registro'}  de Cliente Empresa `"
            :header-class="`bg-${themeColor}`"
            title-class="m-0 text-white"
            :ok-variant="themeColor"
            hide-footer
            centered
            size="lg"
            >

            <b-row>
                <b-col lg="8">
                    <label for="fullname-client" class="col-form-label text-lg-end">Razon Social: </label>
                    <b-form-input
                        type="text"
                        id="fullname-client"
                        v-model="full_name"
                        placeholder="Example: Jose"
                    />
                </b-col>
                <b-col lg="4">
                </b-col>

                <b-col lg="4">
                    <label for="email-client" class="col-form-label text-lg-end">Email: </label>
                    <b-form-input
                        type="text"
                        id="email-client"
                        v-model="email"
                        placeholder="Example: laravest@gmail.com"
                    />
                </b-col>

                <b-col lg="4">
                    <label for="phone-client" class="col-form-label text-lg-end">Telefono: </label>
                    <b-form-input
                        type="number"
                        id="phone-client"
                        v-model="phone"
                        placeholder="Example: laravest@gmail.com"
                    />
                </b-col>

                <b-col lg="4">
                    <label for="type-document-client" class="col-form-label text-lg-end">Tipo de documento: </label>
                    <b-form-select id="type_document" v-model="type_document">
                        <option value="RUC">RUC</option>
                        <option value="RUC20">RUC20</option>
                    </b-form-select>
                </b-col>

                <b-col lg="4">
                    <label for="n_document-client" class="col-form-label text-lg-end">N° de Documento: </label>
                    <b-form-input
                        type="number"
                        id="n_document-client"
                        v-model="n_document"
                        placeholder="Example: ######"
                    />
                </b-col>

                <b-col lg="4">
                    <label for="birth_date-client-company" class="col-form-label text-lg-end">Fecha de Aniversario: </label>
                    <b-form-input
                        type="date"
                        id="birth_date_client-company"
                        v-model="birth_date"
                        placeholder="Example: 2011-08-19"
                    />
                </b-col>

                <b-col lg="3">
                    <label for="state-client" class="col-form-label text-lg-end">Estado: </label>
                    <b-form-radio name="state-client" @click="state = 1" value="1" :checked="state == 1">Activo</b-form-radio>
                    {{ " " }}
                    <b-form-radio name="state-client" @click="state = 2" value="2" :checked="state == 2">Inactivo</b-form-radio>
                </b-col>
                
                <b-col lg="6">
                    <label for="address-client" class="col-form-label text-lg-end">Dirección: </label>
                    <b-form-input
                        type="text"
                        id="address-client"
                        v-model="address"
                        placeholder="Example: Psje Lt 18"
                    />
                </b-col>
                <b-col lg="6"></b-col>
                
                <b-col lg="4">
                    <label for="region-client" class="col-form-label text-lg-end">Región: </label>
                    <b-form-select id="region_list" v-model="ubigeo_region">
                            <template v-for="(REGIONE, index) in REGIONES_L" :key="index">
                            <option :value="REGIONE.id">{{ REGIONE.name }}</option>
                        </template>
                    </b-form-select>
                </b-col>

                <b-col lg="4">
                    <label for="provincia-client" class="col-form-label text-lg-end">Provincia: </label>
                    <b-form-select id="provincia_list" v-model="ubigeo_provincia">
                            <template v-for="(PROVINCIA, index) in PROVINCIAS_SELECTEDS" :key="index">
                            <option :value="PROVINCIA.id">{{ PROVINCIA.name }}</option>
                        </template>
                    </b-form-select>
                </b-col>

                <b-col lg="4">
                    <label for="distrito-client" class="col-form-label text-lg-end">Distrito: </label>
                    <b-form-select id="distrito_list" v-model="ubigeo_distrito">
                            <template v-for="(DISTRITO, index) in DISTRITOS_SELECTEDS" :key="index">
                            <option :value="DISTRITO.id">{{ DISTRITO.name }}</option>
                        </template>
                    </b-form-select>
                </b-col>

                <b-col lg="12" class="mt-3">
                    <div class="modal-footer">
                        <b-button
                            type="button"
                            variant="secondary"
                            data-bs-dismiss="modal"
                        >
                            Cerrar
                        </b-button>
                        <b-button type="button" variant="primary" @click="store">
                            {{ client_selected ? 'Editar' : 'Guardar' }}
                        </b-button>
                    </div>
                </b-col>

            </b-row>

        </b-modal>
    </DefaultLayout>
</template>
<script setup lang="ts">
import DefaultLayout from "@/layouts/DefaultLayout.vue";
import type { AxiosResponse } from "axios";
import { onMounted, ref, watch } from "vue";
import HttpClient from "@/helpers/http-client";
import Swal from "sweetalert2/dist/sweetalert2.js";
import type { Client, ClientResponse, Clients, UbigeoClient } from "@/types/clients";
type TVueSwalInstance = typeof Swal & typeof Swal.fire;
import REGIONES from './json/regiones.json';
import PROVINCIAS from './json/provincias.json';
import DISTRITOS from './json/distritos.json';

const ModalRegisterClientFinal = ref<Boolean>(false);
const ModalRegisterClientEmpresa = ref<Boolean>(false);
const themeColor = ref<string>("primary");

const REGIONES_L = ref<UbigeoClient[]>(REGIONES);
const PROVINCIAS_L = ref<UbigeoClient[]>(PROVINCIAS);
const DISTRITOS_L = ref<UbigeoClient[]>(DISTRITOS);

const PROVINCIAS_SELECTEDS = ref<UbigeoClient[]>([]);
const DISTRITOS_SELECTEDS = ref<UbigeoClient[]>([]);

const name = ref<string>("");
const surname = ref<string>("");
const full_name = ref<string>("");
const email = ref<string>("");
const phone = ref<string>("");
const type_document = ref<string>("DNI");
const n_document = ref<number | null>(null);
const birth_date = ref<Date | string>("");
const gender = ref<string>("M");
const address = ref<string>("");
const ubigeo_region = ref<string>("");
const ubigeo_provincia = ref<string>("");
const ubigeo_distrito = ref<string>("");
const state = ref<number>(1);
const type_client = ref<string>("1");//1 ES CLIENTE FINAL Y 2 CLIENTE EMPRESA

const client_selected = ref<Client | undefined>(undefined);

const clients = ref<Client[]>([]);
const currentPage = ref<number>(1);
const totalPage = ref<number>(0);
const perPageRows = ref<number>(1);

const search = ref<String  | null>(null);

const list = async() => {
    try {
       const res: AxiosResponse<Clients> = await HttpClient.get(
        `clients?page=${currentPage.value}&search=${(search.value ?? '')}`);
      console.log(res);
        clients.value = res.data.clients.data;
        totalPage.value = res.data.total;
        perPageRows.value = res.data.paginate;
    } catch (error) {
        console.log(error);
    }
}

const store = async () => {
    try {
        
        if(type_client.value == "1"){//CLIENTE TIPO NATURAL O FINAL
            if(!name.value){
                (Swal as TVueSwalInstance).fire(
                    "Upps!",
                    "Necesitas ingresar un nombre para el cliente",
                    "error",
                );
                return;
            }
            if(!surname.value){
                (Swal as TVueSwalInstance).fire(
                    "Upps!",
                    "El campo apellido es obligatorio",
                    "error",
                );
                return;
            }
        }else{
            if(!full_name.value){
                (Swal as TVueSwalInstance).fire(
                    "Upps!",
                    "El campo razon social es obligatorio",
                    "error",
                );
                return;
            }
        }

        if(!email.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "El campo email es obligatorio",
                "error",
            );
            return;
        }
        if(!phone.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "El campo telefono es obligatorio",
                "error",
            );
            return;
        }
        if(!n_document.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "El campo numero de documento es obligatorio",
                "error",
            );
            return;
        }
        if(!birth_date.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas seleccionar una fecha para el cliente",
                "error",
            );
            return;
        }
        if(!address.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas una dirección para el cliente",
                "error",
            );
            return;
        }
        if(!ubigeo_region.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas una región para el cliente",
                "error",
            );
            return;
        }
        if(!ubigeo_provincia.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas una provincia para el cliente",
                "error",
            );
            return;
        }
        if(!ubigeo_distrito.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas un distrito para el cliente",
                "error",
            );
            return;
        }

        let region = "";
        let provincia = "";
        let distrito = "";
        let REGION_S = REGIONES_L.value.find((ubgr) => ubgr.id == ubigeo_region.value);
        if(REGION_S){
            region = REGION_S.name;
        }
        let PROVINCIA_S = PROVINCIAS_L.value.find((ubgp) => ubgp.id == ubigeo_provincia.value);
        if(PROVINCIA_S){
            provincia = PROVINCIA_S.name;
        }
        let DISTRITO_S = DISTRITOS_L.value.find((ubgd) => ubgd.id == ubigeo_distrito.value);
        if(DISTRITO_S){
            distrito = DISTRITO_S.name;
        }
        let data = {
            name:name.value,
            surname: surname.value,
            full_name: type_client.value == '1' ? name.value + " " +surname.value : full_name.value,
            email:email.value,
            phone: phone.value,
            n_document: n_document.value,
            type_document: type_document.value,
            address: address.value,
            birth_date: birth_date.value,
            ubigeo_region: ubigeo_region.value,
            ubigeo_provincia: ubigeo_provincia.value,
            ubigeo_distrito: ubigeo_distrito.value,
            region: region,
            provincia: provincia,
            distrito: distrito,
            type_client:type_client.value,
            state: state.value,
            gender:gender.value,
        }
       
        const res: AxiosResponse<ClientResponse> = 
            !client_selected.value ? 
            await HttpClient.post(
            "clients",data)
            :
            await HttpClient.patch(
            "clients/"+client_selected.value?.id,data);

        console.log(res);
        if(res.data.code == 405){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                res.data.message,
                "error",
            );
        }else{
            ModalRegisterClientFinal.value = false
            ModalRegisterClientEmpresa.value = false
            if(!client_selected.value){
                if(res.data.client){
                    clients.value.unshift(res.data.client);
                }
            }else{
                let INDEX = clients.value.findIndex((usr) => usr.id == client_selected.value?.id);
                if(INDEX != -1){
                    if(res.data.client){
                        clients.value[INDEX] = res.data.client;
                    }
                }
            }

            (Swal as TVueSwalInstance).fire(
                "Genial!",
                res.data.message,
                "success",
            );

        }

    } catch (error:any) {
        console.log(error);
        if(error.response?.data){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                error.response?.data.message,
                "error",
            );
            return;
        }
    }
}

const editClient = (client:Client) => {

    client_selected.value = client;
    if(client_selected.value.type_client == 1){
        ModalRegisterClientFinal.value = true;
        name.value = client_selected.value.name;
        surname.value = client_selected.value.surname ?? '';
    }else{
        ModalRegisterClientEmpresa.value = true;
        full_name.value = client_selected.value.full_name;
    }

    email.value = client.email;
    phone.value = client.phone+"";
    state.value = client.state;

    ubigeo_region.value = client.ubigeo_region;
    setTimeout(() => {
        ubigeo_provincia.value = client.ubigeo_provincia;
        setTimeout(() => {
            ubigeo_distrito.value = client.ubigeo_distrito;
        }, 25);
    }, 25);

    address.value = client.address;
    type_client.value = client.type_client+"";
    birth_date.value = client.birth_date ?? '';

    type_document.value = client.type_document ?? '';
    n_document.value = Number(client.n_document);
    gender.value = client.gender;
    state.value = client.state;
}
const deleteClient = (client:Client) => {
    try {
        (Swal as TVueSwalInstance)
            .fire({
            title: "Confirmar la eliminación",
            text: `¿Estas seguro de eliminar este cliente '${client.full_name}'' ?`,
            icon: "warning",
            showCancelButton: true,
            confirmButtonColor: "#3085d6",
            cancelButtonColor: "#d33",
            confirmButtonText: "Si, eliminalo!",
        })
            .then(async (result: any) => {
                if (result.isConfirmed) {
                    const res: AxiosResponse<ClientResponse> = await HttpClient.delete(
                        "clients/"+client.id
                    );
                    console.log(res);
                    (Swal as TVueSwalInstance).fire(
                        "Eliminado!",
                        "El cliente '"+client.full_name+"'"+"se ha sido eliminado",
                        "success",
                    );
                    let INDEX = clients.value.findIndex(ctg => ctg.id == client.id);
                    if(INDEX != -1){
                        clients.value.splice(INDEX,1);
                    }
                }
        });
    } catch (error) {
        console.log(error);
    }
}

const clearField = () => {
    name.value = '';
    surname.value = '';
    full_name.value = "";
    email.value = '';
    phone.value = "";
    state.value = 1;
    type_client.value = "1";
    birth_date.value = "";
    address.value = "";
    ubigeo_distrito.value = "";
    ubigeo_provincia.value = "";
    ubigeo_region.value = "";
    type_document.value = '';
    n_document.value = 0;
    gender.value = '';
    state.value = 1;
}

const reset = () => {
    search.value = "";
    list();
}

watch(ubigeo_region,(value) => {
    if(value){
        PROVINCIAS_SELECTEDS.value = PROVINCIAS_L.value.filter((ubg) => ubg.department_id == value);
        DISTRITOS_SELECTEDS.value = [];
        ubigeo_distrito.value = "";
        ubigeo_provincia.value = "";
    }
})
watch(ubigeo_provincia,(value) => {
    if(value){
        DISTRITOS_SELECTEDS.value = DISTRITOS_L.value.filter((ubg) => ubg.province_id == value);
        ubigeo_distrito.value = "";
    }
})

watch(ModalRegisterClientFinal,(value) => {
    if(value == false){
        client_selected.value = undefined;
        clearField();
    }
    if(value == true){
        type_client.value = "1";//CLIENTE FINAL
    }
})
watch(ModalRegisterClientEmpresa,(value) => {
    if(value == false){
        client_selected.value = undefined;
        clearField();
    }
    if(value == true){
        type_client.value = "2";//CLIENTE EMPRESA
    }
})
watch(currentPage,(value) => {
    list();
})
onMounted(() => {
  list();  
})
</script>