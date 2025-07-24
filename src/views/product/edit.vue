<template>
    <DefaultLayout>
        <b-card>
            <b-card-header>
                <b-card-title>🛍️ EDITAR PRODUCTO</b-card-title>
            </b-card-header>

            <b-card-body class="pt-0">
                <b-row>
                    <b-col lg="5" md="5">
                        <label for="name-product" class="col-form-label text-lg-end">Nombre del Producto: </label>
                        <b-form-input
                            type="text"
                            id="name-product"
                            v-model="title"
                            placeholder="Example: Pollos"
                        />
                    </b-col>
                    <b-col lg="4" md="4">
                        <label for="sku-product" class="col-form-label text-lg-end">Sku: </label>
                        <b-form-input
                            type="text"
                            id="sku-product"
                            v-model="sku"
                            placeholder="Example: FE4RFF"
                        />
                    </b-col>
                    <b-col lg="3" md="3">
                        <label for="categorie-product" class="col-form-label text-lg-end">Categoria: </label>
                        <b-form-select id="type_category_list" v-model="categorie_id">
                            <option value="">Selec. Categoria</option>
                            <template v-for="(categorie, index) in categories" :key="index">
                                <option :value="categorie.id">{{ categorie.title }}</option>
                            </template>
                        </b-form-select>
                    </b-col>
                </b-row>
                
                <b-row>
                    <b-col lg="3" md="3">
                        <label for="price-final-product" class="col-form-label text-lg-end">Precio Cliente Final: </label>
                        <b-form-input
                            type="number"
                            v-model="price_general"
                            id="price-final-product"
                            placeholder="Example: 150"
                        />
                    </b-col>
                    <b-col lg="3" md="3">
                        <label for="price-empresa-product" class="col-form-label text-lg-end">Precio Cliente Empresa: </label>
                        <b-form-input
                            type="number"
                            v-model="price_company"
                            id="price-empresa-product"
                            placeholder="Example: 250"
                        />
                    </b-col>
                    <b-col lg="5" md="5">
                        <label for="description-product" class="col-form-label text-lg-end">Descripción: </label>
                        <b-form-textarea type="textarea" v-model="description" rows="5" id="description-product" />
                    </b-col>
                </b-row>

                <b-row>
                    <b-col lg="2" md="2">
                        <label for="is-discount-product" class="col-form-label text-lg-end">Descuento: </label>
                        <div class="d-flex">
                            <b-form-radio name="is_discount" @click="is_discount = 1" value="1" :checked="is_discount == 1" >No</b-form-radio>
                            {{ " " }}
                            <b-form-radio name="is_discount" @click="is_discount = 2" value="2" :checked="is_discount == 2" class="mx-1">Si</b-form-radio>
                        </div>
                    </b-col>
                    <b-col lg="3" md="3" v-if="is_discount == 2">
                        <label for="discount-amount-product" class="col-form-label text-lg-end">Porcentaje: </label>
                        <b-form-input
                            type="number"
                            v-model="max_discount"
                            id="discount-amount-product"
                            placeholder="Example: 60%"
                        />
                    </b-col>
                    <b-col lg="3" md="3">
                        <label for="disponiblidad-product" class="col-form-label text-lg-end">Disponiblidad: </label>
                        <b-form-radio name="disponiblidad" @click="disponiblidad = 1" value="1" :checked="disponiblidad == 1">Vender los productos sin stock</b-form-radio>
                        <b-form-radio name="disponiblidad" @click="disponiblidad = 2" value="2" :checked="disponiblidad == 2">No vender los productos sin stock</b-form-radio>
                    </b-col>

                    <b-col lg="3" md="3">
                        <label for="is_icbper-product" class="col-form-label text-lg-end">Bolsa de Plastico: </label>
                        <b-form-radio name="is_icbper" @click="is_icbper = 1" value="1" :checked="is_icbper == 1">No</b-form-radio>
                        <b-form-radio name="is_icbper" @click="is_icbper = 2" value="2" :checked="is_icbper == 2">Si</b-form-radio>
                    </b-col>

                    <b-col lg="3" md="3">
                        <label for="is_ivap-product" class="col-form-label text-lg-end">Arroz Pilado: </label>
                        <b-form-radio name="is_ivap" @click="is_ivap = 1" value="1" :checked="is_ivap == 1">No</b-form-radio>
                        <b-form-radio name="is_ivap" @click="is_ivap = 2" value="2" :checked="is_ivap == 2">Si</b-form-radio>
                    </b-col>

                    <b-col lg="2" md="3">
                        <label for="is_isc-product" class="col-form-label text-lg-end">ISC: </label>
                        <div class="d-flex">
                            <b-form-radio name="is_isc" @click="is_isc = 0" value="0" :checked="is_isc == 0">No</b-form-radio>
                            <b-form-radio name="is_isc" @click="is_isc = 1" class="mx-1" value="1" :checked="is_isc == 1">Si</b-form-radio>
                        </div>
                    </b-col>
                    <b-col lg="2" md="3" v-if="is_isc == 1">
                        <label for="isc-amount-product" class="col-form-label text-lg-end">Porcentaje: </label>
                        <b-form-input
                            type="number"
                            v-model="percentage_isc"
                            id="isc-amount-product"
                            placeholder="Example: 60%"
                        />
                    </b-col>
                    <b-col lg="2" md="3">
                        <label for="is_especial_nota-product" class="col-form-label text-lg-end">¿Para Nota Electronica?: </label>
                        <div class="d-flex">
                            <b-form-radio name="is_especial_nota" @click="is_especial_nota = 0" value="0" :checked="is_especial_nota == 0">No</b-form-radio>
                            <b-form-radio name="is_especial_nota" @click="is_especial_nota = 1" class="mx-1" value="1" :checked="is_especial_nota == 1">Si</b-form-radio>
                        </div>
                    </b-col>

                    <b-col lg="1" md="1">
                        <label for="include_igv-product" class="col-form-label text-lg-end">Incluye IGV: </label>
                        <b-form-radio name="include_igv" @click="include_igv = 1" value="1" :checked="include_igv == 1">No</b-form-radio>
                        <b-form-radio name="include_igv" @click="include_igv = 2" value="2" :checked="include_igv == 2">Si</b-form-radio>
                    </b-col>
                    <b-col lg="4" md="2">
                        <label for="price_base_igv" class="col-form-label text-lg-end">Precio Base C.F: </label>
                        S/. <span>{{ getPriceBaseCF() }}</span>
                        <br>
                        <label for="price_base_igv" class="col-form-label text-lg-end">Precio Base C.E: </label>
                        S/. <span>{{ getPriceBaseCE() }}</span>
                    </b-col>

                </b-row>

                <b-row>
                    <b-col lg="3" md="3">
                        <label for="units-product" class="col-form-label text-lg-end">Unidades: </label>
                        <b-form-select id="type_units_list" v-model="unidad_medida">
                            <option value="">Selec. Unidad</option>
                            <template v-for="(unit, index) in units" :key="index">
                                <option :value="unit.code">{{ unit.name }}</option>
                            </template>
                        </b-form-select>
                    </b-col>

                    <b-col lg="3" md="3">
                        <label for="stock-product" class="col-form-label text-lg-end">Stock: </label>
                        <b-form-input
                            type="number"
                            id="stock-product"
                            v-model="stock"
                            placeholder="Example: 5"
                        />
                    </b-col>
                    <b-col lg="5">
                        <label for="imagen-product" class="col-form-label text-lg-end">Imagen: </label>
                        <b-input-group class="mb-3">
                            <b-form-file @change="loadFile($event)"  />
                            <b-input-group-text>Upload</b-input-group-text>
                        </b-input-group>

                        <img v-if="IMAGEN_PREVIZUALIZA" :src="IMAGEN_PREVIZUALIZA" alt="" width="150px" class="rounded d-block mx-auto" />
                    </b-col>


                    <b-col lg="12" class="mt-3">
                        <div class="modal-footer">
                            <b-button type="button" variant="primary" @click="store">
                                Editar
                            </b-button>
                        </div>
                    </b-col>
                </b-row>
            </b-card-body>
        </b-card>
    </DefaultLayout>
