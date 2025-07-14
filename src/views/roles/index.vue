<template>
    <DefaultLayout>
        <b-row class="justify-content-center">
            <b-col cols="12">
                <b-card>
                    <b-card-header>
                        <b-row class="align-items-center justify-content-between">
                            <b-col lg="3">
                                <b-card-title>🔐 Roles y Permisos</b-card-title>
                            </b-col>
                            <b-col lg="2">
                                <b-button type="button" variant="success" @click="ModalRegisterRole = !ModalRegisterRole">
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
                                    <b-th>Role</b-th>
                                    <b-th>Permisos</b-th>
                                    <b-th>Fecha Registro</b-th>
                                    <b-th class="text-end">Acción</b-th>
                                </b-tr>
                            </b-thead>
                            <b-tbody>
                                <b-tr v-for="(role, index) in roles" :key="index">
                                    <b-td>
                                        {{ role.name }}
                                    </b-td>
                                    <b-td>
                                        <ul>
                                            <li v-for="(item, index) in role.permissions_pluck" :key="index">{{ item }}</li>
                                        </ul>
                                    </b-td>
                                    <b-td>
                                        {{ role.created_at }}
                                    </b-td>
                                    <b-td class="text-end">
                                        <a href="#" v-if="role.id != '1'" @click="editRole(role)"><i class="las la-pen text-secondary fs-22"></i></a>
                                        {{ " " }}
                                        <a href="#" v-if="role.id != '1'" @click="deleteRole(role)"><i class="las la-trash-alt text-secondary fs-22"></i></a>
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
            v-model="ModalRegisterRole"
            :title="`🔏 ${role_selected ? 'Edición' : 'Registro'}  de Role y Permisos`"
            :header-class="`bg-${themeColor}`"
            title-class="m-0 text-white"
            :ok-variant="themeColor"
            hide-footer
            centered
            >
            <b-row>
                <b-col lg="12" class="text-center align-self-center">
                    <b-form-input
                        type="text"
                        id="name"
                        v-model="name"
                        placeholder="Nombre del rol"
                    />
                </b-col>

                <b-col lg="12" class="">
                    <b-table-simple responsive class="mb-0 table-centered mt-2">
                        <b-thead class="table-light">
                            <b-tr>
                                <b-th>Modulo</b-th>
                                <b-th>Permisos</b-th>
                            </b-tr>
                        </b-thead>
                        <b-tbody>
                            <b-tr v-for="(PERMISO, index) in PERMISOS" :key="index">
                                <b-td>{{ PERMISO.name }}</b-td>
                                <b-td>
                                    <ul>
                                        <li v-for="(permiso, index2) in PERMISO.permisos" :key="index2">
                                            <b-form-checkbox :checked="permission_selected.includes(permiso.permiso)" @click="addPermission(permiso)" name="checkbox-1">
                                               {{ permiso.name }}
                                            </b-form-checkbox>
                                        </li>
                                    </ul>
                                </b-td>
                            </b-tr>
                        </b-tbody>
                    </b-table-simple>
                </b-col>

                <b-col lg="12" class="mt-3">
                    <div class="modal-footer">
                        <b-button
                            type="button"
                            variant="secondary"
                            @click="ModalRegisterRole = !ModalRegisterRole"
                        >
                            Cerrar
                        </b-button>
                        <b-button type="button" variant="primary" @click="store">
                           {{ role_selected ? 'Editar' : 'Guardar' }}
                        </b-button>
                    </div>
                </b-col>
            </b-row>
        </b-modal>
    </DefaultLayout>
</template>
<script setup lang="ts">
import DefaultLayout from "@/layouts/DefaultLayout.vue";
import { PERMISOS, type Role, type RolePermiso, type RoleResponse, type Roles } from "@/types/roles";
import type { AxiosResponse } from "axios";
import { onMounted, ref, watch } from "vue";
import HttpClient from "@/helpers/http-client";
import Swal from "sweetalert2/dist/sweetalert2.js";
type TVueSwalInstance = typeof Swal & typeof Swal.fire;

