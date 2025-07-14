<template>
    <DefaultLayout>
        <b-row class="justify-content-center">
            <b-col cols="12">
                <b-card>
                    <b-card-header>
                        <b-row class="align-items-center justify-content-between">
                            <b-col lg="3">
                                <b-card-title>💁 Usuarios</b-card-title>
                            </b-col>
                            <b-col lg="2">
                                <b-button type="button" variant="success" @click="ModalRegisterUser = !ModalRegisterUser">
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
                                    <b-th>Nombre y Apellido</b-th>
                                    <b-th>Email</b-th>
                                    <b-th>Telefono</b-th>
                                    <b-th>Role</b-th>
                                    <b-th>Estado</b-th>
                                    <b-th>Fecha Registro</b-th>
                                    <b-th class="text-end">Acción</b-th>
                                </b-tr>
                            </b-thead>
                            <b-tbody>
                                <b-tr v-for="(user, index) in users" :key="index">
                                    <b-td>
                                        {{ user.full_name }}
                                    </b-td>
                                    <b-td>
                                        {{ user.email }}
                                    </b-td>
                                    <b-td>
                                        {{ user.phone }}
                                    </b-td>
                                    <b-td>
                                        {{ user.role.name }}
                                    </b-td>
                                    <b-td>
                                        <b-badge variant="primary" v-if="user.state == 1">Activo</b-badge>
                                        <b-badge variant="danger" v-if="user.state == 2">Inactivo</b-badge>
                                    </b-td>
                                    <b-td>
                                        {{ user.created_at }}
                                    </b-td>
                                    <b-td class="text-end">
                                        <a href="#" @click="editUser(user)"><i class="las la-pen text-secondary fs-22"></i></a>
                                        {{ " " }}
                                        <a href="#" @click="deleteUser(user)"><i class="las la-trash-alt text-secondary fs-22"></i></a>
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
            v-model="ModalRegisterUser"
            :title="`🔏 ${user_selected ? 'Edición' : 'Registro'}  de Usuario`"
            :header-class="`bg-${themeColor}`"
            title-class="m-0 text-white"
            :ok-variant="themeColor"
            hide-footer
            centered
            size="lg"
            >
            <b-row>
                
                <b-col lg="5">
                    <label for="name-user" class="col-form-label text-lg-end">Nombre: </label>
                    <b-form-input
                        type="text"
                        id="name-user"
                        v-model="name"
                        placeholder="Example: Jose"
                    />
                </b-col>
                <b-col lg="5">
                    <label for="surname-user" class="col-form-label text-lg-end">Apellido: </label>
                    <b-form-input
                        type="text"
                        id="surname-user"
                        v-model="surname"
                        placeholder="Example: Jose"
                    />
                </b-col>
                
                <b-col lg="4">
                    <label for="email-user" class="col-form-label text-lg-end">Email: </label>
                    <b-form-input
                        type="text"
                        id="email-user"
                        v-model="email"
                        placeholder="Example: laravest@gmail.com"
                    />
                </b-col>

                <b-col lg="4">
                    <label for="phone-user" class="col-form-label text-lg-end">Telefono: </label>
                    <b-form-input
                        type="number"
                        id="phone-user"
                        v-model="phone"
                        placeholder="Example: ######"
                    />
                </b-col>

                <b-col lg="4">
                    <label for="role_user" class="col-form-label text-lg-end">Role: </label>
                    <b-form-select id="type_document_list" v-model="role_id">
                        <option value="">Selec. Rol</option>
                        <template v-for="(role, index) in roles" :key="index">
                            <option :value="role.id">{{ role.name }}</option>
                        </template>
                    </b-form-select>
                </b-col>

                <b-col lg="4">
                    <label for="type-document-user" class="col-form-label text-lg-end">Tipo de documento: </label>
                    <b-form-select id="type_document_list" v-model="type_document">
                        <option value="DNI">DNI</option>
                        <option value="PASAPORTE">PASAPORTE</option>
                        <option value="CARNET DE EXTRANJERIA">CARNET DE EXTRANJERIA</option>
                        <option value="TARJETA MILITAR">TARJETA MILITAR</option>
                    </b-form-select>
                </b-col>

                <b-col lg="4">
                    <label for="n_document-user" class="col-form-label text-lg-end">N° de Documento: </label>
                    <b-form-input
                        type="number"
                        id="n_document-user"
                        v-model="n_document"
                        placeholder="Example: #######"
                    />
                </b-col>

                <b-col lg="4">
                    <label for="gender-user" class="col-form-label text-lg-end">Genero: </label>
                    <b-form-radio name="gender-user" @click="gender = 'M'" value="M" :checked="gender == 'M'">Masculino</b-form-radio>
                    {{ " " }}
                    <b-form-radio name="gender-user" @click="gender = 'F'" value="F" :checked="gender == 'F'">Femenino</b-form-radio>
                </b-col>

                <b-col lg="3">
                    <label for="state-user" class="col-form-label text-lg-end">Estado: </label>
                    <b-form-radio name="state-user" @click="state = 1" value="1" :checked="state == 1">Activo</b-form-radio>
                    {{ " " }}
                    <b-form-radio name="state-user" @click="state = 2" value="2" :checked="state == 2">Inactivo</b-form-radio>
                </b-col>

                <b-col lg="4">
                    <label for="password-user" class="col-form-label text-lg-end">Contraseña: </label>
                    <b-form-input
                        type="password"
                        id="password-user"
                        v-model="password"
                        placeholder="*********"
                    />
                </b-col>

                <b-col lg="5">
                    <label for="avatar-user" class="col-form-label text-lg-end">Avatar de Usuario: </label>
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
                            @click="ModalRegisterUser = !ModalRegisterUser"
                        >
                            Cerrar
                        </b-button>
                        <b-button type="button" variant="primary" @click="store">
                           {{ user_selected ? 'Editar' : 'Guardar' }}
                        </b-button>
                    </div>
                </b-col>
            </b-row>
        </b-modal>
    </DefaultLayout>
