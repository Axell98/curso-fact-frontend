<template>
    <DefaultLayout>
        <div class="d-flex flex-wrap justify-space-between gap-4 mb-6">
            <div class="d-flex flex-column justify-center">
                <h4 class="text-h4 mb-1">
                    💰 Add New Nota Electrónica
                </h4>
                <p class="text-body-1 mb-0">
                    Orders placed across your store
                </p>
            </div>
        </div>

        <b-row class="mt-2">
            <b-col lg="5" md="5">
                <b-card no-body :class="{'border-sale': doc_nota == '07'}">
                    <b-card-header class="text-center">
                        <i class="fas fa-file-medical fs-18" ></i>
                        <b-card-title class="py-1">NOTA DE CREDITO</b-card-title>
                        <b-form-radio name="doc_nota" @click="doc_nota = '07'" value="07" checked></b-form-radio>
                    </b-card-header>
                </b-card>
            </b-col>
            <b-col lg="2" md="2">
            </b-col>
            <b-col lg="5" md="5">
                <b-card no-body :class="{'border-sale': doc_nota == '08'}">
                    <b-card-header class="text-center">
                        <i class="fas fa-file-alt fs-18" ></i>
                        <b-card-title class="py-1">NOTA DEBITO</b-card-title>
                        <b-form-radio name="doc_nota" @click="doc_nota = '08'" value="08" ></b-form-radio>
                    </b-card-header>
                </b-card>
            </b-col>
        </b-row>

        <b-card no-body>
            <b-card-header>
                <b-card-title>🔩 Datos Generales</b-card-title>
            </b-card-header>

            <b-card-body class="pt-0">
                <b-row>
                    <b-col lg="2">
                        <label for="is_exportacion-c" class="col-form-label text-lg-end">Exportación: </label>
                        <div class="d-flex">
                            <b-form-radio name="is_exportacion-c"  v-if="sale_selected?.is_exportacion == 0" @click="is_exportacion = 0" value="0" :checked="is_exportacion == 0">No</b-form-radio>
                            {{ " " }}
                            <b-form-radio name="is_exportacion-c" class="mx-2" v-if="sale_selected?.is_exportacion == 1" @click="is_exportacion = 1" value="1" :checked="is_exportacion == 1">Si</b-form-radio>
                            {{ " " }}
                        </div>
                    </b-col>

                    <b-col lg="2" md="3">
                        <label for="n-n_transaction" class="col-form-label text-lg-end">N° de Operación: </label>
                        <b-form-input
                            type="text"
                            id="n-n_transaction"
                            v-model="n_transaction"
                            placeholder="F-001"
                        />
                    </b-col>

                    <b-col lg="2" md="3">
                        <label for="n-f-emision" class="col-form-label text-lg-end">Fecha de Emisión: </label>
                        <b-form-input
                            type="date"
                            id="n-f-emision"
                            v-model="today"
                            placeholder="F-001"
                        />
                    </b-col>

                    <b-col lg="5" md="5">
                        <label for="n-f-clients" class="col-form-label text-lg-end">Clientes: </label>
                        <select id="n-f-clients">
                            <option value="">Selec. Cliente</option>
                            <template v-for="(client, index) in clients" :key="index">
                                <option :value="client.id">{{ client.full_name }}</option>
                            </template>
                        </select>
                        <b v-if="client_selected">{{ client_selected.type_client == 1 ? 'CLIENTE FINAL' : 'CLIENTE EMPRESA' }}</b>
                    </b-col>

                    <b-col lg="2">
                        <label for="retencion-igv-c" class="col-form-label text-lg-end">Retenciones IGV: </label>
                        <div class="d-flex">
                            <b-form-radio name="retencion-igv-c" v-if="sale_selected?.retencion_igv == 0" @click="retencion_igv = 0" value="0" :checked="retencion_igv == 0">Ninguno</b-form-radio>
                            {{ " " }}
                            <b-form-radio name="retencion-igv-c" class="mx-2" v-if="sale_selected?.retencion_igv == 1" @click="retencion_igv = 1" value="1" :checked="retencion_igv == 1">Retención</b-form-radio>
                            {{ " " }}
                            <b-form-radio name="retencion-igv-c" class="mx-2" v-if="sale_selected?.retencion_igv == 2" @click="retencion_igv = 2" value="2" :checked="retencion_igv == 2">Detracción</b-form-radio>
                            {{ " " }}
                            <b-form-radio name="retencion-igv-c" class="mx-2" v-if="sale_selected?.retencion_igv == 3" @click="retencion_igv = 3" value="3" :checked="retencion_igv == 3">Percepción</b-form-radio>
                        </div>
                    </b-col>

                    <b-col lg="1">
                        <div>
                            <label for="is_anticipo-c" class="col-form-label text-lg-end">Anticipo: </label>
                            <div class="d-flex">
                                <b-form-radio name="is_anticipo-c" v-if="sales_anticipos.length == 0" @click="is_anticipo = 0" value="0" :checked="is_anticipo == 0">No</b-form-radio>
                                {{ " " }}
                                <b-form-radio name="is_anticipo-c" v-if="sales_anticipos.length > 0" class="mx-2" @click="is_anticipo = 1" value="1" :checked="is_anticipo == 1">Si</b-form-radio>
                                {{ " " }}
                            </div>
                        </div>
                        <div>
                            <div class="table-responsive m-0" v-if="sales_anticipos.length > 0">
                                <table>
                                    <thead class="table-light">
                                        <th>ANTICIPOS</th>
                                        <th></th>
                                    </thead>
                                    <tbody>
                                        <tr v-for="(sales_anticipo, index) in sales_anticipos" :key="index">
                                            <td>
                                                {{ sales_anticipo.n_operacion }}
                                            </td>
                                            <td>
                                                <!-- <b-button type="button" class="rounded-pill btn btn-danger" @click="removeAnticipo(index)">
                                                    <i class="las la-trash-alt ml-3"></i>
                                                </b-button> -->
                                            </td>
                                        </tr>
                                    </tbody>
                                </table>
                            </div>
                        </div>
                    </b-col>

                    <b-col lg="2">
                        <label for="FB" class="col-form-label text-lg-end">Serie: </label>
                        <b-form-select id="FB" v-model="serie" disabled>
                            <option value="FF01">FF01</option>
                        </b-form-select>
                    </b-col>

                    <b-col lg="4">
                        <b-row>
                            <b-col lg="12" v-if="doc_nota == '07'">
                                <label for="type-nota-electronica" class="col-form-label text-lg-end">Tipo de Nota: </label>
                                <b-form-select id="type-nota-electronica" v-model="type_nota">
                                <option value="01">Anulación de la operación</option>
                                <option value="02">Anulación por error en el RUC</option>
                                <option value="03">Corrección por error en la descripción</option>
                                <option value="04">Descuento global</option>
                                <option value="05">Descuento por ítem</option>
                                <option value="06">Devolución total</option>
                                <option value="07">Devolución por ítem</option>
                                <option value="12">Ajustes afectos al IVAP</option>
                                </b-form-select>
                            </b-col>
                            <b-col lg="12" v-if="doc_nota == '08'">
                                <label for="type-nota-electronica" class="col-form-label text-lg-end">Tipo de Nota: </label>
                                <b-form-select id="type-nota-electronica" v-model="type_nota">
                                    <option value="01">Intereses por mora</option>
                                    <option value="02">Aumento en el valor</option>
                                    <option value="03">Penalidades/ otros conceptos</option>
                                </b-form-select>
                            </b-col>
                        </b-row>
                    </b-col>

                    <b-col lg="3">
                        <label for="description-motivo" class="col-form-label text-lg-end">Descripción Motivo: </label>
                        <b-form-textarea type="textarea" v-model="description_motivo" rows="3" id="description-motivo" />
                    </b-col>
                </b-row>
            </b-card-body>
        </b-card>


        <b-card no-body>
            <b-card-header>
                <b-card-title>📒 Detalle de la Nota</b-card-title>
            </b-card-header>
            <b-card-body class="pt-0">
                <b-row>
                    <b-col lg="12">
                        <div class="table-responsive m-0">
                            <table class="table datatable">
                                <thead class="table-light">
                                    <tr>
                                        <th>Id</th>
                                        <th>Producto</th>
                                        <th>Unidad</th>
                                        <th>Cantidad</th>
                                        <th>Precio Base</th>
                                        <th>Igv</th>
                                        <th>Descuento</th>
                                        <th>Precio Final</th>
                                        <th>SubTotal</th>
                                        <th>Acción</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr v-for="(sale_detail, index) in sale_details" :key="index">
                                        <td>{{ index+1 }}</td>
                                        <td>{{ sale_detail.product.title }}</td>
                                        <td>{{ sale_detail.unidad_medida }}</td>
                                        <td>{{ sale_detail.quantity }}</td>
                                        <td>{{ currency }} {{ sale_detail.price_base }}</td>
                                        <td>{{ currency }} {{ (sale_detail.igv + sale_detail.icbper).toFixed(2) }}</td>
                                        <td>{{ currency }} {{ sale_detail.discount }}</td>
                                        <td>{{ currency }} {{ sale_detail.price_final }}</td>
                                        <td v-if="!['11'].includes(sale_detail.tip_afe_igv)">{{ currency }} {{ sale_detail.subtotal }}</td>
                                        <td v-if="['11'].includes(sale_detail.tip_afe_igv)">{{ currency }} 0 </td>
                                        <td>
                                            <b-button type="button" variant="primary" @click="editSaleDetail(sale_detail)">
                                                <i class="far fa-edit"></i>
                                            </b-button>
                                            <b-button type="button" variant="danger" @click="deleteSaleDetail(index)">
                                                <i class="fas fa-prescription-bottle"></i>
                                            </b-button>
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </b-col>
                    <b-col lg="6" class="mt-2">
                        <table style="width: 90%;">
                            <tr>
                                <td>Igv</td>
                                <td>{{ currency }} {{ getIgvTotal() }}</td>
                            </tr>

                            <tr v-if="icbper_total > 0">
                                <td>Icbper</td>
                                <td>{{ currency }} {{ icbper_total }}</td>
                            </tr>
                            <tr v-if="isc_total > 0">
                                <td>Isc</td>
                                <td>{{ currency }} {{ isc_total }}</td>
                            </tr>
                            
                            
                            <tr v-if="total_anticipos > 0">
                                <td>Total de Anticipos</td>
                                <td>{{ currency }} {{ total_anticipos + igv_discount_general }}</td>
                            </tr>

                            <tr>
                                <td>Descuento</td>
                                <td>{{ currency }} {{ discount_total + discount_global }}</td>
                            </tr>

                            <tr>
                                <td>SubTotal</td>
                                <td>{{ currency }} {{ getSubTotalSale() }}</td>
                            </tr>
                            
                            <tr v-if="total_retencion > 0">
                                <td>Retencion</td>
                                <td>{{ currency }} {{ total_retencion }}</td>
                            </tr>
                            <tr v-if="total_detracion > 0">
                                <td>Detracción</td>
                                <td>{{ currency }} {{ total_detracion }}</td>
                            </tr>
                            <tr v-if="total_percepcion > 0">
                                <td>Percepción</td>
                                <td>{{ currency }} {{ total_percepcion }}</td>
                            </tr>
                            <tr>
                                <td>Total</td>
                                <td>{{ currency }} {{ getTotalSales() }}</td>
                            </tr>
                            
                        </table>
                    </b-col>
                </b-row>
          </b-card-body>

        </b-card>

        <b-card no-body>
          <b-card-body>
            <b-row class="align-items-center justify-content-end">
                <b-col lg="3" md="3">
                    <b-button type="button" variant="primary" @click="store()">
                        <i class="far fa-plus-square ml-3"></i> Generar {{ doc_nota == '07' ? 'Nota de Credito' : 'Nota de Debito' }}
                    </b-button>
                </b-col>
            </b-row>
          </b-card-body>
        </b-card>

        <b-modal
            v-model="ModalEditSaleDetail"
            :title="`🧑 ${sale_detail_selected ? 'Edición' : 'Registro'} del Detallado`"
            :header-class="`bg-${themeColor}`"
            title-class="m-0 text-white"
            :ok-variant="themeColor"
            hide-footer
            centered
            size="md"
            >
            <b-row>
                <b-col lg="10">
                    <label for="name-product" class="col-form-label text-lg-end">Producto: </label>
                    <b-form-input
                        type="text"
                        id="name-product"
                        disabled
                        placeholder="Example: Producto"
                        :value="sale_detail_selected?.product.title"
                    />
                </b-col>

                <b-col lg="6">
                    <label for="name-price-base" class="col-form-label text-lg-end">Precio Base: </label>
                    <b-form-input
                        type="number"
                        id="name-price-base"
                        v-model="price_base_edit"
                        placeholder="Example: 0"
                    />
                </b-col>

                <b-col lg="6">
                    <label for="name-cantidad" class="col-form-label text-lg-end">Cantidad: </label>
                    <b-form-input
                        type="number"
                        id="name-cantidad"
                         v-model="quantity_edit"
                        placeholder="Example: 0"
                    />
                </b-col>

                <!-- <b-col lg="6">
                    <label for="name-discount" class="col-form-label text-lg-end">Descuento: </label>
                    <b-form-input
                        type="number"
                        id="name-discount"
                        v-model="discount_edit"
                        placeholder="Example: 0"
                    />
                </b-col> -->


                <b-col lg="6" v-if="doc_nota == '07' && type_nota == '05'">
                    <label for="name-discount" class="col-form-label text-lg-end">Precio Final: (Descuento X Item)</label>
                    <b-form-input
                        type="number"
                        id="name-discount"
                        v-model="price_final_edit"
                        placeholder="Example: 0"
                    />
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
                        <b-button type="button" variant="primary" @click="updateDetail()">
                            Editar
                        </b-button>
                    </div>
                </b-col>
            </b-row>
        </b-modal>

    </DefaultLayout>
