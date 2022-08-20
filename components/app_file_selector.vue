// компонент для выбора файлов
// пример использования
//<AppFileSelector class="btn fas fa-upload" title="Загрузить изображение" :data="{multiple:true, accept:'image/*'}" @change="upload_and_apply_files({files:$event, obj:photo, par:'large'})"></AppFileSelector>                                                
<template>    
    <button @click="select_files()">Выбрать файлы
        <input type="file" ref="file_selector" @change="change2($refs.file_selector.files)" v-bind:multiple="data.multiple || false" :accept="data.accept || none">
    </button>
</template>

<script>
    module.exports =  {
        props: ["data"],
        mounted: function () {},
        methods: {
            select_files: function () {            
                this.$refs.file_selector.click();
            },

            change2: function (files) {
                this.$emit("change", files);
                this.$refs.file_selector.value = "";
            },
        },
    };
</script>
<style scoped>
input {
    display: none;
}
</style>