</template>
<script setup lang="ts">
import DefaultLayout from "@/layouts/DefaultLayout.vue";
import { onMounted, ref } from "vue";
import Swal from "sweetalert2/dist/sweetalert2.js";
import { UNITS, type Product, type ProductCategorie, type ProductConfigResponse, type ProductResponse, type ProductUnit } from "@/types/products";
import type { AxiosResponse } from "axios";
type TVueSwalInstance = typeof Swal & typeof Swal.fire;
import HttpClient from "@/helpers/http-client";
import { useRoute } from "vue-router";

const title = ref<string>("");
const sku = ref<string>("");
const categorie_id = ref<string>("");
const categories = ref<ProductCategorie[]>([]);
const price_general = ref<number>(0);
const price_company = ref<number>(0);
const description = ref<string>("");
const is_discount = ref<number>(1);
const max_discount = ref<number>(0);
const disponiblidad = ref<number>(1);
const is_icbper = ref<number>(0);
const is_ivap = ref<number>(0);
const is_isc = ref<number>(0);
const percentage_isc = ref<number>(0);
const is_especial_nota = ref<number>(0);
const include_igv = ref<number>(1);
const units = ref<ProductUnit[]>(UNITS);
const unidad_medida = ref<string>("NIU");
const stock = ref<number>(0);
const IMAGEN_PREVIZUALIZA = ref<string | ArrayBuffer | null>(null);
const FILE_IMAGEN = ref<File | undefined>(undefined);

const product_selected = ref<Product | undefined>(undefined);

const route = useRoute("product.edit");

