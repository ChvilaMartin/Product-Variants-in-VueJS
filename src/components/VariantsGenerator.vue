<template>
    <div>
        <table class="table table-striped">
            <thead>
                <th>Varianta</th>
                <th>Cena</th>
                <th>Skladem</th>
                <th>EAN</th>
                <th>Varianta existuje</th>
            </thead>
            <tbody>
                <tr v-for="(variant, index) in variantsJSON">
                    <td>
                        <input type="hidden" :name="'variant['+index+'][attributes]'" :value='JSON.stringify(variant)'/>
                        <span v-for="value in variant">{{value.value}} </span>
                    </td>
                    <td><input class="form-control price-formatter" :name="'variant['+index+'][price]'" placeholder="Použít obecnou prodejní cenu"></td>
                    <td><input class="form-control" :name="'variant['+index+'][in_stock]'" type="number" value="0" placeholder="Skladem"></td>
                    <td><input class="form-control" :name="'variant['+index+'][ean]'" type="number" value="0" placeholder="Identifikacni cislo"></td>
                    <td>
                        <div class="checkbox-inline custom-checkbox">
                            <input :id="'variant['+index+'][exists]'" :name="'variant['+index+'][exists]'" type="checkbox" checked>
                            <label :for="'variant['+index+'][exists]'"></label>
                        </div>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    export default {
        props: ['options', 'currencySettings'],
        data: function(){
            return {
                variantsArr: []
            }
        },
        computed: {
            allArrays: function() {
                var allArrays = [];

                for (var option of this.options) {
                    let options = option.value.split(',');
                    let fixedOptions = [];
                    options.forEach(function(el){
                       fixedOptions.push(el.replace(/^\s+|\s+$/g,''));
                    });
                    // TODO: Nechceme mazat uplne vsechny mezery, jen pokud je prvni leading znak

                    //allArrays.push(option.value.split(","));
                    allArrays.push(fixedOptions);
                }
                return allArrays;
            },
            variantsJSON: function() {
                // Generate JSON of combinations for submit
                var helperOptions = this.options;
                function cartesian(arg) {
                    var r = [],  max = arg.length-1;
                    function helper(arr, i) {
                        for (var j=0, l=arg[i].length; j<l; j++) {
                            var a = arr.slice(0); // clone arr
                            a.push(arg[i][j]);
                            if (i==max)
                                r.push(a);
                            else
                                helper(a, i+1);
                        }
                    }
                    helper([], 0);

                    var resultArr = [];
                    for (let i = 0; i < r.length; i++){
                        let step = [];
                        for (let y = 0; y < r[0].length; y++){
                            step.push({
                                attributegroup: {
                                    name: helperOptions[y].name
                                },
                                value: r[i][y]
                            });
                        }
                        resultArr.push(step);
                    }
                    return resultArr;
                }
                return cartesian(this.allArrays);
            }
        },

        mounted: function() {
            var priceInput = $('.price-formatter');
            var decimalSymbol = this.currencySettings.decimalSymbol;
            var currencySymbol = this.currencySettings.currencySymbol;
            var format = this.currencySettings.format;

            priceInput.formatCurrency({ 'decimalSymbol': decimalSymbol, 'symbol' : currencySymbol, 'positiveFormat': format, 'digitGroupSymbol': " "});
            priceInput.blur(function() {
                priceInput.formatCurrency({ 'decimalSymbol': decimalSymbol, 'symbol' : currencySymbol, 'positiveFormat': format, 'digitGroupSymbol': " "});
            });
        },
    }

</script>

<style>
    .variant-disabled-input {
        background-color: transparent;
        border: transparent;
    }
</style>