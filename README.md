## GET STARTED
Componente Select2 de jquery en VueJS. <br>
En mi ultimo proyecto toco utilizar un template admin que venia con base Jquery trate de utilizar
la mayoria de librerias que traia para no recargar tanto al VUE, una de ellas es Select2, ademas
que usa busquedas locales y por Ajax.<br>
Requisito: tener instalado JQUERY y SELECT2
```js
<Select2 v-model="selectedBrandPhone" >
    <option value="1">SAMSUNG A72</option>
    <option value="2">SAMSUNG S21</option>
    <option value="3">SAMSUNG A52</option>
    <option value="4">XIAOMI M10</option>
</Select2>
<Select2 v-model="selectedBrandLp"  placeholder="Seleccione Marca" multiple> 
    <option value="1">HP</option>
    <option value="2">LENOVO</option>
    <option value="3">AZUS</option>
</Select2>
<Select2 v-model="selectedCategory" search placeholder="Seleccione Categoría">
    <option value="1">Smartphone</option>
    <option value="3">Laptop Gammer</option>
    <option value="4">PC Gammer</option>
</Select2>
<Select2 v-model="exampleDataAjax" placeholder="Buscar un repositorio" 
        :ajax="{
            url:'https://api.github.com/search/repositories',
            options:(item)=>{
                return item.full_name + ' - ' + item.description
            },
            selected:(item)=>{
                return item.full_name
            }
        }" >
</Select2>
...
data(){
    return {
        selectedBrandPhone:2,
        selectedBrandLp:[], // multiple
        selectedCategory:null,
        exampleDataAjax:null
    }
}
```
### PROPS
- options : {...settings}
- placeholder : String
- search : Boolean
- ajax : {}
- multiple : Boolean
### PROP AJAX (object)
```js
ajax = {
    url : String,
    params: Object, // default: {page:option.page, search:option.term, total:option.total},
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

```
### ADD PROP PROBLEM MODAL - Boostrap.
Cuando tendas un modal y quieras utilizar Select2 traera un conflicto pero con el siguiente codigo lo podras resolver.
```js
  :options="{dropdownParent: '.modal-content'}"
```

### AJAX SELECT2 [doc](https://select2.org/data-sources/ajax)
Para realizar busquedas por Api 'ajax' asincrónica . <br>
si tienes paginado por 10 cada página mostrará las 10 pero una vez llevado el Scrool hasta el último cargara automaticamente la siguiente página. <br>
valor 'PROPS' => ajax:{type:Object}
```js
ajax: {
    url: "https://api.github.com/search/repositories",
    dataType: 'json',
    delay: 250,
    data: function (params) {
      return {
        // Query parameters will be ?search=[term]&amp;page=[page]
        // search 'term' : esta variable esta por defecto cuando se escribe en input search
        q: params.term, 
        page: params.page
      };
    },
    processResults: function (data, params) {
      // parse the results into the format expected by Select2
      // since we are using custom formatting functions we do not need to
      // alter the remote JSON data, except to indicate that infinite
      // scrolling can be used
      params.page = params.page || 1;

      return {
        results: data.items,
        pagination: {
          more: (params.page * 30) < data.total_count
        }
      };
    },
    cache: true
  },
  // esto ya lo tenemos en el valor prop 'placeholder'
  placeholder: 'Search for a repository', 
  // caracter minimo para relizar la busqueda defautl:1
  minimumInputLength: 1,

  // MOSTRARA AL RESULTADO DE LA BUSQUEDA
  templateResult: (repo) =>{
    if (repo.loading) {
        return repo.text;
    }
    // AQUI PUEDES PERSONALIZAR.
    return repo.full_name + " - "+ repo.description
  },

  // RETORNARA LO SELECIONADO Y SE MOSTRARA EN EL INPUT PRINCIPAL
  templateSelection: (repo)=>{
    if(repo.text){
        return repo.text
    }
    // AQUI PUEDES PERSONALIZAR.
    return repo.full_name
  }
});
 

function formatRepoSelection (repo) {
  return repo.full_name || repo.text;
}
```
