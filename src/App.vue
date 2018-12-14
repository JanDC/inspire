<template>
    <v-app>
        <v-content>
            <v-container fluid fill-height>
                <v-layout align-center justify-center>
                    <v-flex xs12 sm8 md4>
                        <v-toolbar app>
                            <v-toolbar-title class="headline text-uppercase">
                                <span>Inspire</span>
                                <span class="font-weight-light">.IO</span>
                            </v-toolbar-title>
                            <v-spacer></v-spacer>
                        </v-toolbar>

                        <Inspire/>
                    </v-flex>
                </v-layout>
            </v-container>
        </v-content>
    </v-app>
</template>

<script>
    import Inspire from './components/Inspire'

    export default {
        name: 'App',
        components: {
            Inspire
        },
        data() {
            return {
                //
            }
        }
    }
</script>
