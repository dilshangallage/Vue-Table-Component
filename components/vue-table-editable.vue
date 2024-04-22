<template>
    <div class="table-content">
        <!-- Table title -->
        <b-row :style="tableTitleStyle">{{tableOptions.ttText}}</b-row>
        <!-- Filter and Pagination in same row -->
        <b-row :class="computedClass" v-if="combinedRowLeft" class="no-gutters">
            <b-col lg="3">
                <b-input-group size="md">
                    <b-form-input
                            class="filter-field"
                            id="filter-input"
                            v-model="filter"
                            type="search"
                            placeholder="Type to Filter"
                    ></b-form-input>
                </b-input-group>
            </b-col>
            <b-col class="row-content-display">
                <b-pagination
                        :disabled="openEditAction"
                        v-model="currentPage"
                        :total-rows="totalRows"
                        :per-page="tableOptions.pgPerPage"
                        :size="tableOptions.pgSize"
                ></b-pagination>
            </b-col>
        </b-row>
        <!-- Pagination and Filter in same row -->
        <b-row :class="computedClass" v-if="combinedRowRight" class="no-gutters">
            <b-col>
                <b-pagination
                        :disabled="openEditAction"
                        v-model="currentPage"
                        :total-rows="totalRows"
                        :per-page="tableOptions.pgPerPage"
                        :size="tableOptions.pgSize"
                ></b-pagination>
            </b-col>
            <b-col lg="3" class="row-content-display">
                <b-input-group size="md">
                    <b-form-input
                            class="filter-field"
                            id="filter-input"
                            v-model="filter"
                            type="search"
                            placeholder="Type to Filter"
                    ></b-form-input>
                </b-input-group>
            </b-col>
        </b-row>
        <!-- Filter only -->
        <b-row v-if="!combinedRowLeft && !combinedRowRight" :class="computedClass" class="no-gutters">
            <b-col lg="3">
                <b-input-group size="md">
                    <b-form-input
                            class="filter-field"
                            id="filter-input"
                            v-model="filter"
                            type="search"
                            placeholder="Type to Filter"
                    ></b-form-input>
                </b-input-group>
            </b-col>
        </b-row>
        <!-- Pagination only -->
        <b-row v-if="tableOptions.pgPosition==='top' && !combinedRowLeft && !combinedRowRight" :class="PaginationClass">
            <b-pagination
                    :disabled="openEditAction"
                    v-model="currentPage"
                    :total-rows="totalRows"
                    :per-page="tableOptions.pgPerPage"
                    :size="tableOptions.pgSize"
            ></b-pagination>
        </b-row>
        <!-- Bootstrap table -->
        <b-table
                :items="items"
                :fields="fields"
                :filter="filter"
                :filter-included-fields="filterOn"
                :current-page="currentPage"
                :per-page="tableOptions.pgPerPage"
                aria-sort
                @filtered="onFiltered"
                ref="table"
                :bordered="tableOptions.tBorder"
                :hover="tableOptions.rHover"
                :responsive=true
                :head-variant="tableOptions.thColor"
        >
            <!-- Check table filed data types -->
            <template v-for="(field, index) in fields" #[`cell(${field.key})`]="data">
                <b-form-datepicker
                        v-if="field.editable && field.type === 'date' && selectedRowIndex === data.index && selectedCells.includes(field.key)"
                        :key="index" :type="field.type" v-model="items[data.index][field.key]"></b-form-datepicker>
                <b-form-select
                        v-else-if="field.editable && field.type === 'select' && selectedRowIndex === data.index && selectedCells.includes(field.key)"
                        :key="index" v-model="items[data.index][field.key]" :options="field.options"
                        class="form-control"></b-form-select>
                <b-form-input
                        v-else-if="field.editable && field.type && selectedRowIndex === data.index && selectedCells.includes(field.key)"
                        :key="index" :type="field.type" v-model="items[data.index][field.key]"></b-form-input>
                <b-img :key="index" v-else-if="field.type === 'img'" :src="data.value"
                       v-model="items[data.index][field.key]" :style="imageSize"/>
                <span :key="index" v-else>{{data.value}}</span>
            </template>
            <!-- Table action column action buttons -->
            <template #cell(actions)="row">
                <b-button v-if="tableOptions.editable && row.index !== selectedRowIndex" size="md"
                          :disabled="openEditAction"
                          @click="onEdit(row.item, row.index)"
                          class="success-click-btn" variant="success">
                    Edit
                </b-button>
                <b-button v-if="tableOptions.editable && row.index === selectedRowIndex" size="md"
                          @click="onSave(row.item)"
                          class="success-click-btn" variant="success">
                    Save
                </b-button>
                <b-button v-if="tableOptions.editable && row.index === selectedRowIndex" size="md"
                          @click="onCancel()"
                          class="danger-click-btn" variant="danger">
                    Cancel
                </b-button>
                <b-button v-if="tableOptions.deletable && row.index !== selectedRowIndex" size="md"
                          :disabled="openEditAction"
                          @click="deleteRow(row.item, row.index)" class="danger-click-btn"
                          variant="danger">
                    Delete
                </b-button>
                <b-modal id="row.index" ok-only>
                </b-modal>
            </template>
        </b-table>
        <!-- Pagination only -->
        <b-row v-if="tableOptions.pgPosition==='bottom'" :class="PaginationClass">
            <b-pagination
                    :disabled="openEditAction"
                    v-model="currentPage"
                    :total-rows="totalRows"
                    :per-page="tableOptions.pgPerPage"
                    :size="tableOptions.pgSize"
            ></b-pagination>
        </b-row>
    </div>
