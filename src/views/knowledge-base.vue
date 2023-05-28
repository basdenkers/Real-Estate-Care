<template>
    <main class="container">
        <router-link :to="{ name: 'home' }" class="btn btn-primary mb-4" aria-label="Go back to the dashboard">Go back</router-link>
        <div class="row">


            <div class="col-sm-6 mt-3">
                <div class="border rounded p-3">
                    <h5>Document 1</h5>
                    <a @click="file(1)" download class="btn btn-primary" aria-label="Download document 1">Download</a>
                </div>
            </div>
            <div class="col-sm-6 mt-3">
                <div class="border rounded p-3">
                    <h5>Document 2</h5>
                    <a @click="file(2)" download class="btn btn-primary" aria-label="Download document 2">Download</a>
                </div>
            </div>
        </div>
    </main>
</template>

<script>
    import FileSaver from 'file-saver';

    import file1 from '@/files/document-1.docx?url'
    import file2 from '@/files/document-2.docx?url'
    import file3 from '@/files/example-document-3.docx?url'
    import file4 from '@/files/example-document-4.docx?url'

    export default {
        methods: {
            file(number = false) {
                switch(number) {
                    case 1:
                        fetch(file1)
                        .then(response => response.blob())
                        .then(blob => {
                            FileSaver.saveAs(blob, file1.split('/').pop());
                        });
                        break;
                    case 2:
                        fetch(file2)
                        .then(response => response.blob())
                        .then(blob => {
                            FileSaver.saveAs(blob, file2.split('/').pop());
                        });
                        break;
                    default:
                }
            },
            downloadFile(fileUrl) {
                fetch(fileUrl)
                .then(response => response.blob())
                .then(blob => {
                    FileSaver.saveAs(blob, fileUrl.split('/').pop());
                });
            },
        },
    }
</script>