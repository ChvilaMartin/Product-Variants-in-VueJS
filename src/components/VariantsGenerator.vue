<template>
    <div>
        <table class="table table-striped">
            <thead>
                <th class="text-center">Variant</th>
                <th class="text-center">In stock</th>
            </thead>
            <tbody>
                <tr v-for="(variant, index) in variants">
                    <td>
                        <span>{{variant}}</span>
                        <input type="hidden" :name="'variant['+index+'][attributes]'" :value="variantsJSON[index]"/>
                    </td>
                    <td><input :name="'variant['+index+'][in_stock]'" type="number" value="0" :id="index"></td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    export default {
        props: ['options'],
        data: function(){
            return {
                variantsArr: [],
            }
        },
        computed: {
            allArrays: function() {
                var allArrays = [];
                for (var option of this.options) {
                    allArrays.push(option.value.split(","));
                }
                return allArrays;
            },
            variants: function () {
                // Generate STRING of combinations for display
                function getPermutation(array, prefix) {
                    prefix = prefix || '';
                    if (!array.length) {
                        return prefix;
                    }

                    var result = array[0].reduce(function (result, value) {
                        return result.concat(getPermutation(array.slice(1), prefix + " " + value + " - "));
                    }, []);

                    return result;
                }
                return getPermutation(this.allArrays);
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
                                name: helperOptions[y].name,
                                value: r[i][y]
                            });
                        }
                        resultArr.push(JSON.stringify(step));
                    }
                    return resultArr;
                }
                return cartesian(this.allArrays);
            }
        }
    }

</script>

<style>
    .variant-disabled-input {
        background-color: transparent;
        border: transparent;
    }
</style>