const roles = ref<Role[]>([]);
const ModalRegisterRole = ref<Boolean>(false);
const themeColor = ref<String>("primary");

const name = ref<String | null>(null);
const permission_selected = ref<string[]>([]);
const search = ref<String | null>(null);


const currentPage = ref<number>(1);
const totalPage = ref<number>(0);
const perPageRows = ref<number>(1);

const role_selected = ref<Role | undefined>(undefined);

const list = async() => {
    try {
       const res: AxiosResponse<Roles> = await HttpClient.get(
        `roles?page=${currentPage.value}&search=${(search.value ?? '')}`);
      console.log(res);
        roles.value = res.data.roles;
        totalPage.value = res.data.total;
        perPageRows.value = res.data.paginate;
    } catch (error) {
        console.log(error);
    }
}

const reset = async() => {
    search.value = null;
    list();
}

const addPermission = (permiso:RolePermiso) => {

    let INDEX = permission_selected.value.findIndex((prm) => prm == permiso.permiso);
    if(INDEX != -1){
        permission_selected.value.splice(INDEX,1);
    }else{
        permission_selected.value.push(permiso.permiso);
    }

    console.log(permission_selected.value);
}

const store = async () => {

    if(!name.value){
        (Swal as TVueSwalInstance).fire(
            "Upps!",
            "Necesitas ingresar un nombre para el rol",
            "error",
        );
        return;
    }
    if(permission_selected.value.length == 0){
        (Swal as TVueSwalInstance).fire(
            "Upps!",
            "Necesitas seleccionar almenos 1 permiso para el rol",
            "error",
        );
        return;
    }

    try {

        const res: AxiosResponse<RoleResponse> = 
            !role_selected.value ? 
            await HttpClient.post(
            "roles",{
                name: name.value,
                permissions: permission_selected.value
            })
            :
            await HttpClient.put(
            "roles/"+role_selected.value?.id,{
                name: name.value,
                permissions: permission_selected.value
            });

        console.log(res);
        if(res.data.code == 405){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                res.data.message,
                "error",
            );
        }else{
            ModalRegisterRole.value = false;
            if(!role_selected.value){
                if(res.data.role){
                    roles.value.unshift(res.data.role);
                }
            }else{
                let INDEX = roles.value.findIndex((rol) => rol.id == role_selected.value?.id);
                if(INDEX != -1){
                    if(res.data.role){
                        roles.value[INDEX] = res.data.role;
                    }
                }
            }

            (Swal as TVueSwalInstance).fire(
                "Genial!",
                res.data.message,
                "success",
            );
        }
    } catch (error) {
        console.log(error);
    }
}

const editRole = (role:Role) => {
    ModalRegisterRole.value = true;
    role_selected.value = role;
    name.value = role.name;
    permission_selected.value = role.permissions_pluck;
}

watch(currentPage,(value) => {
    list();
})
watch(ModalRegisterRole,(value) => {
    if(value == false){
        role_selected.value = undefined;
        name.value = null;
        permission_selected.value = [];
    }
})

const deleteRole = (role:Role) => {
    try {
        (Swal as TVueSwalInstance)
            .fire({
            title: "Confirmar la eliminación",
            text: `¿Estas seguro de eliminar el rol '${role.name}'' ?`,
            icon: "warning",
            showCancelButton: true,
            confirmButtonColor: "#3085d6",
            cancelButtonColor: "#d33",
            confirmButtonText: "Si, eliminalo!",
        })
            .then(async (result: any) => {
                if (result.isConfirmed) {
                    const res: AxiosResponse<RoleResponse> = await HttpClient.delete(
                        "roles/"+role.id
                    );
                    console.log(res);
                    (Swal as TVueSwalInstance).fire(
                        "Eliminado!",
                        "El rol '"+role.name+"'"+" ha sido eliminado",
                        "success",
                    );
                    let INDEX = roles.value.findIndex(rol => rol.id == role.id);
                    if(INDEX != -1){
                        roles.value.splice(INDEX,1);
                    }
                }
        });
    } catch (error) {
        console.log(error);
    }
}

onMounted(() => {
    list();
})
</script>