const getPriceBaseCF = () => {
    return include_igv.value == 1 ? price_general.value : Number((price_general.value/1.18).toFixed(6));
}
const getPriceBaseCE = () => {
    return include_igv.value == 1 ? price_company.value : Number((price_company.value/1.18).toFixed(6));
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

const store = async() => {
    try {
        
        if(!title.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas digitar el nombre del producto",
                "error",
            );
            return;
        }

        if(!sku.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas digitar el sku del producto",
                "error",
            );
            return;
        }

        if(!categorie_id.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas seleccionar una categoria del producto",
                "error",
            );
            return;
        }

        if(!unidad_medida.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas seleccionar una unidad del producto",
                "error",
            );
            return;
        }
        if(!description.value){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas digitar la descripción del producto",
                "error",
            );
            return;
        }

        if(is_discount.value == 2 && max_discount.value == 0){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas digitar un descuento para el producto",
                "error",
            );
            return;
        }
        
        if(is_isc.value == 2 && percentage_isc.value <= 0){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas digitar un porcentaje ISC",
                "error",
            );
            return;
        }

        // if(!FILE_IMAGEN.value){
        //     (Swal as TVueSwalInstance).fire(
        //         "Upps!",
        //         "Necesitas poner una imagen producto",
        //         "error",
        //     );
        //     return;
        // }

        if(price_general.value < 0){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas digitar un precio positivo del cliente final",
                "error",
            );
            return;
        }

        if(price_company.value < 0){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas digitar un precio positivo del cliente empresa",
                "error",
            );
            return;
        }

        if(stock.value < 0){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                "Necesitas digitar el stock positivo para el producto",
                "error",
            );
            return;
        }


        let formData = new FormData();
        formData.append("title",title.value);
        formData.append("sku",sku.value);
        formData.append("categorie_id",categorie_id.value);
        formData.append("price_general",price_general.value+"");
        formData.append("price_company",price_company.value+"");
        formData.append("description",description.value);
        formData.append("is_discount",is_discount.value+"");
        formData.append("max_discount",max_discount.value+"");
        formData.append("disponiblidad",disponiblidad.value+"");
        formData.append("include_igv",include_igv.value+"");
        formData.append("is_icbper",is_icbper.value+"");
        formData.append("is_ivap",is_ivap.value+"");
        formData.append("unidad_medida",unidad_medida.value);
        formData.append("stock",stock.value+"");
        formData.append("percentage_isc",percentage_isc.value+"");
        formData.append("is_especial_nota",is_especial_nota.value+"");
        if(FILE_IMAGEN.value){
            formData.append("image",FILE_IMAGEN.value);
        }


        const resp: AxiosResponse<ProductResponse> = await HttpClient.post("products/"+route.params.id,formData);

        console.log(resp);

        if(resp.data.code == 405){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                resp.data.message,
                "error",
            );    
        }else{
            (Swal as TVueSwalInstance).fire(
                "Genial!",
                resp.data.message,
                "success",
            );  
        }
    } catch (error:any) {
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

const resetData = () => {
    categorie_id.value = "";
    unidad_medida.value = "";
    title.value = "";
    sku.value = "";
    price_general.value = 0;
    price_company.value = 0;
    description.value = "";
    is_discount.value = 1;
    max_discount.value = 0;
    // is_gift.value = 1;
    include_igv.value = 1;
    is_icbper.value = 1;
    is_ivap.value = 1;
    is_isc.value = 1;
    percentage_isc.value = 0;
    is_especial_nota.value = 1;

    disponiblidad.value = 1;
    stock.value = 0;
    FILE_IMAGEN.value = undefined;
    IMAGEN_PREVIZUALIZA.value = null; 
}

const show = async () => {
    try {
        
        const res: AxiosResponse<ProductResponse> = await HttpClient.get(
        `products/${route.params.id}`);

        console.log(res);
        if(res.data.code == 405){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                res.data.message,
                "error",
            );
            return;
        }

        product_selected.value = res.data.product;
        if(product_selected.value){
            title.value = product_selected.value?.title ?? '';
            sku.value = product_selected.value?.sku ?? '';
            categorie_id.value = product_selected.value?.categorie_id+"";
            unidad_medida.value = product_selected.value?.unidad_medida ?? '';
            price_general.value = product_selected.value.price_general;
            price_company.value = product_selected.value.price_company;
            description.value = product_selected.value.description;
            is_discount.value = product_selected.value.is_discount;
            max_discount.value = product_selected.value.max_discount;
            include_igv.value = product_selected.value.include_igv;
            is_icbper.value = product_selected.value.is_icbper;
            is_ivap.value = product_selected.value.is_ivap;
            disponiblidad.value = product_selected.value.disponiblidad;
            stock.value = product_selected.value.stock;
            IMAGEN_PREVIZUALIZA.value = product_selected.value.imagen; 
            percentage_isc.value = product_selected.value.percentage_isc; 
            is_isc.value = product_selected.value.percentage_isc > 0 ? 1 : 0; 
            is_especial_nota.value = product_selected.value.is_especial_nota;
        }

    } catch (error) {
        console.log(error);
    }
}

onMounted(() => {
  config() ;
  show();
})
</script>