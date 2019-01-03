<template>
    <v-layout align-center justify-center row fill-height>
        <v-text-field ref="searchInput" v-model="textToSearch" @input="searchChanged" @keyup.enter="search"></v-text-field>

        <v-btn flat icon color="grey darken-1" :disabled="navigateBeforeButtonDisable" @click="beforeSearchResult">
            <v-icon :title="$t('before')" style="font-size: 24px">navigate_before</v-icon>
        </v-btn>

        <v-btn flat icon color="grey darken-1" :disabled="navigateNextButtonDisable" @click="nextSearchResult">
            <v-icon :title="$t('next')" style="font-size: 24px">navigate_next</v-icon>
        </v-btn>
    </v-layout>
</template>

<script>
    const remote = __non_webpack_require__('electron').remote;
    const webContents = remote.getCurrentWebContents();
    
    export default {
        data() {
            return {
                textToSearch: "",
                navigateBeforeButtonDisable: true,
                navigateNextButtonDisable: true
            }
        },
        mounted: function() {
            webContents.on('found-in-page', (event, result) => {
                if (result.matches > 1) {
                    this.navigateBeforeButtonDisable = false;
                    this.navigateNextButtonDisable = false;
                }
            });
        },
        methods: {
            search(keyboardEvent) {
                if (this.textToSearch) {
                    webContents.findInPage(this.textToSearch);
                }
                else {
                    webContents.stopFindInPage("clearSelection");
                }
            },
            searchChanged() {
                this.navigateBeforeButtonDisable = true;
                this.navigateNextButtonDisable = true;
            },
            beforeSearchResult() {
                webContents.findInPage(this.textToSearch, {forward: false, findNext: true});
            },
            nextSearchResult() {
                webContents.findInPage(this.textToSearch, {forward: true, findNext: true});
            }
        }
    }
</script>
