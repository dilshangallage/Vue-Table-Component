<template>
    <div id="app">
        <TableComponent
                class="table-wrapper"
                :items="itemsSet1"
                :fields="fieldSet1"
                :tableOptions="tableOptions1"
                @output-change="handleOutput"
        ></TableComponent>
        <TableComponent
                class="table-wrapper"
                :items="itemsSet2"
                :fields="fieldSet2"
                :tableOptions="tableOptions2"
                @output-change="handleOutput"
        ></TableComponent>
        <TableComponent
                class="table-wrapper"
                :items="itemsSet3"
                :fields="fieldSet3"
                :tableOptions="tableOptions3"
                @output-change="handleOutput"
        ></TableComponent>
        <TableComponent
                class="table-wrapper"
                :items="itemsSet4"
                :fields="fieldSet4"
                :tableOptions="tableOptions4"
        ></TableComponent>
    </div>
</template>

<script>
    import TableComponent from 'vue-table-editable';
    import axios from 'axios';

    export default {
        name: 'App',
        data() {
            return {
                itemsSet1: [],
                itemsSet2: [],
                itemsSet3: [],
                itemsSet4: [],
                tableOptions1: {
                    ttAlign: "left",
                    ttText: "Default Editable Table",
                    ttFontsize: "20px",
                    ttColor: "green",
                    rHover: true,
                    tBorder: false,
                    thColor: "none",
                    deleteAlertTitle: "Sure?",
                    editable: true,
                    deletable: false,
                    filterFloats: "right",
                    pgAlign: "right",
                    pgPosition: "bottom",
                    pgPerPage: 5,
                    pgSize: "md"

                },
                tableOptions2: {
                    ttAlign: "center",
                    ttText: "Default Deletable Table",
                    ttFontsize: "25px",
                    ttColor: "red",
                    rHover: true,
                    tBorder: true,
                    thColor: "dark",
                    deleteAlertTitle: "Are you sure?",
                    editable: true,
                    deletable: true,
                    filterFloats: "left",
                    pgAlign: "right",
                    pgPosition: "top",
                    pgPerPage: 4,
                    pgSize: "md"

                },
                tableOptions3: {
                    ttAlign: "left",
                    ttText: "Filtering Editable Table",
                    ttFontsize: "22px",
                    ttColor: "black",
                    rHover: false,
                    tBorder: true,
                    thColor: "light",
                    deleteAlertTitle: "Want to delete?",
                    editable: true,
                    deletable: true,
                    filterFloats: "right",
                    pgAlign: "left",
                    pgPosition: "top",
                    pgPerPage: 10,
                    pgSize: "md"

                },
                tableOptions4: {
                    ttAlign: "center",
                    ttText: "Image Table",
                    ttFontsize: "20px",
                    ttColor: "black",
                    rHover: false,
                    tBorder: true,
                    thColor: "light",
                    deleteAlertTitle: "Want to delete?",
                    editable: false,
                    deletable: true,
                    filterFloats: "right",
                    pgAlign: "left",
                    pgPosition: "top",
                    pgPerPage: 4,
                    pgSize: "md",
                    imgSize: "sm"

                },
                fieldSet1: [
                    {key: "id", label: "ID", type: "text", sortable: true, filterOn: true},
                    {
                        key: "first_name",
                        label: "First Name",
                        type: "text",
                        sortable: true,
                        editable: true
                    },
                    {
                        key: "last_name",
                        label: "Last Name",
                        type: "text",
                        sortable: true,
                        editable: false
                    },
                    {
                        key: "email",
                        label: "Email",
                        type: "decimal",
                        sortable: true,
                        editable: false
                    },
                    {key: 'actions', label: 'Actions', type: "button"}
                ],
                fieldSet2: [
                    {key: "id", label: "ID", type: "text", sortable: true, filterOn: true},
                    {
                        key: "first_name",
                        label: "First Name",
                        type: "text",
                        sortable: true,
                        editable: true
                    },
                    {
                        key: "last_name",
                        label: "Last Name",
                        type: "text",
                        sortable: true,
                        editable: false
                    },
                    {
                        key: "email",
                        label: "Email",
                        type: "decimal",
                        sortable: true,
                        editable: false
                    },
                    {key: 'actions', label: 'Actions', type: "button"}
                ],
                fieldSet3: [
                    {key: "id", label: "ID", type: "text", sortable: true, editable: true},
                    {key: "name", label: "Name", type: "text", sortable: true, filterOn: true},
                    {key: "origin", label: "Origin", type: "text", sortable: true, filterOn: true},
                    {key: "life_span", label: "Life(Years)", type: "text", sortable: true, editable: true},
                    {key: "energy_level", label: "Energy Level", type: "number", sortable: true, editable: true},
                    {key: "social_needs", label: "Social Needs", type: "number", sortable: true,},
                    {key: "health_issues", label: "Health Issues", type: "number", sortable: true, editable: true},
                    {key: 'actions', label: 'Actions', type: "button"}
                ],
                fieldSet4: [
                    {key: "id", label: "ID", type: "text", sortable: true, filterOn: true},
                    {key: "url", label: "Image", type: "img"},
                    {key: 'actions', label: 'Actions', type: "button"}
                ]
            }
        },
        mounted() {
            axios
                .get('https://reqres.in/api/users')
                .then(response => {
                    console.log("2222", response.data.data);
                    this.itemsSet1 = response.data.data;
                });

            axios
                .get('https://reqres.in/api/users')
                .then(response => {
                    console.log("****", response.data.data);
                    this.itemsSet2 = response.data.data;
                });

            axios
                .get('https://api.thecatapi.com/v1/breeds/')
                .then(response => {
                    this.itemsSet3 = response.data;
                });
            axios
                .get('https://api.thedogapi.com/v1/images/search?limit=25&page=10&order=Desc')
                .then(response => {
                    this.itemsSet4 = response.data;
                })
        },
        components: {
            TableComponent
        },
        methods: {
            handleOutput(action, data) {
                console.log("##### Event - Action:", action)
                console.log("##### Event - Data:", data)
            }
        }
    }
</script>

<style scoped>
    .table-wrapper {
        margin: 1rem;
        border: 3px solid #dee2e6;
        border-radius: 0.5rem;
    }
</style>
