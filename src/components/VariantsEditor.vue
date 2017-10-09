<template>
    <div>
        <table v-if="updateData.update == true" class="table variants-editor-table">
            <thead>
            <th>Varianta</th>
            <th>Cena</th>
            <th>EAN</th>
            <th v-if="hasStock">Skladem</th>
            </thead>
            <tbody>
            <tr v-for="(variant, index) in data">
                <td>
                    <span v-for="attribute in variant.attributes">{{attribute.value}} </span>
                    <input v-if="variant.id" type="hidden" :name="'variant['+index+'][id]'" :value="variant.id"/>
                    <input type="hidden" :name="'variant['+index+'][attributes]'" :value='JSON.stringify(variant.attributes)'/>
                    <input type="hidden" :name="'variant['+index+'][exists]'" value="on"/>
                </td>
                <td><input class="form-control price-formatter" placeholder="Použít obecnou prodejní cenu" :name="'variant['+index+'][price]'" :value="variant.price"></td>
                <td><input class="form-control" :name="'variant['+index+'][ean]'" type="number" :value="variant.ean"></td>
                <td><input class="form-control" v-if="variant.added" :name="'variant['+index+'][in_stock]'" type="number" :value="variant.in_stock"></td>
                <td>
                    <a v-if="variant.id" class="btn btn-sm btn-primary oc-icon-pencil" :href="updateData.backendUrl + '/' + 'update/' + variant.id"></a>
                    <div class="btn btn-sm btn-danger oc-icon-trash" @click="removeVariant(index)"></div>
                </td>
            </tr>
            <tr v-if="addVariantInput">
                <td v-for="(attrGr, index) in attributeGroups"><input class="form-control"  type="text" :placeholder="attrGr.attributegroup.name" v-model="newAttributeValues[index]"></td>
                <td><input class="form-control" type="text" placeholder="Cena" v-model="newPrice"></td>
                <td><input class="form-control" type="text" placeholder="Skladem" v-model="newInStock"></td>
                <td><input class="form-control" type="text" placeholder="EAN" v-model="newEan"></td>
                <td><div class="btn btn-sm btn-success" @click="addVariant">Přidat</div></td>
            </tr>
            </tbody>
        </table>
        <div class="btn btn-secondary btn-sm oc-icon-plus" @click="addVariantInput = !addVariantInput">Přidat variantu</div>
    </div>
</template>
<script>
    export default {
        props: ['updateData'],
        data: function() {
            return {
                data: this.updateData.variants,
                attributeGroups: this.updateData.variants[0].attributes,
                currencySettings: this.updateData.currencySettings,
                addVariantInput: false,
                newAttributeValues: [],
                newInStock: '',
                newEan: '',
                newPrice: '',
                hasStock: false
            }
        },
        methods: {
            removeVariant: function(index) {
                this.data.splice(index, 1);
            },
            addVariant: function() {
                let obj = {
                    "attributes": [],
                    "ean": this.newEan,
                    "in_stock": this.newInStock,
                    "price": this.newPrice,
                    "exists": true,
                    "added": true
                };
                if (this.newAttributeValues.length === this.attributeGroups.length) {
                    for (let i = 0; i < this.newAttributeValues.length; i++){
                        console.log(this.newAttributeValues[i]);
                        obj.attributes.push({
                            "value": this.newAttributeValues[i],
                            "attributegroup": {
                                "name": this.attributeGroups[i].attributegroup.name
                            },
                        });
                    }
                    this.data.push(obj);
                    this.hasStock = true;
                    this.newEan = 0;
                    this.newInStock = 0;
                    this.newAttributeValues = [];
                    this.addVariantInput = false;
                }
            }
        },
        updated: function() {
            var priceInput = $('.price-formatter');

            priceInput.formatCurrency({ 'decimalSymbol': this.currencySettings.decimalSymbol, 'symbol' : this.currencySettings.currencySymbol, 'positiveFormat': this.currencySettings.format, 'digitGroupSymbol': " "});
            priceInput.blur(function() {
                priceInput.formatCurrency({ 'decimalSymbol': this.currencySettings.decimalSymbol, 'symbol' : this.currencySettings.currencySymbol, 'positiveFormat': this.currencySettings.format, 'digitGroupSymbol': " "});
            });
        }
    }

</script>