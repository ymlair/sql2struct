<script>
  let typeMap = {
  "TINYINT": "uint8",
  "SMALLINT": "uint16",
  "MEDIUMINT": "uint32",
  "INT": "int64",
  "BIGINT": "int64",
  "FLOAT": "int64",
  "DOUBLE": "int64",
  "DECIMAL": "int64",
  "YEAR": "time.Time",
  "TIME": "time.Time",
  "DATE": "time.Time",
  "DATETIME": "time.Time",
  "TIMESTAMP": "time.Time",
  "CHAR": "string",
  "VARCHAR": "string",
  "BINARY": "string",
  "VARBINARY": "string",
  "BLOB": "string",
  "TEXT": "string",
  "ENUM": "string",
  "SET": "string"
};
  let md;
  let parser;
  let value = "";
  $: goStruct = getGoStruct(value)
  
  const initializeRemarkable = () => {
    parser = window.SqlParser;
  }
  function getGoStruct(sql){
    let goStruct = "";
   if (parser != undefined)
     try{
    md = parser.parse(sql);
       }catch{
      console.log("Parse Error")
       }
    if (md != undefined && md.length >0){
      console.log("Parse Success")
      goStruct = "type "+changeNameFunc(md[0].name)+" struct { <br>"
      md[0].columns.forEach((item)=>{
        if (item.type == "column") {
          goStruct += ""+changeNameFunc(item.name)+" "+typeMap[item.data_type.type]+" "+"`json:\""+item.name+"\"` // "+item.comment+"<br>";
        }
      
      })
    }
    return goStruct + "}"
  }
  function changeNameFunc(name) {
    if (name == undefined){ return }
    let arr = name.split('');
    arr.map((item, index) => {
      arr[0] = arr[0].toUpperCase()
        if (item == '_') {
            arr.splice(index,1);
            arr[index] = arr[index].toUpperCase()
        }
    })
    return arr.join('')
}
</script>

<svelte:head>
  <script src="https://mmzp.github.io/sql-parser/browser/sql-parser.min.js" on:load={initializeRemarkable}></script>
</svelte:head>

<div className="MarkdownEditor">
  <h3>Input</h3>
  <label htmlFor="markdown-content">
    Enter some markdown
  </label>
  <textarea
    id="markdown-content"
    bind:value={value}
  />
  <h3>Output</h3>
  <div
    className="content">
    {#if md}
      {@html goStruct}
    {/if}
  </div>
</div>
