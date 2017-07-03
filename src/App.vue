<template>
    <div id="variantsWidgetApp" class="container">
        <div class="rowtext-center" v-if="!update">
            <div class="col-xs-5"><input class="form-control" type="text" name="option-name" v-model="newOptionName"
                                         v-on:keyup.enter="newOptionName = matches[0]"></div>
            <!--<input type="text" id="search" v-model="matches[0]" disabled="true">-->
            <div class="col-xs-5"><input class="form-control" type="text" name="option-value" v-model="newOptionValue"
                                         v-on:keyup.enter="saveNewOption"></div>
            <div class="col-xs-1">
                <a href="#" class="btn btn-primary btn-sm " @click.prevent="saveNewOption">
                    Add
                </a>
            </div>
        </div>
        <br>
        <hr>
        <div v-for="(option, index) in options" class="row m-t text-center">
            <div class="col-xs-5"><input class="form-control" type="text" :name="'options['+index+'][name]'"
                                         v-model="options[index].name"></div>
            <div class="col-xs-5"><input class="form-control col-sm-4" type="text" :name="'options['+index+'][value]'"
                                         v-model="options[index].value"></div>
            <div class="col-xs-1">
                <button class="btn btn-danger btn-sm" @click.prevent="removeOption(index)"> X </button>
            </div>
        </div>
        <br>
        <app-variantsGenerator :options="options" v-if="(options.length > 0 && !update)"></app-variantsGenerator>
        <app-variants-editor v-if="update" :updateData="updateData"></app-variants-editor>
    </div>

</template>

<script>
    import VariantsGenerator from './components/VariantsGenerator.vue';
    import VariantsEditor from './components/VariantsEditor.vue';

    export default {
        name: 'app',
        data () {
            return {
                msg: 'Welcome to Your Vue.js App',
                newOptionName: '',
                newOptionValue: '',
                options: [],
                attributes: [
                    'napeti', 'rozpeti', 'hmotnost', 'barva', 'velikost', 'material'
                ],
                update: false
            }
        },

        methods: {
            saveNewOption() {
                this.options.push({name: this.newOptionName, value: this.newOptionValue});
                this.newOptionName = '';
                this.newOptionValue = '';
            },
            removeOption(index) {
                console.log("pushed!");
                this.options.splice(index, 1);
            }
        },
        computed: {
            matches: function () {

                if (this.newOptionName === "") {
                    return [];
                }

                return this.attributes.filter((str) => {
                    return str.indexOf(this.newOptionName.toLowerCase()) >= 0;
                });
            },

            openSuggestion: function () {
                return this.newOptionName !== "" && this.matches.length !== 0 && this.open === true;
            }
        },
        created: function () {
            var options = [];
            var data = JSON.parse(document.getElementById('updateData').value);
            if (!data.update) {
                return;
            }
            this.updateData = data;
            this.update = true;
        },

        components: {
            appVariantsGenerator: VariantsGenerator,
            appVariantsEditor: VariantsEditor
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

    .vue-text-center {
        text-align: center;
    }


</style>