</template>
<script setup lang="ts">
import DefaultLayout from "@/layouts/DefaultLayout.vue";
import type { Client } from "@/types/clients";
import { Sale, SaleDetail, type SaleConfig, type SaleResponse } from "@/types/sales";
import type { AxiosResponse } from "axios";
import { onMounted, ref, watch } from "vue";
import HttpClient from "@/helpers/http-client";
import type { Product } from "@/types/products";
import Selectr from "mobius1-selectr";
import Swal from "sweetalert2/dist/sweetalert2.js";
type TVueSwalInstance = typeof Swal & typeof Swal.fire;

const doc_nota = ref<string>("07");
const is_exportacion = ref<number>(0);
const n_transaction = ref<string>();
const today = ref<Date | string>("");
const is_anticipo = ref<number>(0);
const serie = ref<string>("FF01");
const type_nota = ref<string>("01");
const description_motivo  = ref<string>("");
const currency = ref<string>("S/.");

const sale_selected = ref<Sale | undefined>(undefined);
const client_selected = ref<Client | undefined>(undefined);

const clients = ref<Client[]>([]);
const products = ref<Product[]>([]);

const sales_anticipos = ref<any[]>([]);
const sale_details = ref<SaleDetail[]>([]);

const retencion_igv = ref<number>(0);
const discount_global = ref<number>(0);
const discount_total = ref<number>(0);
const icbper_total = ref<number>(0);
const isc_total = ref<number>(0);
const igv_total = ref<number>(0);
const sale_total = ref<number>(0);
const sale_subtotal = ref<number>(0);
const igv_discount_global = ref<number>(0);
const igv_discount_general = ref<number>(0);
const discount_global_general = ref<number>(0);