</template>

<script>
    export default {
        name: 'TableComponent',
        props: {
            /**
             * Properties and Options in Bootstrap b-table
             */
            tableOptions: {
                default: () => {
                    return {
                        ttAlign: "left",
                        ttText: "Default table",
                        ttFontsize: "15px",
                        ttColor: "Black",
                        rHover: true,
                        tBorder: true,
                        thColor: "light",
                        deleteAlertTitle: "Are you sure?",
                        editable: false,
                        deletable: true,
                        filterFloats: "left",
                        pgAlign: "right",
                        pgPosition: "bottom",
                        pgPerPage: 5,
                        pgSize: "md"
                    }
                },
                type: Object
            },
            /**
             * Table items
             */
            items: Array,
            /**
             * Table main header fields
             */
            fields: Array
        },
        watch: {
            /**
             * Watch items get from parent component (For network delays response issue)
             */
            items() {
                this.totalRows = (this.items) ? this.items.length : 0;
            }
        },
        data() {
            return {
                temporyItem: null,
                combinedRowLeft: false,
                combinedRowRight: false,
                combinedRow: false,
                selectedRowIndex: -1,
                showModal: false,
                selectedRow: {},
                selectedCells: [],
                openEditAction: false,
                totalRows: 1,
                currentPage: 1,
                filterOn: [],
                filter: null
            }
        },
        computed: {
            /**
             * Search filter float direction
             * @returns {string} className
             */
            computedClass() {
                let className = (this.tableOptions.filterFloats === "left") ? "item-floating-left" :
                    (this.tableOptions.filterFloats === "right") ? "item-floating-right" : "item-floating-center";
                return className;
            },
            /**
             *  Pagination float direction
             * @returns {string} className
             */
            PaginationClass() {
                let className = (this.tableOptions.pgAlign === "left") ? "item-floating-left" :
                    (this.tableOptions.pgAlign === "right") ? "item-floating-right" : "item-floating-center";
                return className;
            },
            /**
             * Table title css properties
             * @returns {{"margin-left": string, color: *, "font-weight": string, "font-size": *, "justify-content": string}}
             */
            tableTitleStyle() {
                return {
                    "justify-content": (this.tableOptions.ttAlign === "center") ? "center" : "flex-start",
                    "margin-left": "0rem",
                    "font-size": (this.tableOptions.ttFontsize) ? this.tableOptions.ttFontsize : "10px",
                    "font-weight": "bold",
                    "color": (this.tableOptions.ttColor) ? this.tableOptions.ttColor : "black"
                }

            },
            /**
             * Set image size from table options propert imageSize
             * @returns {{md: 110px, sm: 75px, lg: 150px}}
             */
            imageSize() {
                return {
                    "width": (this.tableOptions.imgSize === "md") ? "10rem" : (this.tableOptions.imgSize === "sm") ? "5rem" : "15rem"
                }
            },
        },
        mounted() {
            /**
             * Check Filter and Pagination in same row
             */
            (this.tableOptions.pgAlign === "right" && this.tableOptions.pgPosition === "top" &&
                this.tableOptions.filterFloats === "left") ?
                this.combinedRowLeft = true : this.combinedRowLeft = false;
            /**
             * Check Filter and Pagination in same row
             */
            (this.tableOptions.pgAlign === "left" &&
                this.tableOptions.pgPosition === "top" && this.tableOptions.filterFloats === "right") ?
                this.combinedRowRight = true : this.combinedRowRight = false;
            /**
             * Select Editable columns and Filter on columns
             */
            if (this.fields) {
                if (this.fields.length > 0) {
                    this.filterOn = [];
                    this.selectedCells = [];
                    this.fields.forEach(field => {
                        if (field.filterOn) {
                            this.filterOn.push(field.key);
                        }

                        if (field.editable) {
                            this.selectedCells.push(field.key)
                        }
                    });
                }
            }

        },
        methods: {
            /**
             * Delete row item with alert confirmation
             * @param item
             * @param index
             * @event output-change
             */
            deleteRow(item, index) {
                this.$confirm(this.tableOptions.deleteAlertTitle).then(() => {
                    this.showModal = true;
                    this.items.splice(index, 1);
                    this.$emit('output-change', "delete", item);
                    this.openEditAction = false;
                }, () => {
                });
            },
            /**
             * Filter table rows
             * @param filteredItems
             */
            onFiltered(filteredItems) {
                this.totalRows = filteredItems.length;
                this.currentPage = 1
            },
            /**
             * Allowed to Editing that select table row editable cells and disabled other rows action buttons
             * @param item
             * @param index
             */
            onEdit(item, index) {
                this.temporyItem = {...item};
                this.selectedRowIndex = index;
                this.openEditAction = true;
            },
            /**
             * Save edit row
             * @param item
             * @event output-change
             */
            onSave(item) {
                this.temporyItem = null;
                this.$emit('output-change', "save", item);
                this.selectedRowIndex = -1;
                this.openEditAction = false;
            },
            /**
             * Cancel edit action and enable other action buttons
             */
            onCancel() {
                this.items[this.selectedRowIndex] = this.temporyItem;
                this.$refs.table.refresh();
                this.selectedRowIndex = -1;
                this.openEditAction = false;
            }
        }
    }
</script>

<style scoped>
    .table-content {
        padding: 1rem;
    }

    .filter-field {
        border-radius: 0.3rem !important;
    }

    .item-floating-right {
        justify-content: flex-end;
        margin-bottom: 0.5rem;
        margin-top: 0.5rem;
        margin-right: 0rem;
    }

    .item-floating-center {
        justify-content: center;
        margin-bottom: 0.5rem;
        margin-top: 0.5rem;
    }

    .item-floating-left {
        justify-content: flex-start;
        margin-bottom: 0.5rem;
        margin-top: 0.5rem;
        margin-left: 0rem;
    }

    .success-click-btn {
        margin-left: 1rem;
        width: 4rem;
    }

    .danger-click-btn {
        margin-left: 1rem;
        width: 5rem;
    }

    .row-content-display {
        display: flex;
        justify-content: flex-end;
    }
</style>