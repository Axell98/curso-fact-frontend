<template>
    <DefaultLayout>
        <b-row class="justify-content-center">
            <b-col cols="12">
                <b-card>
                    <b-card-header>
                        <b-row class="align-items-center justify-content-between">
                            <b-col lg="3">
                                <b-card-title>🧮 Categories</b-card-title>
                            </b-col>
                            <b-col lg="2">
                                <b-button type="button" variant="success" @click="ModalRegisterCategorie = !ModalRegisterCategorie">
                                    <i class="far fa-plus-square ml-3"></i> Registrar
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
                                    <b-th>Categoria</b-th>
                                    <b-th>Estado</b-th>
                                    <b-th>Fecha Registro</b-th>
                                    <b-th class="text-end">Acción</b-th>
                                </b-tr>
                            </b-thead>
                            <b-tbody>
                                <b-tr v-for="(categorie, index) in categories" :key="index">
                                    <b-td>
                                        <div>
                                            <img :src="categorie.imagen" style="border-radius: 50%;width: 54px;" alt="">
                                            
                                            <span class="mx-2">{{ categorie.title }}</span>
                                            
                                        </div>
                                    </b-td>
                                    
                                    <b-td>
                                        <b-badge variant="primary" v-if="categorie.state == 1">Activo</b-badge>
                                        <b-badge variant="danger" v-if="categorie.state == 2">Inactivo</b-badge>
                                    </b-td>
                                    <b-td>
                                        {{ categorie.created_at }}
                                    </b-td>
                                    <b-td class="text-end">
                                        <a href="#" @click="editCategorie(categorie)"><i class="las la-pen text-secondary fs-22"></i></a>
                                        {{ " " }}
                                        <a href="#" @click="deleteCategorie(categorie)"><i class="las la-trash-alt text-secondary fs-22"></i></a>
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
            v-model="ModalRegisterCategorie"
            :title="`🔏 ${categorie_selected ? 'Edición' : 'Registro'}  de Categoria`"
            :header-class="`bg-${themeColor}`"
            title-class="m-0 text-white"
            :ok-variant="themeColor"
            hide-footer
            centered
            size="lg"
            >
            <b-row>
                
                <b-col lg="8">
                    <label for="name-user" class="col-form-label text-lg-end">Nombre de Categoria: </label>
                    <b-form-input
                        type="text"
                        id="name-user"
                        v-model="name"
                        placeholder="Example: Jose"
                    />
                </b-col>
                
                <b-col lg="3">
                    <label for="state-user" class="col-form-label text-lg-end">Estado: </label>
                    <b-form-radio name="state-user" @click="state = 1" value="1" :checked="state == 1">Activo</b-form-radio>
                    {{ " " }}
                    <b-form-radio name="state-user" @click="state = 2" value="2" :checked="state == 2">Inactivo</b-form-radio>
                </b-col>

                <b-col lg="5">
                    <label for="avatar-user" class="col-form-label text-lg-end">Imagen de Categoria: </label>
                    <b-input-group class="mb-3">
                        <b-form-file @change="loadFile($event)"  />
                        <b-input-group-text>Upload</b-input-group-text>
                    </b-input-group>

                    <img v-if="IMAGEN_PREVIZUALIZA" :src="IMAGEN_PREVIZUALIZA" alt="" width="100px" class="rounded d-block mx-auto" />
                </b-col>


                <b-col lg="12" class="mt-3">
                    <div class="modal-footer">
                        <b-button
                            type="button"
                            variant="secondary"
                            @click="ModalRegisterCategorie = !ModalRegisterCategorie"
                        >
                            Cerrar
                        </b-button>
                        <b-button type="button" variant="primary" @click="store">
                           {{ categorie_selected ? 'Editar' : 'Guardar' }}
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
import type { Categorie, CategorieResponse, Categories } from "@/types/categories";
type TVueSwalInstance = typeof Swal & typeof Swal.fire;

const ModalRegisterCategorie = ref<Boolean>(false);
const themeColor = ref<string>("primary");

const name = ref<string>("");
const state = ref<number>(1);
const FILE_IMAGEN = ref<File | undefined>(undefined);
const IMAGEN_PREVIZUALIZA = ref<string | ArrayBuffer | null>("");

const categorie_selected = ref<Categorie | undefined>(undefined);

