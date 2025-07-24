<template>
    <DefaultLayout>
        <b-row class="justify-content-center">
            <b-col cols="12">
                <b-card>
                    <b-card-header>
                        <b-row class="align-items-center justify-content-between">
                            <b-col lg="3">
                                <b-card-title>🛍️ Productos</b-card-title>
                            </b-col>
                            <b-col lg="2">
                                <!-- <a variant="success" href="/rizz_v/product/register">
                                    <i class="far fa-plus-square ml-3"></i> Registrar
                                </a> -->
                                <b-button type="button" variant="success" @click="router.push({name:'product.register'})">
                                    <i class="far fa-plus-square ml-3"></i> Registrar
                                </b-button>
                            </b-col>
                        </b-row>
                        <b-row class="align-items-center">
                            <b-col lg="3">
                                <label for="type_category_list" class="col-form-label text-lg-end">Busqueda: </label>
                                <b-form-input
                                    type="text"
                                    id="search"
                                    v-model="search"
                                    placeholder="Buscar por nombre"
                                />
                            </b-col>
                            <b-col lg="3">
                                <label for="type_category_list" class="col-form-label text-lg-end">Categorias: </label>
                                <b-form-select id="type_category_list" v-model="categorie_id">
                                    <option value="">Selec. Categoria</option>
                                    <template v-for="(categorie, index) in categories" :key="index">
                                        <option :value="categorie.id">{{ categorie.title }}</option>
                                    </template>
                                </b-form-select>
                            </b-col>
                            <b-col lg="2">
                                <label for="state-product" class="col-form-label text-lg-end">Estado: </label>
                                <b-form-radio name="state" @click="state = 0" value="0" :checked="state == 0">Todos</b-form-radio>
                                <b-form-radio name="state" @click="state = 1" value="1" :checked="state == 1">Activo</b-form-radio>
                                <b-form-radio name="state" @click="state = 2" value="2" :checked="state == 2">Inactivo</b-form-radio>
                            </b-col>
                            <b-col lg="2">
                                <label for="type_units_list" class="col-form-label text-lg-end">Unidades: </label>
                                <b-form-select id="type_units_list" v-model="unidad_medida">
                                    <option value="">Selec. Unidad</option>
                                    <template v-for="(unit, index) in units" :key="index">
                                        <option :value="unit.code">{{ unit.name }}</option>
                                    </template>
                                </b-form-select>
                            </b-col>
                            <b-col lg="2" md="2">
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
                                    <b-th>Producto</b-th>
                                    <b-th>Sku</b-th>
                                    <b-th>Categoria</b-th>
                                    <b-th>Precio C.F</b-th>
                                    <b-th>Precio C.E</b-th>
                                    <b-th>Estado</b-th>
                                    <b-th>Unidad</b-th>
                                    <b-th>Incluye IGV</b-th>
                                    <b-th class="text-end">Acción</b-th>
                                </b-tr>
                            </b-thead>
                            <b-tbody>
                                <b-tr v-for="(product, index) in products" :key="index">
                                    <b-td>
                                        <div>
                                            <img :src="product.imagen" style="border-radius: 50%;width: 54px;" alt="">
                                            
                                            <span class="mx-2">{{ product.title }}</span>
                                            
                                        </div>
                                    </b-td>
                                    <b-td>
                                        {{ product.sku }}
                                    </b-td>
                                    <b-td>{{ product.categorie.title }}</b-td>
                                    <b-td>{{ product.price_general }}</b-td>
                                    <b-td>{{ product.price_company }}</b-td>
                                    <b-td>
                                        <b-badge variant="primary" v-if="product.state == 1">Activo</b-badge>
                                        <b-badge variant="danger" v-if="product.state == 2">Inactivo</b-badge>
                                    </b-td>

                                    <b-td>{{ product.unidad_medida }}</b-td>
                                    
                                    <b-td>{{ product.include_igv == 1 ? 'NO' : 'SI' }}</b-td>

                                    <b-td class="text-end">
                                        <a href="#" @click="editProduct(product)"><i class="las la-pen text-secondary fs-22"></i></a>
                                        {{ " " }}
                                        <a href="#" @click="deleteProduct(product)"><i class="las la-trash-alt text-secondary fs-22"></i></a>
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
    </DefaultLayout>
</template>
<script setup lang="ts">
import DefaultLayout from "@/layouts/DefaultLayout.vue";
import type { AxiosResponse } from "axios";
import { onMounted, ref, watch } from "vue";
import HttpClient from "@/helpers/http-client";
import Swal from "sweetalert2/dist/sweetalert2.js";
import { ProductCategorie, UNITS, type Product, type ProductConfigResponse, type ProductResponse, type Products, type ProductUnit } from "@/types/products";
import router from "@/router";
type TVueSwalInstance = typeof Swal & typeof Swal.fire;

const products = ref<Product[]>([]);
const currentPage = ref<number>(1);
const totalPage = ref<number>(0);
const perPageRows = ref<number>(1);

const search = ref<String  | null>(null);
const categorie_id = ref<string>("");
const categories = ref<ProductCategorie[]>([]);
const state = ref<number>(0);
const unidad_medida = ref<string>("");
const units = ref<ProductUnit[]>(UNITS);

const list = async() => {
    try {
       const res: AxiosResponse<Products> = await HttpClient.get(
        `products?page=${currentPage.value}&search=${(search.value ?? '')}
        &categorie_id=${(categorie_id.value ?? '')}
        &state=${state.value ?? ''}
        &unidad_medida=${unidad_medida.value ?? ''}
        `);
      console.log(res);
        products.value = res.data.products.data;
        totalPage.value = res.data.total;
        perPageRows.value = res.data.paginate;
    } catch (error) {
        console.log(error);
    }
}

const config = async() => {
    try {
       const res: AxiosResponse<ProductConfigResponse> = await HttpClient.get(
        `products/config`);
      console.log(res);
        categories.value = res.data.categories;
    } catch (error) {
        console.log(error);
    }
}

const editProduct = (product:Product) => {
    router.push({
        name:'product.edit',
        params: {
            id:product.id,
        }
    })
}
const deleteProduct = (product:Product) => {
    try {
        (Swal as TVueSwalInstance)
            .fire({
            title: "Confirmar la eliminación",
            text: `¿Estas seguro de eliminar este producto '${product.title}'' ?`,
            icon: "warning",
            showCancelButton: true,
            confirmButtonColor: "#3085d6",
            cancelButtonColor: "#d33",
            confirmButtonText: "Si, eliminalo!",
        })
            .then(async (result: any) => {
                if (result.isConfirmed) {
                    const res: AxiosResponse<ProductResponse> = await HttpClient.delete(
                        "products/"+product.id
                    );
                    console.log(res);
                    (Swal as TVueSwalInstance).fire(
                        "Eliminado!",
                        "El producto '"+product.title+"'"+"se ha sido eliminado",
                        "success",
                    );
                    let INDEX = products.value.findIndex(ctg => ctg.id == product.id);
                    if(INDEX != -1){
                        products.value.splice(INDEX,1);
                    }
                }
        });
    } catch (error) {
        console.log(error);
    }
}

const reset = () => {
    search.value = "";
    categorie_id.value = "";
    state.value = 0;
    unidad_medida.value = "";
    list();
}

watch(currentPage,(value) => {
    list();
})
onMounted(() => {
  list();  
  config();
})
</script>