</template>
<script setup lang="ts">
import DefaultLayout from "@/layouts/DefaultLayout.vue";
import type { RoleUser, User, UserResponse, Users } from "@/types/users";
import type { AxiosResponse } from "axios";
import { onMounted, ref, watch } from "vue";
import HttpClient from "@/helpers/http-client";
import Swal from "sweetalert2/dist/sweetalert2.js";
type TVueSwalInstance = typeof Swal & typeof Swal.fire;

const ModalRegisterUser = ref<Boolean>(false);
const themeColor = ref<string>("primary");

const name = ref<string>("");
const surname = ref<string>("");
const email = ref<string>("");
const phone = ref<number>(0);
const role_id = ref<string>("");
const type_document = ref<string>("DNI");
const n_document  = ref<number>(0);
const roles = ref<RoleUser[]>([]);
const gender = ref<string>("M");
const state = ref<number>(1);
const password = ref<string>("");
const FILE_AVATAR = ref<File | undefined>(undefined);
const IMAGEN_PREVIZUALIZA = ref<string | ArrayBuffer | null>("");

const user_selected = ref<User | undefined>(undefined);

const users = ref<User[]>([]);
const currentPage = ref<number>(1);
const totalPage = ref<number>(0);
const perPageRows = ref<number>(1);

const search = ref<String  | null>(null);

