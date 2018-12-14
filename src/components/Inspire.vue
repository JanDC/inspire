<template>
    <v-container>
        <v-layout
                text-xs-center
                wrap
        >
            <v-select
                    :items="alphabet"
                    box
                    @change="selectAdjective"
                    label="Select adjective"
            ></v-select>
            <v-select
                    :items="alphabet"
                    box
                    @change="selectSubject"
                    label="Select subject"
            ></v-select>

            <v-btn @click="inspire" color="info">
                Inspire me
            </v-btn>

            <v-toolbar>
                <v-spacer>Your inspirations</v-spacer>
            </v-toolbar>
            <v-list>
                <template v-for="item in items">
                    <v-list-tile :key="index">
                        <v-list-tile-title>{{item.adjectiveWord}} {{item.subjectWord}}</v-list-tile-title>
                    </v-list-tile>
                </template>
            </v-list>

        </v-layout>
    </v-container>
</template>

<script>
    const axios = require('axios');

    export default {
        data: () => ({
            adjective: "",
            subject: "",
            items: [],
            dictionary: [],
        }),
        computed: {
            alphabet: () => {
                return 'abcdefghijklmnopqrstuvwxyz'.split('');
            },
            isFilterable: () => {
                return true;
            }
        },
        methods: {
            selectAdjective(adjective) {
                this.adjective = adjective;
            },
            selectSubject(subject) {
                this.subject = subject;
            },
            inspire() {
                if (this.dictionary.length === 0) {
                    this.loadDictionary();
                }

                if (!this.isFilterable) {
                    // explain why
                    return;
                }

                const adjectiveWord = this.chooseWordFromDictionary(this.adjective);
                const subjectWord = this.chooseWordFromDictionary(this.subject);

                if (adjectiveWord && subjectWord) {
                    this.items.unshift({adjectiveWord: adjectiveWord, subjectWord: subjectWord})
                }
            },
            chooseWordFromDictionary(firstLetter) {
                const eligibleWords = this.dictionary.filter((word) => {

                    if (word === undefined || word.length === 0) {
                        return false;
                    }

                    return word[0].toUpperCase() === firstLetter.toUpperCase();
                });


                const chosenWorkIndex = Math.floor(Math.random() * Math.floor(eligibleWords.length));

                return eligibleWords[chosenWorkIndex];
            },
            loadDictionary() {
                // Make a request for a user with a given ID
                axios.get('https://raw.githubusercontent.com/wooorm/dictionaries/master/dictionaries/en-US/index.dic').then((response) => {

                    let rows = response.data.split('\n');

                    this.dictionary = rows.map((row) => {
                        return row.split('/')[0];
                    });
                }).catch();
            }
        }
    }
</script>

<style>

</style>