const total_retencion = ref<number>(0);
const total_detracion = ref<number>(0);
const total_percepcion = ref<number>(0);
const total_payments = ref<number>(0);
const total_anticipos = ref<number>(0);

const clientSelectr = ref<any>(null);
import { useRoute } from "vue-router";
const route = useRoute("sale.nota");
const sale_id = ref<string | string[]>("");

const price_base_edit = ref<number>(0);
const quantity_edit = ref<number>(0);
const discount_edit = ref<number>(0);
const sale_detail_selected = ref<SaleDetail>();
const ModalEditSaleDetail = ref<boolean>(false);
const product_notes = ref<Product[]>([]);
const themeColor = ref<string>("success");
const price_final_edit = ref<number>(0);

const config = async() => {
    try {
       const res: AxiosResponse<SaleConfig> = await HttpClient.get(
        `sales/config`);
      console.log(res);
        // n_transaction.value = res.data.n_transaction;
        // today.value = res.data.today;
        product_notes.value = res.data.product_notes?.data ?? [];

        if(clients.value.length == 0){
            clients.value = res.data.clients.data;
            setTimeout(() => {
                clientSelectr.value = new Selectr("#n-f-clients");

                show()
            },25)
        }
        
        setTimeout(() => {
            clientSelectr.value.on("selectr.change",(option:any) => {
                console.log(option.value);
                client_selected.value = clients.value.find((client) => client.id == option.value);
            })
        }, 50);

    } catch (error) {
        console.log(error);
    }
}