const categories = ref<Categorie[]>([]);
const currentPage = ref<number>(1);
const totalPage = ref<number>(0);
const perPageRows = ref<number>(1);

const search = ref<String  | null>(null);

const list = async() => {
    try {
       const res: AxiosResponse<Categories> = await HttpClient.get(
        `categories?page=${currentPage.value}&search=${(search.value ?? '')}`);
      console.log(res);
        categories.value = res.data.categories;
        totalPage.value = res.data.total;
        perPageRows.value = res.data.paginate;
    } catch (error) {
        console.log(error);
    }
}

const store = async () => {
    try {
        
        if(!name.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas ingresar un nombre para el usuario",
                "error",
            );
            return;
        }

        let formData = new FormData();
        formData.append("title",name.value);
        formData.append("state",state.value+"");
        if(FILE_IMAGEN.value){
            formData.append("image",FILE_IMAGEN.value);
        }

        const res: AxiosResponse<CategorieResponse> = 
            !categorie_selected.value ? 
            await HttpClient.post(
            "categories",formData)
            :
            await HttpClient.post(
            "categories/"+categorie_selected.value?.id,formData);

        console.log(res);
        if(res.data.code == 405){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                res.data.message,
                "error",
            );
        }else{
            ModalRegisterCategorie.value = false;
            if(!categorie_selected.value){
                if(res.data.categorie){
                    categories.value.unshift(res.data.categorie);
                }
            }else{
                let INDEX = categories.value.findIndex((usr) => usr.id == categorie_selected.value?.id);
                if(INDEX != -1){
                    if(res.data.categorie){
                        categories.value[INDEX] = res.data.categorie;
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

const editCategorie = (categorie:Categorie) => {

    ModalRegisterCategorie.value = true;
    categorie_selected.value = categorie

    name.value = categorie_selected.value.title;
   
    state.value = categorie.state;
    IMAGEN_PREVIZUALIZA.value = categorie.imagen ?? '';
    FILE_IMAGEN.value = undefined;
}
const deleteCategorie = (categorie:Categorie) => {
    try {
        (Swal as TVueSwalInstance)
            .fire({
            title: "Confirmar la eliminación",
            text: `¿Estas seguro de eliminar esta categoria '${categorie.title}'' ?`,
            icon: "warning",
            showCancelButton: true,
            confirmButtonColor: "#3085d6",
            cancelButtonColor: "#d33",
            confirmButtonText: "Si, eliminalo!",
        })
            .then(async (result: any) => {
                if (result.isConfirmed) {
                    const res: AxiosResponse<CategorieResponse> = await HttpClient.delete(
                        "categories/"+categorie.id
                    );
                    console.log(res);
                    (Swal as TVueSwalInstance).fire(
                        "Eliminado!",
                        "La categoria '"+categorie.title+"'"+"se ha sido eliminado",
                        "success",
                    );
                    let INDEX = categories.value.findIndex(ctg => ctg.id == categorie.id);
                    if(INDEX != -1){
                        categories.value.splice(INDEX,1);
                    }
                }
        });
    } catch (error) {
        console.log(error);
    }
}

const loadFile = ($event:any) => {
    if($event.target.files[0].type.indexOf("image") < 0){
        IMAGEN_PREVIZUALIZA.value = null;
        (Swal as TVueSwalInstance).fire(
            "Upps!",
            "SOLAMENTE PUEDEN SER ARCHIVOS DE TIPO IMAGEN",
            "error",
        );
        return;
    }
    FILE_IMAGEN.value = $event.target.files[0];
    let reader = new FileReader();
    if(FILE_IMAGEN.value){
        reader.readAsDataURL(FILE_IMAGEN.value);
        reader.onloadend = () => IMAGEN_PREVIZUALIZA.value = reader.result;
    }
}

const clearField = () => {
    name.value = '';
    state.value = 1;
    IMAGEN_PREVIZUALIZA.value = '';
    FILE_IMAGEN.value = undefined;
    state.value = 1;
}

const reset = () => {
    search.value = "";
    list();
}

watch(ModalRegisterCategorie,(value) => {
    if(value == false){
        categorie_selected.value = undefined;
        clearField();
    }
})
watch(currentPage,(value) => {
    list();
})
onMounted(() => {
  list();  
})
</script>