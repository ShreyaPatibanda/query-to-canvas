// path has to exist and has to be exact
canvasFilePath:: Canvas/test.canvas
width:: 550
height:: 750

padding:: 30
grid:: yes
gridWidth:: 10

```dataviewjs

// making random alphanumeric string for id
function randomString(length, chars) {
    var result = '';
    for (var i = length; i > 0; --i) result += chars[Math.floor(Math.random() * chars.length)];
    return result;
}
var rString = randomString(32, '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ');
    
const query = (await dv.query(`table without id file.path
where file.cday > date("2023-07-14")
where file.cday < date("2023-07-21")
`));

const files = query.value.values;
// making array of new nodes
let height = dv.current().height;
let width = dv.current().width;
let isGrid = dv.current().grid ? true : false;

let gridWidth = dv.current().gridWidth;
let path = dv.current().canvasFilePath;
console.log(path);
let padding = dv.current().padding;
let added = [files.length]
// only adding right side
let space = width + padding;
let startX = -500;
let x = startX;
let yVal = -1780;
for(let i = 0; i < files.length; i++) {
  let path = files[i][0];
  x = x + (space);
  let newID = randomString(16, rString);
  if(isGrid && i % gridWidth == 0) {
  yVal += height + padding;
  console.log("new row");
  x = startX;
  }
  console.log("next");
  added[i] = {"type":"file","file": path,"id": newID,"x":x,"y":yVal,"width":width,"height":height};
}


const { createButton } = app.plugins.plugins["buttons"];
// getting json from current canvas
const tfile = this.app.vault.getAbstractFileByPath(path);
  const canvasContents = await app.vault.read(tfile);
  const canvasData = JSON.parse(canvasContents);
//  console.log(canvasData);
const data = canvasData.nodes;
const newData = data.concat(added);
let newCanvas = canvasData;
newCanvas.nodes = newData;
//console.log(newCanvas);
//console.log(files);
let names = data.map((d) => d.file ? d.file : "text card");
const updated = JSON.stringify(newCanvas);
//console.log(updated);
const empty = async () => {
  await app.vault.modify(tfile, updated);
}
createButton({
        app,
        el: this.container,
        args: { name: "add", color: "blue"  },
        clickOverride: { click: empty, params: [] },
      });
dv.table(["Going to Add", "old Canvas Nodes", ""], [[dv.func.flat(query.value.values), names]]);
```









