<template>
    <v-container>
        <v-layout
                text-xs-center
                wrap
        >
            <v-select
                    :items="alphabet"
                    box
                    v-model="adjective"
                    label="Select adjective"
            />
            <v-select
                    :items="alphabet"
                    box
                    v-model="subject"
                    label="Select subject"
            />

            <v-btn @click="inspire" color="info">
                Inspire me
            </v-btn>

            <v-toolbar>
                <v-spacer>Your inspiration: {{this.adjectiveWord}} {{this.subjectWord}}</v-spacer>
            </v-toolbar>
        </v-layout>
    </v-container>
</template>

<script>
    const axios = require('axios');

    const minimongo = require("minimongo");

    const IndexedDb = minimongo.IndexedDb;

    export default {
        data: () => ({
            adjective: "i",
            subject: "o",
            adjectiveWord: "I",
            subjectWord: "O",
            items: [],
            db: null,
        }),
        computed: {
            alphabet: () => {
                return 'abcdefghijklmnopqrstuvwxyz'.split('');
            },
        },
        mounted() {
            this.db = new IndexedDb({namespace: "dictionary"}, () => {
                // Add a collection to the database
                this.db.addCollection("dictionary", () => {
                    this.loadDictionary().then((dictionary) => {
                        this.db.dictionary.seed(dictionary);
                    });
                });
            }, function () {
                alert("some error!");
            });
        },
        methods: {
            inspire() {
                this.chooseWordFromDictionary(this.adjective, (word) => {
                    this.adjectiveWord = word
                });
                this.chooseWordFromDictionary(this.subject, (word) => {
                    this.subjectWord = word
                });

            },
            chooseWordFromDictionary(firstLetter, callback) {
                this.db.dictionary.find({first: firstLetter}, {
                    limit: 100,
                    aggregate: [{$sample: {size: 1}}]
                }).fetch((eligibleWords) => {
                        callback(eligibleWords[0]._id);
                    }
                );
            },
            async loadDictionary() {
                // Make a request for a user with a given ID
                const response = await axios.get('https://raw.githubusercontent.com/wooorm/dictionaries/master/dictionaries/en-US/index.dic');

                let rows = response.data.split('\n');

                return rows.map((row) => {
                    const word = row.split('/')[0].toLowerCase();

                    return {first: word[0], _id: word}
                });


            }
        }
    }
</script>

<style>

</style>
