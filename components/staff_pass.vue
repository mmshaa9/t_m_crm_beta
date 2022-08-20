<template>
    <div class="pass_page" >
        <div ref="pass_">
            <div  class="pass_main_block">
                <div class="pass_block_two">
                    <div class="block_two_img">
                        <img class="img_logo" src="../assets/logo.png" alt="">
                    </div>
                    <div class="block_two_text">
                        Завод дробильного оборудования
                        <br>
                        "Тульские машины"
                    </div>
                </div>
                <div class="pass_block_three">
                    {{data.name}}
                    <!-- <div v-html="worker.barcode" class="pass_barcode"></div> -->
                    <img class="img_code" :src="data.barcode" alt="">
                </div>
            </div>
        </div>
        <div class="pass_block_four">
            <button class="block_four_btn button" @click="print"> Печать</button>
        </div>
    </div>
</template>

<script>
    export default ({
        props:['data'],
        data(){
            return {
                
            }  
        },    
        
        methods:{
            print(){
                console.log(this.$refs)
                let stylesHtml = '';
                for (const node of [...document.querySelectorAll('link[rel="stylesheet"], style')]) {
                    stylesHtml += node.outerHTML;
                }
                var win = window.open('','','width=1024,height=768,toolbar=0,scrollbars=0,status =0');
                var content = "<html>";
                content +="<title>Печать пропуска</title>"
                content += "<body onload=\"window.print(); window.close();\">";
                content += this.$refs.pass_.innerHTML ;
                content += "</body>";
                content += "</html>";
                win.document.write(content);
                win.document.write(stylesHtml);
                win.document.close();
            }
        }
    })
</script>

<style scoped>

    .pass_page{
        background: white;
    }
    .pass_block_two{
        display: flex;
        align-items: center;
        border-bottom: 1px solid #8a8a8a;
        padding: 10px;
        background: white;
    }
    .block_two_img{
        height: 40px;
        width: 33px;
        display: flex;
        align-items: center;
        /* background: rebeccapurple;
        background-image: url('~@/assets/logo.png');
        background-size: cover; */
    }
    .block_two_text{
        font-weight: 600;
        text-align: center;
        margin-left: 65px;
    }
    .pass_block_three{
        text-align: center;
        padding-top: 10px;
        padding-bottom: 50px;
        font-size: 20px;
        font-weight: 900;
        background: white;
        display: flex;
        flex-direction: column;
        align-items: center;
    }
    .pass_block_four{
        padding: 7px 5px;
        background: #f6f6f6;
        border-top: 1px solid #8a8a8a;
    }
    .block_four_btn{
        width: 98%;
    }

    .pass_main_block{
        width: 460px;
        border: 1px black;
        border-style: dashed;
        margin: 10px;
    }

    .pass_barcode{
        display: flex;
        align-items: center;
        justify-content: center;
    }

    @media print {
        .pass_main_block{
            overflow: hidden;
            page-break-inside: avoid;
            width: 8.7cm;
            height: 5.5cm;
        }
        .block_two_text{
            margin-left: 25px;
            font-size: 14px;
        }
        .pass_block_three{
            padding-top: 0px;
            white-space: pre-line;
        }
    }
</style>