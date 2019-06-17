<template>
    <core-typeahead
        v-bind="$attrs"
        v-on="$listeners"
        ref="typeahead">
        <template v-slot:default="{
                query, label, items, loading, hasError, currentIndex, highlight,
                clearBindings, inputBindings, inputEvents, itemEvents,
            }">
            <dropdown class="typeahead"
                :disabled="!query"
                manual>
                <template v-slot:trigger="{ open }">
                    <!-- <div class="control has-icons-left has-icons-right" -->
                    <div class=" relative ltr:text-left rtl:text-right flex content-center"
                        :class="{ 'is-loading': loading }">
                        <!-- <input class="input is-fullwidth" -->
                        <!-- TODO: add Diffrent colored shadow ? -->
                        <input class=" px-10  w-full block bg-white h-9
                        border border-gray-860 shadow-inner focus:shadow-outline hover:border-gray-500 hover:shadow text-base"
                            :class="[{ 'is-rounded rounded-full': isRounded }, { 'is-danger border-red-500': hasError }]"
                            type="text"
                            :disabled="disabled"
                            :placeholder="i18n(placeholder)"
                            @keyup="open"
                            v-bind="inputBindings"
                            v-on="inputEvents">
                        <!-- <span class="icon is-small is-left"> -->
                        <span class="items-center inline-flex justify-center
                        absolute top-0 ltr:left-0 rtl:right-0
                        text-gray-500 w-10 h-9">
                            <fa icon="search"/>
                        </span>
                        <!-- <span class="icon is-small is-right clear-button" -->
                        <span class="items-center inline-flex justify-center
                        absolute top-0 rtl:left-0 ltr:right-0
                        focus:text-gray-480 text-gray-500 w-10 h-9"
                            v-on="clearBindings"
                            v-if="query && !loading">
                            <a class="delete is-small"/>
                        </span>
                    </div>
                </template>
                <template v-slot:controls>
                    <slot name="controls"
                        :items="items"/>
                </template>
                <template v-slot:options>
                    <!-- <a class="dropdown-item option" -->
                    <a class="dropdown-item option ltr:text-left rtl:text-right
                                rounded-sm block py-2 px-3 whitespace-no-wrap w-full
                                cursor-pointer no-underline relative text-sm hover:bg-white-smoke"
                        v-for="(item, index) in items"
                        :key="index"
                        :class="{ 'is-active bg-blue-500 hover:bg-blue-400 font-semibold text-white': index === currentIndex }"
                        v-on="itemEvents(index)">
                        <slot name="option"
                            :highlight="highlight"
                            :item="item"
                            :label="label">
                            <span v-html="highlight(item[label])"/>
                        </slot>
                    </a>
                    <template v-if="!items.length">
                        <!-- <a class="dropdown-item" -->
                        <a class="dropdown-item  ltr:text-left rtl:text-right rounded-sm block py-2 px-3
                                whitespace-no-wrap w-full cursor-pointer no-underline relative text-sm hover:bg-white-smoke"
                            v-if="loading">
                            <span v-if="loading">
                                {{ i18n(searching) }}
                            </span>
                        </a>
                        <!-- <a class="dropdown-item" -->
                        <a class="dropdown-item  rounded-sm block py-2 px-3 whitespace-no-wrap w-full ltr:text-left rtl:text-right
                                cursor-pointer no-underline relative text-sm hover:bg-white-smoke"
                            v-else-if="query">
                            <span>
                                {{ i18n(noResults) }}
                            </span>
                        </a>
                    </template>
                </template>
            </dropdown>
        </template>
    </core-typeahead>
</template>

<script>
import { library } from '@fortawesome/fontawesome-svg-core';
import { faSearch } from '@fortawesome/free-solid-svg-icons';
import Dropdown from '@enso-ui/dropdown/bulma';
import CoreTypeahead from '../renderless/Typeahead.vue';

library.add(faSearch);
export default {
    name: 'Typeahead',

    components: { CoreTypeahead, Dropdown },

    model: {
        event: 'selected',
    },
    props: {
        disabled: {
            type: Boolean,
            default: false,
        },
        i18n: {
            type: Function,
            default: v => v,
        },
        isRounded: {
            type: Boolean,
            default: false,
        },
        noResults: {
            type: String,
            default: 'Nothing found...',
        },
        placeholder: {
            type: String,
            default: 'What are you searching for today?',
        },
        searching: {
            type: String,
            default: 'Searching...',
        },
    },
    methods: {
        clear() {
            this.$refs.typeahead.clear();
        },
    },
};
</script>

<style lang="scss">
    .dropdown.typeahead {
        width: 100%;

        .dropdown-trigger {
            width: 100%;
        }

        .dropdown-menu {
            width: 100%;

            .dropdown-content {
            // .overflow-y-auto {
                width: 100%;
                max-height: 20rem;
                // overflow-y: scroll;
                a.dropdown-item {
                    width: 100%;
                    text-overflow: ellipsis;
                    overflow-x: hidden;
                    padding-right: 1em;
                }
            }
        }
    }
    .control.has-icons-right .icon.clear-button {
        pointer-events: all;
    }
    .max-h-20 {
        max-height: 20rem;
    }
</style>