const editSaleDetail = (sale_detail:SaleDetail) => {
    ModalEditSaleDetail.value = true;
    sale_detail_selected.value = sale_detail;
    price_base_edit.value = sale_detail.price_base;
    quantity_edit.value = sale_detail.quantity;
}
const updateDetail = () => {


    if(!quantity_edit.value || quantity_edit.value == 0){
        (Swal as TVueSwalInstance).fire(
            "Upps!",
            "Necesitas digitar la cantidad del producto",
            "error",
        );
        return;
    }

    // 
    if(type_nota.value == "05" && price_final_edit.value == 0){
        (Swal as TVueSwalInstance).fire(
            "Upps!",
            "Necesitas ingresar el precio final que va tener el producto",
            "error",
        );
        return;
    }
    if(price_final_edit.value > 0){
        price_base_edit.value = Number((price_final_edit.value/1.18).toFixed(5));
    }

    let DISCOUNT_TOTAL = discount_edit.value * quantity_edit.value;

    let tip_afe_igv = Number(sale_detail_selected.value?.tip_afe_igv);
    let TOTAL_VENTA = (quantity_edit.value * price_base_edit.value) - DISCOUNT_TOTAL;
    let ivap = sale_detail_selected.value?.product.is_ivap == 2 ? 0.04 * TOTAL_VENTA : 0;

    let PER_IGV = 0.18;
    if(![10,11].includes(tip_afe_igv)){
        PER_IGV = 0;
    }

    let IGV = 0;
    if(sale_detail_selected.value?.product.is_ivap == 1){
        IGV = TOTAL_VENTA * PER_IGV;
    }else{
        IGV = ivap;
    }
    let percentage_isc = sale_detail_selected.value?.product.percentage_isc ?? 0;
    let isc = TOTAL_VENTA * percentage_isc*0.01;

    if((sale_detail_selected.value?.product.percentage_isc ?? 0) > 0){
        IGV = (TOTAL_VENTA + isc) * PER_IGV;
    }

    let PRECIO_FINAL = Number(((TOTAL_VENTA + IGV + isc)/quantity_edit.value).toFixed(2));

    let icbper = sale_detail_selected.value?.product.is_icbper == 2 ? 0.50 * quantity_edit.value : 0;
    // 
    let INDEX = sale_details.value.findIndex((sale_detail) => sale_detail.id == sale_detail_selected.value?.id);
    if(INDEX != -1){
        sale_details.value[INDEX].price_base = price_base_edit.value;
        sale_details.value[INDEX].price_final = PRECIO_FINAL;
        sale_details.value[INDEX].discount = DISCOUNT_TOTAL;
        sale_details.value[INDEX].quantity = quantity_edit.value;
        // sale_details.value[INDEX].description = description_sale_detail.value;
        sale_details.value[INDEX].igv = Number(IGV.toFixed(2));
        sale_details.value[INDEX].subtotal = Number(TOTAL_VENTA.toFixed(2));
        sale_details.value[INDEX].tip_afe_igv = tip_afe_igv+"";
        sale_details.value[INDEX].per_icbper = sale_detail_selected.value?.product.is_icbper == 1 ? 0.50 : 0;
        sale_details.value[INDEX].icbper = icbper;
        sale_details.value[INDEX].percentage_isc = percentage_isc;
        sale_details.value[INDEX].isc = isc;

        ModalEditSaleDetail.value = false;
        sumDetails();
    }
}

