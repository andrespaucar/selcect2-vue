<template>
    <select class="form-control select2" ref="selectj" style="width: 100%;">
        <slot></slot>
    </select>
</template>

<script>
export default {
    props:{
        options:{type:Object,default(){return {};}},
        value:{
            type: [String, Number, Array],
            default: null,
        },
        multiple:{default:null,type:Boolean},
        placeholder:{type:String},
        search:{default:null,type:Boolean},
        ajax:{default:null,type:Object}
    },
    data(){
        return{ 
            optionsSelect:{}
        }
    },
    watch:{
         value: function (value) {
            console.log('updatewatch value: ',value)
            // update value
            // let vm = this
            $(this.$el)
                .val(value)
                .trigger('change')
            //     .on('change', function (item) {
            //     if(this.multiple){
            //         vm.$emit('input', $(item.currentTarget).val())
            //     }else{
            //         vm.$emit('input', this.value)
            //     }
            // }) 
        },
        // options: function (options) {
        //     // update options
        //     $(this.$el).empty().select2({ data: options })
        // }
    },
    mounted(){ 
        // asignamos los options enviados por el PROP
        this.optionsSelect = this.options

        // this.optionsSelect.theme = "bootstrap4"

        if(this.placeholder !== 'undefined'){
            this.optionsSelect.placeholder = this.placeholder
        }
        if(this.multiple !== null){
            this.optionsSelect.multiple = true
        }
        if(this.search === null){
            this.optionsSelect.minimumResultsForSearch = Infinity//-1
        }else {
            this.focus_input_search()
            this.optionsSelect.minimumInputLength= 1 
            this.optionsSelect.minimumResultsForSearch = 1

        }
        this.optionsSelect.language={
            noResults:()=>'No hay resultado',
            searching:()=>'Buscando...',
            inputTooShort : (np)=> 'Por favor ingrese '+np.minimum+' o más caracteres...' ,
            errorLoading:()=>'Error loading results',
            loadingMore:()=>'Cargando más resultados...',
            searching:()=>'Buscando…'
        } 

        
 
        //https://select2.org/searching
        //https://es.vuejs.org/v2/examples/select2.html
        //https://select2.org/data-sources/ajax
        if(this.ajax !== null){ 
            this.focus_input_search()
            this.optionsSelect.ajax = this.ajax_objt(this.ajax)
            
            this.optionsSelect.minimumInputLength = 1
            this.optionsSelect.minimumResultsForSearch = 1

            //Resultado de la busqueda son como los options.
            this.optionsSelect.templateResult = (repo)=>{
                if (repo.loading) {
                    return repo.text;
                }
                return this.ajax.options(repo) || 'opciones no asignadas'// 'options not assigned'
            },

            // Mostrado al input principal.
            this.optionsSelect.templateSelection =  (repo) => {
                if(repo.text){
                    return repo.text
                }
                return this.ajax.selected(repo) || 'seleccionado no asignado'// 'selected not assigned'
            }

        }

        let vm = this
        // console.log(this.$el)//$refs.selectj
        $(this.$el)
        .select2(this.optionsSelect)
            .val(this.value)
            .trigger('change')
            // emit event on change || select2:select.
            .on('change', function (item) {
                vm.$emit('change')
                if(this.multiple){
                    vm.$emit('input', $(item.currentTarget).val())
                }else{
                    vm.$emit('input', this.value)
                }
            }) 
      
    },
    methods:{
        ajax_objt(option){
            /*
            option = {
                url : String,
                params: Object, // default: {page:option.page,search:option.term,total:option.total},
                items: Array[Object] // var del resultado de las busquedas , default:'items',
                options:(item)=>{...} // esultado de la busqueda son como los options. Mostrado al input principal.
                selected:(item)=>{...} // Mostrado al input principal.
                dataType: String, // default:'json'
                delay: Number, // default:250
                cache: Boolean // defaull:true
            }
            ----------------------------------------------------
            REQUEST 
            ?page=[]&q=[]&[...otherparams..]
            ------------
            RESPONSEJSON 
                total:145,
                page:1,
                items:[ {},{},{},{}...]
            */
            return {  
                url: option.url,
                dataType: option.dataType || 'json',
                delay: option.delay || 250,
                data: function (params) {
                    // Query parameters will be ?search=[term]&amp;page=[page]
                    return {
                        page: params.page || 1,// ?page=1...
                        q: params.term,// search term
                        ...option.params // others params
                    };
                },
                processResults: function (data, params) { 
                    params.page = params.page || 1;

                    return {
                        // Resultado de busqueda.
                        results: data[option.items] || data.items,
                        pagination: {
                            more: (params.page * 30) < data.total//_count
                        }
                    };
                },
                cache: option.cache || true, 
            } 
        },
        focus_input_search(){
            // Agregando el autofocus cuando el search est activo
            $(document).on('select2:open', () => {
                document.querySelector('.select2-search__field').focus();
            });
        }
    },
    // updated(){
        // let vm = this
        // $(this.$el)
        // .val(this.value)
        // .trigger('change')
        // // emit event on change
        // .on('change', function (item) {
        //     if(this.multiple){
        //         vm.$emit('input', $(item.currentTarget).val())
        //     }else{
        //         vm.$emit('input', this.value)
        //     }
        // }) 
    // },
    destroyed(){
        $(this.$el).select2('destroy');
    }

}
</script>

<style>

</style>
