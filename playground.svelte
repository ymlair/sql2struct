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
	let type = 1; // 默认转结构体带json
	$: goStruct = getGoStruct(value,type);
	const initializeRemarkable = () => {
		parser = window.SqlParser;
	}
	function getGoStruct(sql,type){
		let goStruct = "";
	 if (parser != undefined)
		 try{
		md = parser.parse(sql);
			 }catch{
			console.log("Parse Error")
			 }
		if (md != undefined && md.length >0){
						switch (type){
				case 1:
					goStruct = "type "+changeNameFunc(md[0].name)+" struct { \n"
					md[0].columns.forEach((item)=>{
						if (item.type == "column") {

							goStruct += ""+changeNameFunc(item.name)+" "+typeMap[item.data_type.type]+" "+"`json:\""+item.name+"\"` // "+item.comment+"\n";
						}
					})
					return goStruct + "}";
					break;
				case 2:
					goStruct = "type "+changeNameFunc(md[0].name)+" struct { \n"
					md[0].columns.forEach((item)=>{
						if (item.type == "column") {

							goStruct += ""+changeNameFunc(item.name)+" "+typeMap[item.data_type.type]+" "+"`json:\""+item.name+"\" gorm:\"column:"+item.name+"\"` // "+item.comment+"\n";
						}
					})
					return goStruct + "}";
					break;
				case 3:
					md[0].columns.forEach((item)=>{
						if (item.type == "column") {

							goStruct += item.name+",";
						}
					})
					return goStruct;
					break;
				case 4:
					md[0].columns.forEach((item)=>{
						if (item.type == "column") {

							goStruct += '"'+item.name+'",';
						}
					})
				  return goStruct;
					break;
				case 5:
					goStruct = "type "+changeNameFunc(md[0].name)+" struct { \n"
					md[0].columns.forEach((item)=>{
						if (item.type == "column") {

							goStruct += ""+changeNameFunc(item.name)+" "+typeMap[item.data_type.type]+" // "+item.comment+"\n";
						}
					})
					return goStruct + "}";
					break;
			}
		}
		return "";
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
<div>Enter the table creation statement on the left</div>
<hr />
<div>
<span on:click="{()=>{type=5}}">
	<input name="sql2struct" type="radio" checked={type==5} />convert Go struct
</span>|
<span on:click="{()=>{type=1}}">
<input name="sql2struct" type="radio" checked={type==1} on:click="{()=>{type=1}}" />with Json
	</span>|
<span on:click="{()=>{type=2}}">
<input name="sql2struct" type="radio" checked={type==2} on:click="{()=>{type=2}}" />with Gorm </span>|
<span on:click="{()=>{type=3}}">
<input name="sql2struct" type="radio" checked={type==3} on:click="{()=>{type=3}}" />get table filelds </span>|
<span on:click="{()=>{type=4}}">
<input name="sql2struct" type="radio" checked={type==4} on:click="{()=>{type=4}}" />get table fields with quote
	</span>
</div>
<hr />
<div id="sql">
	<textarea  rows="30" cols="80"
    id="markdown-content"
    bind:value={value}
  />
</div>
<div id="struct">
	<textarea  rows="30" cols="80"
    id="markdown-content"
    bind:value={goStruct}
  />
</div>

<style>
	#sql {
		float:left;
	}
	#struct{
		float:right;
	}
</style>
