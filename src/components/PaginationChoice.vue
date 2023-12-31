<template>
    <!-- Template Pagination Choice : Used to select one or multiple specific elements in a paginated API request (for example the users's pagination or the scenarios' list) -->
    <div
        v-show="obj.showing"
        id="pagination-parent"
        class="z-50 flex flex-col justify-center fixed top-0 left-0 w-screen h-screen bg-black/[0.4]"
    >
        <div 
            class="p-1 flex flex-col min-w-[50vw] min-h-[50vh] md:max-w-[70vw] max-w-[98vw] md:max-h-[70vh] max-h-[98vh] shadow-lg border border-1 border-black/[0.1] mx-auto rounded-lg"
            :class="darkMode ? 'bg-gray-700' : 'bg-white'"
        >
            <!-- POPUP -->
            <div class="flex flex-col grow min-h-0">
                <div class="flex">
                    <!-- Pagination title -->
                    <h1 class="text-indigo-600 font-extrabold text-2xl mx-2">
                        {{ title }}
                    </h1>
                    <!-- refresh button (Desktop) -->
                    <span class="refresh-btn border border-2 rounded border-indigo-600 bg-indigo-600 my-auto ml-auto md:block hidden cursor-pointer shadow-md">
                        <svg
                            xmlns="http://www.w3.org/2000/svg"
                            class="h-6 w-6 text-white"
                            fill="none"
                            viewBox="0 0 24 24"
                            stroke="currentColor"
                            stroke-width="2"
                        >
                            <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"
                            />
                        </svg>
                    </span>
                </div>
                <div class="flex justify-between my-2">
                    <!-- CONTROLS -->
                    <!-- Left button -->
                    <div
                        class="w-full disabled:opacity-50"
                        @click="prevPage();"
                    >
                        <svg
                            xmlns="http://www.w3.org/2000/svg"
                            class="shadow h-8 w-8 cursor-pointer text-indigo-600 m-auto border border-1 border-indigo-600 rounded-lg"
                            fill="none"
                            viewBox="0 0 24 24"
                            stroke="currentColor"
                            stroke-width="2"
                        >
                            <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                d="M15 19l-7-7 7-7"
                            />
                        </svg>
                    </div>
                    <!-- Center text ([page number] out of [max page]) -->
                    <div class="w-full">
                        <p
                            id="pagination-state"
                            class="m-auto w-fit"
                        >
                            {{ obj.pageNumber }} / {{ obj.pageNbr_max }}
                        </p>
                    </div>
                    <!-- Right button -->
                    <div
                        class="w-full disabled:opacity-50"
                        @click="nextPage();"
                    >
                        <svg
                            xmlns="http://www.w3.org/2000/svg"
                            class="shadow h-8 w-8 cursor-pointer text-indigo-600 m-auto border border-1 border-indigo-600 rounded-lg"
                            fill="none"
                            viewBox="0 0 24 24"
                            stroke="currentColor"
                            stroke-width="2"
                        >
                            <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                d="M9 5l7 7-7 7"
                            />
                        </svg>
                    </div>
                </div>
                <!-- Pagination's page current content -->
                <div
                    class="flex flex-col overflow-x-hidden overflow-y-scroll grow border border-1 border-black/[0.1] rounded-lg min-h-0"
                    :class="darkMode ? 'bg-gray-800' : 'bg-indigo-50'"
                >
                    <div
                        v-for="el in obj.paginationContent[obj.pageNumber]"
                        :key="el"
                        class="flex px-2 py-1 select-none space-x-2 cursor-pointer"
                        :class="darkMode ? 'hover:bg-gray-900' : 'hover:bg-indigo-200'"
                        @click="el.selected = !el.selected"
                    >
                        <p
                            class="flex grow text-base"
                            :class="darkMode ? 'text-gray-200' : 'text-black'"
                        >
                            {{ el.label }}
                        </p> <!-- item label (generated with the displayAttribute callback) -->
                        <div v-show="el.selected">
                            <!-- item selected icon -->
                            <svg
                                xmlns="http://www.w3.org/2000/svg"
                                class="h-6 w-6 text-indigo-600"
                                fill="none"
                                viewBox="0 0 24 24"
                                stroke="currentColor"
                                stroke-width="2"
                            >
                                <path
                                    stroke-linecap="round"
                                    stroke-linejoin="round"
                                    d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"
                                />
                            </svg>
                        </div>
                        <div
                            v-show="!el.selected"
                            class="h-6 w-6"
                        /> <!-- item unselected gap -->
                    </div>
                </div>
            </div>
            <div class="flex grow-0 m-1 mt-2 justify-between">
                <!-- BUTTONS -->
                <!-- cancel button -->
                <button-text @click="hide();">
                    {{ User.LANGUAGE.DATA.ACTIONS.CANCEL }}
                </button-text>
                <!-- refresh button (Mobile) -->
                <span class="refresh-btn border border-2 rounded border-indigo-600 bg-indigo-600 m-auto md:hidden block shadow-md">
                    <svg
                        xmlns="http://www.w3.org/2000/svg"
                        class="h-6 w-6 text-white"
                        fill="none"
                        viewBox="0 0 24 24"
                        stroke="currentColor"
                        stroke-width="2"
                    >
                        <path
                            stroke-linecap="round"
                            stroke-linejoin="round"
                            d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"
                        />
                    </svg>
                </span>
                <!-- validation button -->
                <button-block @click="validate();">
                    {{ User.LANGUAGE.DATA.ACTIONS.VALIDATE }}
                </button-block>
            </div>
        </div>
    </div>