const deleteSaleDetail = (index:number) => {
    sale_details.value.splice(index,1);
    sumDetails();
}

const store = () => {
    
} 

const getIgvTotal = () => {
    return Number((igv_total.value - (igv_discount_general.value)).toFixed(5));
}
const getSubTotalSale = () => {
    return Number((sale_subtotal.value - (discount_global_general.value ?? 0)).toFixed(5));
}
const getTotalSales = () => {
    return Number(((sale_total.value + icbper_total.value + isc_total.value + total_percepcion.value) - 
    (discount_global_general.value + igv_discount_general.value + total_retencion.value + total_detracion.value)).toFixed(5));
}
const getTotalCalc = () => {
    return Number(((sale_total.value + icbper_total.value + isc_total.value) - (discount_global_general.value + igv_discount_global.value)).toFixed(5));
}

const sumDetails = () => {

    console.log(sale_details.value);

    igv_total.value = Number((sale_details.value.filter((sale_detail:any) => ![11].includes(Number(sale_detail.tip_afe_igv)))
    .reduce((sum:number,sale_detail:any) => (sum + sale_detail.igv),0)).toFixed(5));

    icbper_total.value = Number((sale_details.value.filter((sale_detail:any) => ![11].includes(Number(sale_detail.tip_afe_igv)))
    .reduce((sum:number,sale_detail:any) => (sum + sale_detail.icbper),0)).toFixed(5));

    isc_total.value = Number((sale_details.value.filter((sale_detail:any) => ![11].includes(Number(sale_detail.tip_afe_igv)))
    .reduce((sum:number,sale_detail) => (sum + sale_detail.isc),0)).toFixed(5));

    sale_subtotal.value = Number((sale_details.value.filter((sale_detail:any) => ![11].includes(Number(sale_detail.tip_afe_igv)))
    .reduce((sum:number,sale_detail) => sum + sale_detail.subtotal,0)).toFixed(5));

    discount_total.value = Number((sale_details.value.filter((sale_detail:any) => ![11].includes(Number(sale_detail.tip_afe_igv)))
    .reduce((sum:number,sale_detail) => sum + sale_detail.discount,0)).toFixed(5));

    discount_global_general.value = 0;
    igv_discount_general.value = 0;
    igv_discount_global.value = 0;
    if(discount_global.value > 0){
        discount_global_general.value += discount_global.value; 
        igv_discount_global.value = discount_global.value*0.18;
        igv_discount_general.value += igv_discount_global.value;
    }

    total_anticipos.value =  Number(sales_anticipos.value.reduce((sum:number,sal_antic:any) => sum + Number(sal_antic.subtotal),0).toFixed(2));

    if(total_anticipos.value > 0){
        discount_global_general.value += total_anticipos.value;
        igv_discount_general.value +=  Number((total_anticipos.value*0.18).toFixed(2));
    }

    sale_total.value = Number((igv_total.value + sale_subtotal.value).toFixed(5));

    total_retencion.value = 0;
    total_detracion.value = 0;
    total_percepcion.value = 0;
    switch (retencion_igv.value) {
        case 1://RETENCION
            total_retencion.value = Number((getTotalCalc() * 0.03).toFixed(5));
            break;

        case 2://DETRACCION
            total_detracion.value = Number((getTotalCalc() * 0.04).toFixed(5));
            break;

        case 3://PERCEPCION
            total_percepcion.value = Number((getTotalCalc() * 0.04).toFixed(5));
            break;
    
        default:
            break;
    }
}

