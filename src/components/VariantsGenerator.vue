<template>
    <div>
        <table class="table table-striped">
            <thead>
                <th class="text-center">Variant</th>
                <th class="text-center">In stock</th>
            </thead>
            <tbody>
                <tr v-for="(variant, index) in variants">
                    <td><input :name="'variant-combination-'+index" class="variant-disabled-input" disabled type="text" :value="variant"></td>
                    <td><input :name="'variant-instock-'+index" type="number" name="in_stock" value="0" :id="index"></td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    export default {
        props: ['options'],
        computed: {
            variants: function () {
                var allArrays = [];

                for (var option of this.options) {
                    allArrays.push(option.value.split(","));
                }

                function getPermutation(array, prefix) {
                    prefix = prefix || '';
                    if (!array.length) {
                        return prefix;
                    }

                    var result = array[0].reduce(function (result, value) {
                        return result.concat(getPermutation(array.slice(1), prefix + " " + value + "; "));
                    }, []);


                    return result;
                }

                return getPermutation(allArrays);
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