</template>

<script>
import API from '../script/API';
import User from '../script/User';
import ButtonBlock from '../components/inputs/ButtonBlock.vue';
import ButtonText from '../components/inputs/ButtonText.vue';

/**
 * Updates the pagination's content based on the page number
 * @param {any} self current PaginationChoice object
 * @param {boolean} forceRefresh if true, the pagination's content is refreshed even if it has already been loaded once
 */
function updatePagination(self, forceRefresh=false)
{
    if (!self.obj.paginationContent[self.obj.pageNumber] || forceRefresh) {
        // execute new API request to load the pagination's content
        API.execute_logged(self.route+API.createPagination(self.obj.pageNumber, self.obj.pageLength), API.METHOD.GET, User.currentUser.getCredentials(), undefined, API.TYPE.JSON).then(res => {
            self.obj.paginationContent[self.obj.pageNumber] = []; // create a new array for the current page
            self.obj.pageNbr_min = 1;
            self.obj.pageNumber = res.current_page; // update the current page
            self.obj.pageNbr_max = res.last_page; // update the last page
            if (!res.data) return; // problem ? return.
            // for each element, use the identifier callback to check if it is selected or not
            // and if not, add it to the current pagination content
            res.data.forEach(el => {
                if (! (self.identifier(el) in self.selectedValues))
                    self.obj.paginationContent[self.obj.pageNumber].push({
                        label: self.displayAttribute(el), // use the displayAttribute callback to generate the label
                        infos: el, // raw element to return when the item is selected
                        selected: self.identifier(el) in self.selectedValues // check if the item is selected to display the checkbox
                    });
            });
        }).catch(err => { // display error message in the pagination content
            self.obj.paginationContent[self.obj.pageNumber] = [{label: User.LANGUAGE.DATA.PAGINATION.LOADING_ERROR+" : "+err.status, infos: null, selected: false}];
        });
    }
}

/** object representing the current pagination window */
class PaginationController {
    showing = false;
    paginationContent = [];
    pageNumber = 1;
    pageLength = 10;
    pageNbr_min = 1;
    pageNbr_max = 1;
}

export default {
    name: "PaginationChoice",
    components: {
        ButtonBlock,
        ButtonText
    },
    props: {
        displayAttribute: {
            type: Function,
            required: false,
            default: () => {}
        },
        identifier: {
            type: Function,
            required: false,
            default: () => {}
        },
        selectedValues: {
            type: Object,
            required: false,
            default: () => ({})
        },
        callback: {
            type: Function,
            required: false,
            default: () => {}
        },
        route: {
            type: String,
            required: false,
            default: ""
        },
        selectID: {
            type: String,
            required: false,
            default: ""
        },
        title: {
            type: String,
            required: false,
            default: ""
        },
        darkMode: {
            type: Boolean,
            required: false
        }
    },
    data: () => {return {obj: new PaginationController(), User}},
    mounted: function() {
        // on enter pressed, validate the selection
        window.addEventListener("keydown", ev => {
            if (!this.obj.showing) return;
            if (ev.key == "Enter") this.validate();
        });

        // if one of the refresh buttons are clicked, refresh the current page (triggers the spin animation for one second)
        document.querySelectorAll(".refresh-btn").forEach(el => {
            el.addEventListener('click', ev => {
                if (!ev.target.childNodes[0]) return;
                if (ev.target.childNodes[0].classList.contains("animate-spin")) return; // Prevent multiple refresh
                updatePagination(this, true);
                ev.target.childNodes[0].classList.add("animate-spin");
                setTimeout(() => {
                    ev.target.childNodes[0].classList.remove("animate-spin");
                }, 1010);
            });
        });
    },
    methods: {
        show() {
            // if show is called, display the pagination and if not loaded, load the current page (usually the first one)
            this.obj.showing = true;
            updatePagination(this);
        },
        hide() {
            this.obj.showing = false;
            const obj = document.querySelector(this.selectID);
            if (obj != null) obj.value = obj.children[0].value;
        },
        validate() {
            let selectedContent = [];
            this.obj.paginationContent.forEach(list => {
                // add all the selected objects to the selectedContent array
                list.forEach(el => {
                    if (el.selected && el.infos != null) selectedContent.push(el.infos);
                });
            });
            // call the selectedContent callback with the selectedContent array
            this.callback(selectedContent);
            this.hide();
        },
        nextPage() {
            // increases the page number (clamped to max page number) and updates the pagination
            this.obj.pageNumber = Math.min(this.obj.pageNumber + 1, this.obj.pageNbr_max);
            updatePagination(this, false);
        },
        prevPage() {
            // decreases the page number (clamped to min page number) and updates the pagination
            this.obj.pageNumber = Math.max(this.obj.pageNumber - 1, this.obj.pageNbr_min);
            updatePagination(this, false);
        }
    }
}
</script>