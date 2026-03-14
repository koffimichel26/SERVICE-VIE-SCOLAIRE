# SERVICE-VIE-SCOLAIRE
Logiciel de gestion ÉDUCATEUR

<html lang="fr">
<head>

<meta charset="UTF-8">
<title>Logiciel Educateur - Vie Scolaire</title>

<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.29/jspdf.plugin.autotable.min.js"></script>

<style>

body{
font-family:Arial;
background:#eef2f7;
margin:0;
}

header{
background:#1e3799;
color:white;
text-align:center;
padding:15px;
}

.container{
padding:20px;
}

.card{
background:white;
padding:15px;
margin-bottom:20px;
border-radius:8px;
box-shadow:0 0 6px rgba(0,0,0,0.2);
}

table{
width:100%;
border-collapse:collapse;
margin-top:10px;
}

th,td{
border:1px solid #ddd;
padding:8px;
text-align:center;
}

th{
background:#1e3799;
color:white;
}

input,select{
padding:6px;
margin:4px;
}

button{
padding:8px;
background:#27ae60;
border:none;
color:white;
cursor:pointer;
border-radius:5px;
}

</style>
</head>

<body>

<header>
<h2>VIE SCOLAIRE - EDUCATEUR</h2>
<p>Gestion discipline et absences élèves</p>
</header>

<div class="container">

<div class="card">

<h3>Importer la liste des élèves</h3>

<input type="file" id="excelFile">
<button onclick="importerExcel()">Importer Excel</button>

<br><br>

<select id="listeEleves">
<option>Choisir un élève</option>
</select>

<br>

<input type="text" id="classe" placeholder="Classe">
<input type="text" id="matricule" placeholder="Matricule">

<br>

<select id="incident">

<option>Retard</option>
<option>Absence</option>
<option>Indiscipline</option>
<option>Tenue non correcte</option>
<option>Coiffure</option>
<option>Bagarre/Violence</option>
<option>Usage de Téléphone</option>
<option>Objet illicite</option>
<option>Fraude</option>
<option>Non respect du règlementinterieur</option>

</select>

<br>

<input type="text" id="parent" placeholder="Téléphone parent">
<input type="text" id="educateur" placeholder="Nom éducateur">

<button onclick="ajouterIncident()">Enregistrer</button>

<table id="tableIncident">

<tr>
<th>Élève</th>
<th>Classe</th>
<th>Matricule</th>
<th>Incident</th>
<th>Parent</th>
<th>Éducateur</th>
<th>Date</th>
</tr>

</table>

<br>

<button onclick="genererRapport()">Générer Rapport PDF</button>

</div>

</div>

<script>

let baseEleves=[]

function importerExcel(){

let file=document.getElementById("excelFile").files[0]

let reader=new FileReader()

reader.onload=function(e){

let data=new Uint8Array(e.target.result)

let workbook=XLSX.read(data,{type:"array"})

let sheet=workbook.Sheets[workbook.SheetNames[0]]

let json=XLSX.utils.sheet_to_json(sheet)

let select=document.getElementById("listeEleves")

json.forEach(function(row){

baseEleves.push(row)

let option=document.createElement("option")

option.text=row.NOM || row.Nom || row.Eleve

select.add(option)

})

}

reader.readAsArrayBuffer(file)

}

document.getElementById("listeEleves").addEventListener("change",function(){

let nom=this.value

let eleve=baseEleves.find(e=>e.NOM==nom || e.Nom==nom || e.Eleve==nom)

if(eleve){

document.getElementById("classe").value=eleve.CLASSE || eleve.Classe
document.getElementById("matricule").value=eleve.MATRICULE || eleve.Matricule

}

})

function ajouterIncident(){

let eleve=document.getElementById("listeEleves").value
let classe=document.getElementById("classe").value
let matricule=document.getElementById("matricule").value
let incident=document.getElementById("incident").value
let parent=document.getElementById("parent").value
let educateur=document.getElementById("educateur").value
let date=new Date().toLocaleDateString()

let table=document.getElementById("tableIncident")

let row=table.insertRow()

row.insertCell(0).innerHTML=eleve
row.insertCell(1).innerHTML=classe
row.insertCell(2).innerHTML=matricule
row.insertCell(3).innerHTML=incident
row.insertCell(4).innerHTML=parent
row.insertCell(5).innerHTML=educateur
row.insertCell(6).innerHTML=date

// message parent

let message="Bonjour M/Mme parent de "+eleve+
", matricule "+matricule+
". Incident : "+incident+
". Merci de vous présenter au lycée à mon bureau. Educateur : "+educateur

window.open("https://wa.me/"+parent+"?text="+encodeURIComponent(message))

// message Inspecteur Education

let numeroIE="2250700000000"

let messageIE="Notification Vie scolaire : "+eleve+
" Classe "+classe+
" Matricule "+matricule+
" Incident "+incident

window.open("https://wa.me/"+numeroIE+"?text="+encodeURIComponent(messageIE))

}

function genererRapport(){

const { jsPDF } = window.jspdf

let doc=new jsPDF()

doc.text("RAPPORT VIE SCOLAIRE",20,20)

let data=[]

let rows=document.querySelectorAll("#tableIncident tr")

for(let i=1;i<rows.length;i++){

let c=rows[i].children

data.push([
c[0].innerText,
c[1].innerText,
c[2].innerText,
c[3].innerText,
c[4].innerText,
c[5].innerText,
c[6].innerText
])

}

doc.autoTable({

head:[["Élève","Classe","Matricule","Incident","Parent","Éducateur","Date"]],
body:data,
startY:30

})

doc.save("rapport_vie_scolaire.pdf")

}

</script>

</body>
</html>