const show = async() => {
    try {
        
        const res: AxiosResponse<SaleResponse> = await HttpClient.get(
        `sales/${sale_id.value}`);

        console.log(res);

        if(res.data.code == 405){
            (Swal as TVueSwalInstance).fire(
                "Upps!",
                res.data.message,
                "error",
            );
            return;
        }

        sale_selected.value = res.data.sale;

        if(sale_selected.value){

            client_selected.value = sale_selected.value.client;
            
            clientSelectr.value.setValue(sale_selected.value.client.id);
            sale_details.value = sale_selected.value.sale_details;

            retencion_igv.value = sale_selected.value.retencion_igv;
            discount_global.value = sale_selected.value.discount_global;
            // serie.value = sale_selected.value.serie;

            is_exportacion.value = sale_selected.value.is_exportacion;
            currency.value = sale_selected.value.currency;

            today.value = sale_selected.value.created_at_format;
            n_transaction.value = sale_selected.value.n_transaction;
            if(sale_selected.value.sales_anticipos){
                sales_anticipos.value = sale_selected.value.sales_anticipos;
                is_anticipo.value = 1;
            }
            setTimeout(() => {
                sumDetails();
            }, 25);
        }


    } catch (error) {
        console.log(error);
    }
}

watch(type_nota,(value) => {
    price_final_edit.value = 0;
    if(doc_nota.value == '07'){//NOTA DE CREDITO
        if(value == "04"){//descuento global
            sale_details.value = [];
            let PRODUCT_DESC_GLOB = product_notes.value.find((prod) => prod.title.toLocaleLowerCase().indexOf('descuento global') != -1);
            if(PRODUCT_DESC_GLOB){
                sale_details.value.push({
                    product: PRODUCT_DESC_GLOB,
                    unidad_medida: PRODUCT_DESC_GLOB.unidad_medida,
                    price_base: 0,
                    price_final: 0,
                    quantity: 1,
                    discount: 0,
                    igv: 0,
                    subtotal:0,
                    tip_afe_igv: "10",
                    per_icbper: 0,
                    icbper: 0,
                    percentage_isc:0,
                    isc:0,
                });
            }
        }else{
            sale_details.value = sale_selected.value?.sale_details ?? [];
        }
    }else{//NOTA DE DEBITO
        if(value == "01"){
            sale_details.value = [];
            let PRODUCT_DESC_GLOB = product_notes.value.find((prod) => prod.title.toLocaleLowerCase().indexOf('interés por') != -1);
            if(PRODUCT_DESC_GLOB){
                sale_details.value.push({
                    product: PRODUCT_DESC_GLOB,
                    unidad_medida: PRODUCT_DESC_GLOB.unidad_medida,
                    price_base: 0,
                    price_final: 0,
                    quantity: 1,
                    discount: 0,
                    igv: 0,
                    subtotal:0,
                    tip_afe_igv: "10",
                    per_icbper: 0,
                    icbper: 0,
                    percentage_isc:0,
                    isc:0,
                });
            }
        }
        if(value == "02"){
            sale_details.value = sale_selected.value?.sale_details ?? [];
        }
        if(value == "03"){
            sale_details.value = [];
            let PRODUCT_DESC_GLOB = product_notes.value.find((prod) => prod.title.toLocaleLowerCase().indexOf('penalidades') != -1);
            if(PRODUCT_DESC_GLOB){
                sale_details.value.push({
                    product: PRODUCT_DESC_GLOB,
                    unidad_medida: PRODUCT_DESC_GLOB.unidad_medida,
                    price_base: 0,
                    price_final: 0,
                    quantity: 1,
                    discount: 0,
                    igv: 0,
                    subtotal:0,
                    tip_afe_igv: "10",
                    per_icbper: 0,
                    icbper: 0,
                    percentage_isc:0,
                    isc:0,
                });
            }
        }
    }
    sumDetails();
})

watch(doc_nota, (value) => {
    type_nota.value = "01";
    if(value == "08"){//NOTA DE DEBITO
        sale_details.value = [];
        let PRODUCT_DESC_GLOB = product_notes.value.find((prod) => prod.title.toLocaleLowerCase().indexOf('interés por') != -1);
        if(PRODUCT_DESC_GLOB){
            sale_details.value.push({
                product: PRODUCT_DESC_GLOB,
                unidad_medida: PRODUCT_DESC_GLOB.unidad_medida,
                price_base: 0,
                price_final: 0,
                quantity: 1,
                discount: 0,
                igv: 0,
                subtotal:0,
                tip_afe_igv: "10",
                per_icbper: 0,
                icbper: 0,
                percentage_isc:0,
                isc:0,
            });
        }
    }
    if(value == "07"){//NOTA DE CREDITO
        sale_details.value = sale_selected.value?.sale_details ?? [];
    }
})

onMounted(() => {
    sale_id.value = route.params.id;
    config()
})

</script>