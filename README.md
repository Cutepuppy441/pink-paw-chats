<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pink Paw Chats</title>

<style>
body{
font-family:Arial;
margin:0;
background:linear-gradient(to bottom right,#ffc6ff,#ffd6ff);
text-align:center;
}
.panel{
background:white;
margin:10px;
padding:15px;
border-radius:15px;
}
#chatBox,#privateBox,#groupBox{
height:200px;
overflow-y:auto;
background:white;
padding:10px;
border-radius:10px;
text-align:left;
}
.meTag{color:hotpink;font-weight:bold;}
.fakeTag{color:black;font-weight:bold;}
button{margin:4px;padding:6px;}
.friendItem{margin:4px;}
#topNotification{
position:fixed;
top:0;
left:0;
right:0;
background:black;
color:white;
padding:10px;
display:none;
z-index:1000;
}
.onlineDot{
width:8px;
height:8px;
background:lime;
display:inline-block;
border-radius:50%;
margin-left:5px;
}
</style>
</head>

<body>

<div id="topNotification"></div>

<h1>🌸 Pink Paw Chats 🌸</h1>

<div class="panel">
💬 Public Chat
<div id="chatBox"></div>
<input id="chatInput">
<button onclick="sendMessage()">Send</button>
</div>

<div class="panel">
👥 Add Friend
<input id="friendInput" placeholder="Type player name">
<button onclick="sendFriendRequest()">Add</button>

<div id="friendRequests"></div>

<h3>Your Friends</h3>
<div id="friendsList"></div>
</div>

<div class="panel">
📩 Private Chat
<div id="privateBox"></div>
<input id="privateInput">
<button onclick="sendPrivate()">Send</button>
</div>

<div class="panel">
👥 Group Chat
<div id="groupBox"></div>
<input id="groupInput">
<button onclick="sendGroup()">Send</button>
</div>

<script>

let myName="Vicky";
let fakePlayers=["Leo","Luna","Max","Zara","Kai","Mia","Noah","Aria","Nova","Axel"];

let chatBox=document.getElementById("chatBox");
let privateBox=document.getElementById("privateBox");
let groupBox=document.getElementById("groupBox");

let friends=[];
let friendRequests=[];
let blocked=[];
let currentPrivate=null;

function showNotification(text){
let bar=document.getElementById("topNotification");
bar.innerText=text;
bar.style.display="block";
setTimeout(()=>{bar.style.display="none";},6000);
}

function humanReply(input){
let emotional=[
"Wait… why would you say that?",
"I didn't expect that honestly.",
"Okay but hear me out…",
"That lowkey hurt 😭",
"I mean… you're not wrong.",
"Are we serious right now?",
"That’s dramatic.",
"I respect it but also no.",
"You always say stuff like that.",
"I'm thinking about it..."
];

let curious=[
"Why though?",
"What made you think that?",
"Explain please.",
"Tell me more.",
"Now I'm interested."
];

let playful=[
"LOL stop.",
"You're insane.",
"I can't deal with you.",
"This is why we're friends.",
"Unbelievable behavior."
];

let pools=[emotional,curious,playful];
let chosenPool=pools[Math.floor(Math.random()*pools.length)];
return chosenPool[Math.floor(Math.random()*chosenPool.length)];
}

function renderMessage(name,msg){
let div=document.createElement("div");

if(name===myName){
div.innerHTML="<span class='meTag'>"+name+"</span>: "+msg;
}else{
div.innerHTML="<span class='fakeTag'>"+name+"</span>: "+msg;
}

chatBox.appendChild(div);
chatBox.scrollTop=chatBox.scrollHeight;
}

function sendMessage(){
let msg=document.getElementById("chatInput").value;
if(!msg) return;

renderMessage(myName,msg);
document.getElementById("chatInput").value="";

for(let i=0;i<4;i++){
setTimeout(()=>{
let p=fakePlayers[Math.floor(Math.random()*fakePlayers.length)];
if(!blocked.includes(p)){
renderMessage(p,humanReply(msg));
}
},400*(i+1));
}
}

function sendFriendRequest(){
let name=document.getElementById("friendInput").value.trim();
if(!name || name===myName) return;
if(friends.includes(name)) return;

friendRequests.push(name);
updateFriendRequests();
document.getElementById("friendInput").value="";
}

function updateFriendRequests(){
let container=document.getElementById("friendRequests");
container.innerHTML="<h3>Requests</h3>";

friendRequests.forEach((name,index)=>{
let div=document.createElement("div");
div.className="friendItem";
div.innerHTML=name+
"<button onclick='acceptFriend("+index+")'>Accept</button>"+
"<button onclick='removeRequest("+index+")'>Remove</button>";
container.appendChild(div);
});
}

function acceptFriend(index){
let name=friendRequests[index];
friends.push(name);
friendRequests.splice(index,1);
updateFriendRequests();
updateFriendsList();
showNotification(name+" is now your friend!");
}

function removeRequest(index){
friendRequests.splice(index,1);
updateFriendRequests();
}

function updateFriendsList(){
let container=document.getElementById("friendsList");
container.innerHTML="";

friends.forEach(name=>{
let div=document.createElement("div");
div.className="friendItem";

let online=Math.random()>0.5;
div.innerHTML=
"👤 "+name+
(online?"<span class='onlineDot'></span>":"")+
"<button onclick='openPrivate(\""+name+"\")'>Chat</button>"+
"<button onclick='blockUser(\""+name+"\")'>Block</button>";

container.appendChild(div);
});
}

function blockUser(name){
blocked.push(name);
showNotification(name+" blocked.");
}

function openPrivate(name){
if(!friends.includes(name)) return;
currentPrivate=name;
privateBox.innerHTML="<b>Chat with "+name+"</b><br><br>";
}

function sendPrivate(){
let msg=document.getElementById("privateInput").value;
if(!msg || !currentPrivate) return;

let div=document.createElement("div");
div.innerHTML="<span class='meTag'>"+myName+"</span>: "+msg;
privateBox.appendChild(div);
document.getElementById("privateInput").value="";

setTimeout(()=>{
if(!blocked.includes(currentPrivate)){
let reply=document.createElement("div");
reply.innerHTML="<span class='fakeTag'>"+currentPrivate+"</span>: "+humanReply(msg);
privateBox.appendChild(reply);
privateBox.scrollTop=privateBox.scrollHeight;
showNotification(currentPrivate+" sent you a message!");
}
},1000);
}

function sendGroup(){
let msg=document.getElementById("groupInput").value;
if(!msg) return;

let div=document.createElement("div");
div.innerHTML="<span class='meTag'>"+myName+"</span>: "+msg;
groupBox.appendChild(div);
document.getElementById("groupInput").value="";

friends.forEach(friend=>{
setTimeout(()=>{
if(!blocked.includes(friend)){
let reply=document.createElement("div");
reply.innerHTML="<span class='fakeTag'>"+friend+"</span>: "+humanReply(msg);
groupBox.appendChild(reply);
}
},Math.random()*2000);
});
}

setInterval(()=>{
let p=fakePlayers[Math.floor(Math.random()*fakePlayers.length)];
if(!blocked.includes(p)){
renderMessage(p,humanReply("random"));
}
},2000);

</script>

</body>
</html>