const list = async() => {
    try {
       const res: AxiosResponse<Users> = await HttpClient.get(
        `users?page=${currentPage.value}&search=${(search.value ?? '')}`);
      console.log(res);
        users.value = res.data.users.data;
        totalPage.value = res.data.total;
        perPageRows.value = res.data.paginate;
        roles.value = res.data.roles;
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

        if(!surname.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas ingresar un apellido para el usuario",
                "error",
            );
            return;
        }

        if(!email.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas ingresar un email para el usuario",
                "error",
            );
            return;
        }

        if(!role_id.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas ingresar un rol para el usuario",
                "error",
            );
            return;
        }

        if(!n_document.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas ingresar un n° de documento para el usuario",
                "error",
            );
            return;
        }

        if(!user_selected.value){
            if(!password.value){
                (Swal as TVueSwalInstance).fire(
                    "Upps!",
                    "Necesitas ingresar una contraseña para el usuario",
                    "error",
                );
                return;
            }
        }

        let formData = new FormData();
        formData.append("name",name.value);
        formData.append("surname",surname.value);
        formData.append("email",email.value);
        formData.append("phone",phone.value+"");
        formData.append("role_id",role_id.value+"");
        formData.append("type_document",type_document.value);
        formData.append("n_document",n_document.value+"");
        if(password.value){
            formData.append("password",password.value);
        }
        formData.append("gender",gender.value);
        formData.append("state",state.value+"");
        if(FILE_AVATAR.value){
            formData.append("imagen",FILE_AVATAR.value);
        }

        const res: AxiosResponse<UserResponse> = 
            !user_selected.value ? 
            await HttpClient.post(
            "users",formData)
            :
            await HttpClient.post(
            "users/"+user_selected.value?.id,formData);

        console.log(res);
        if(res.data.code == 405){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                res.data.message,
                "error",
            );
        }else{
            ModalRegisterUser.value = false;
            if(!user_selected.value){
                if(res.data.user){
                    users.value.unshift(res.data.user);
                }
            }else{
                let INDEX = users.value.findIndex((usr) => usr.id == user_selected.value?.id);
                if(INDEX != -1){
                    if(res.data.user){
                        users.value[INDEX] = res.data.user;
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

const editUser = (user:User) => {

    ModalRegisterUser.value = true;
    user_selected.value = user

    name.value = user_selected.value.name;
    surname.value = user_selected.value.surname;
    email.value = user.email;
    phone.value = user.phone;
    state.value = user.state;
    IMAGEN_PREVIZUALIZA.value = user.avatar ?? '';
    FILE_AVATAR.value = undefined;
    type_document.value = user.type_document ?? '';
    n_document.value = user.n_document ?? 0;
    gender.value = user.gender;
    state.value = user.state;
    role_id.value = user.role_id;
}
const deleteUser = (user:User) => {
    try {
        (Swal as TVueSwalInstance)
            .fire({
            title: "Confirmar la eliminación",
            text: `¿Estas seguro de eliminar este usuario '${user.full_name}'' ?`,
            icon: "warning",
            showCancelButton: true,
            confirmButtonColor: "#3085d6",
            cancelButtonColor: "#d33",
            confirmButtonText: "Si, eliminalo!",
        })
            .then(async (result: any) => {
                if (result.isConfirmed) {
                    const res: AxiosResponse<UserResponse> = await HttpClient.delete(
                        "users/"+user.id
                    );
                    console.log(res);
                    (Swal as TVueSwalInstance).fire(
                        "Eliminado!",
                        "El usuario '"+user.full_name+"'"+" ha sido eliminado",
                        "success",
                    );
                    let INDEX = users.value.findIndex(urs => urs.id == user.id);
                    if(INDEX != -1){
                        users.value.splice(INDEX,1);
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
    FILE_AVATAR.value = $event.target.files[0];
    let reader = new FileReader();
    if(FILE_AVATAR.value){
        reader.readAsDataURL(FILE_AVATAR.value);
        reader.onloadend = () => IMAGEN_PREVIZUALIZA.value = reader.result;
    }
}

const clearField = () => {
    name.value = '';
    surname.value = '';
    email.value = '';
    phone.value = 0;
    state.value = 1;
    IMAGEN_PREVIZUALIZA.value = '';
    FILE_AVATAR.value = undefined;
    type_document.value = '';
    n_document.value = 0;
    gender.value = '';
    state.value = 1;
    password.value = '';
    role_id.value = '';
}

const reset = () => {
    search.value = "";
    list();
}

watch(ModalRegisterUser,(value) => {
    if(value == false){
        user_selected.value = undefined;
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