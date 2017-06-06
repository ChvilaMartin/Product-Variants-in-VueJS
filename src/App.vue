<template>

    <div id="app" class="container">
        <h1 class="text-center">
            VariantWidget
        </h1>

        <p>
            <b>Naseptavam: </b> <span v-for="att in attributes"><i>{{att}} </i></span> (toto budem tahat ze serveru)
        </p>
        <div style="position: relative">
            <input type="text" name="option-name" v-model="newOptionName" id="namesearch" v-on:keyup.enter="newOptionName = matches[0]">
            <input type="text" id="search" v-model="matches[0]" disabled="true">
            <input type="text" id="value" name="option-value" v-model="newOptionValue" v-on:keyup.enter="saveNewOption">
            <button class="btn btn-primary btn-sm" id="saveBtn" @click.prevent="saveNewOption">Save</button>
        </div>
        <br>
        <hr>

        <div v-for="(option, index) in options">
            <input type="text" :name="'option-name-' + index" v-model="options[index].name">
            <input type="text" :name="'option-value-' + index" v-model="options[index].value">
            <button class="btn btn-danger btn-sm" @click.prevent="removeOption(index)">Remove</button>
        </div>

        <hr>
        <app-variants :options="options"></app-variants>
    </div>

</template>

<script>
    import Variants from './components/VariantsGenerator.vue';
    export default {
        name: 'app',
        data () {
            return {
                msg: 'Welcome to Your Vue.js App',
                newOptionName: '',
                newOptionValue: '',

                options: [
                    {name: "Barva", value: "Cervena, Hneda, Modra"},
                    {name: "Velikost", value: "M, L, XL"},
                    {name: "Material", value: "Bavlna, Latka"}
                ],

                attributes: [
                    'Napeti', 'Rozpeti', 'Hmotnost', 'Barva', 'Velikost', 'Material'
                ]
            }
        },

        methods: {
            saveNewOption() {
                this.options.push({name: this.newOptionName, value: this.newOptionValue});
                this.newOptionName = '';
                this.newOptionValue = '';
            },
            removeOption(index) {
                this.options.splice(index, 1);
            }
        },
        computed: {
            matches: function () {

                if (this.newOptionName === "") {
                    return [];
                }

                return this.attributes.filter((str) => {
                    return str.indexOf(this.newOptionName) >= 0;
                });
            },

            openSuggestion: function () {
                return this.newOptionName !== "" && this.matches.length !== 0 && this.open === true;
            }
        },
        components: {
            appVariants: Variants
        }

    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }

    /* RECREATE TO SOMETHING -NONBREAKING- */
    #namesearch {
        position: absolute;
        left: 22em;
        top: 0px;
        background-color: transparent;
        z-index: 10;
    }

    #search {
        position: absolute;
        left: 22em;
        top: 0px;
        color: #a8a8a8;
        z-index: 0;
    }

    #search:disabled {
        background-color: #fff;
    }

    #value {
        position: absolute;
        left: 35em;
        top: 0px;
    }

    #saveBtn {
        position: absolute;
        left: 55em;
        top: 2px;
    }


</style>
