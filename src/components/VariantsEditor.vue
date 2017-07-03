<template>
    <div>
        <table v-if="updateData.update == true" class="table">
            <thead>
            <th>Variant</th>
            <th>Price</th>
            <th>EAN</th>
            <th v-if="hasStock">In Stock</th>
            </thead>
            <tbody>
            <tr v-for="(variant, index) in data">
                <td>
                    <span v-for="attribute in variant.attributes">{{attribute.value}} </span>
                    <input v-if="variant.id" type="hidden" :name="'variant['+index+'][id]'" :value="variant.id"/>
                    <input type="hidden" :name="'variant['+index+'][attributes]'" :value='JSON.stringify(variant.attributes)'/>
                    <input type="hidden" :name="'variant['+index+'][exists]'" value="on"/>
                </td>
                <td><input class="form-control" :name="'variant['+index+'][price]'" type="number" :value="variant.price"></td>
                <td><input class="form-control" :name="'variant['+index+'][ean]'" type="number" :value="variant.ean"></td>
                <td><input class="form-control" v-if="variant.added" :name="'variant['+index+'][in_stock]'" type="number" :value="variant.in_stock"></td>
                <td><div class="btn btn-sm btn-danger oc-icon-trash" @click="removeVariant(index)"></div></td>
            </tr>
            <tr v-if="addVariantInput">
                <td v-for="(attrGr, index) in attributeGroups"><input class="form-control"  type="text" :placeholder="attrGr.attributegroup.name" v-model="newAttributeValues[index]"></td>
                <td><input class="form-control" type="text" placeholder="in stock" v-model="newInStock"></td>
                <td><input class="form-control" type="text" placeholder="ean" v-model="newEan"></td>
                <td><div class="btn btn-sm btn-success" @click="addVariant">Add</div></td>
            </tr>
            </tbody>
        </table>
        <br>
        <div class="btn btn-sm" @click="addVariantInput = !addVariantInput">Add variant</div>
    </div>
</template>
<script>
    export default {
        props: ['updateData'],
        data: function() {
            return {
                data: this.updateData.variants,
                attributeGroups: this.updateData.variants[0].attributes,
                addVariantInput: false,
                newAttributeValues: [],
                newInStock: '',
                newEan: '',
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
                    "exists": true,
                    "added": true
                };
                for (let i = 0; i < this.newAttributeValues.length; i++){
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
        },
    }

</script>