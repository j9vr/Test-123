<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>The Hive System | DXRP</title>
<style>
body { margin:0; font-family:Arial, sans-serif; background:#000; color:#fff; }
#authScreen { display:flex; flex-direction:column; align-items:center; justify-content:center; height:100vh; }
input, select { width:250px; padding:10px; margin:8px 0; border:none; border-radius:5px; background:#111; color:#00ffcc; text-align:center; font-size:16px; }
button { width:270px; padding:10px; margin-top:10px; border:none; border-radius:5px; background:#00ffcc; color:#000; font-weight:bold; cursor:pointer; }
button:hover { background:#00bfa5; }
#hiveSystem { display:none; }
.nav { display:flex; justify-content:space-between; padding:15px 30px; background:#111; border-bottom:2px solid #00ffcc; }
.logo { font-size:25px; font-weight:bold; color:#00ffcc; }
.content { padding:20px 40px; }
.section { background:#111; border-left:4px solid #00ffcc; border-radius:5px; margin-bottom:20px; padding:20px; }
.section h2 { color:#00ffcc; margin-bottom:10px; }
.member { background:#222; padding:10px; margin:5px 0; border-left:3px solid #00ffcc; display:flex; justify-content:space-between; align-items:center; }
.ownerBtn { background:#ffaa00; color:#000; margin-top:10px; width:auto; padding:5px 10px; }
.ownerPanel { display:none; background:#111; border-left:4px solid #ffaa00; padding:15px; margin-top:20px; border-radius:5px; }
.ownerPanel input, .ownerPanel select { width:200px; margin-right:5px; }
.memberControl { display:flex; gap:5px; }
.memberControl button { padding:2px 5px; border:none; border-radius:3px; cursor:pointer; }
.memberControl button.edit { background:#ffaa00; color:#000; }
.memberControl button.delete { background:#ff4444; color:#fff; }
.customRoleBtn { background:#00ffcc; color:#000; margin-top:5px; padding:3px 7px; border:none; border-radius:3px; cursor:pointer; }
.toggleLink { background:none; border:none; color:#00ffcc; cursor:pointer; text-decoration:underline; margin-top:10px; }
</style>
</head>
<body>

<div id="authScreen">
<h1 id="authTitle">The Hive System Login</h1>
<input type="text" id="authUsername" placeholder="Username">
<input type="password" id="authPassword" placeholder="Password">
<button id="authBtn" onclick="authenticate()">Login</button>
<button class="toggleLink" onclick="toggleAuth()">Create Account</button>
<p style="margin-top:10px; color:#00ffcc;">Owner login → Username: j9vr | Password: dxrp</p>
</div>

<div id="hiveSystem">
<div class="nav">
    <div class="logo">THE HIVE SYSTEM</div>
    <div><button class="ownerBtn" onclick="logout()">Logout</button></div>
</div>

<div class="content">
<h1>Welcome to The Hive DXRP Dashboard</h1>
<div class="section">
<h2>Players / Members</h2>
<div id="membersListDisplay"></div>
</div>

<!-- Owner Panel -->
<button id="ownerPanelBtn" class="ownerBtn" onclick="toggleOwnerPanel()" style="display:none;">Owner Panel</button>
<div class="ownerPanel" id="ownerPanel">
<h3>Manage Members & Access</h3>
<input id="grantUsername" placeholder="Enter username to grant member">
<button class="customRoleBtn" onclick="grantMember()">Grant Member</button>
<button class="customRoleBtn" onclick="grantOwner()">Grant Owner Panel</button>

<h3>Current Members</h3>
<div id="membersList"></div>
</div>
</div>

<script>
// Load state
let users = JSON.parse(localStorage.getItem("hiveUsers")) || [{"username":"j9vr","password":"dxrp","isMember":true,"isOwner":true}];
let isOwner=false;
let currentUser=null;

// Auth toggle
let isRegister=false;
function toggleAuth(){
    isRegister=!isRegister;
    document.getElementById("authTitle").textContent=isRegister?"Create Account":"The Hive System Login";
    document.getElementById("authBtn").textContent=isRegister?"Register":"Login";
}

// Login/Register
function authenticate(){
    const username=document.getElementById("authUsername").value.trim();
    const password=document.getElementById("authPassword").value.trim();
    if(username===""||password===""){alert("Enter username and password");return;}

    if(isRegister){
        if(users.some(u=>u.username===username)) return alert("Username exists");
        users.push({username,password,isMember:false,isOwner:false});
        localStorage.setItem("hiveUsers", JSON.stringify(users));
        alert("Account created! Login now.");
        toggleAuth();
        return;
    }

    const user=users.find(u=>u.username===username && u.password===password);
    if(!user) return alert("Invalid credentials");

    currentUser=user;
    isOwner=user.isOwner;
    document.getElementById("ownerPanelBtn").style.display=isOwner?"inline-block":"none";

    document.getElementById("authScreen").style.display="none";
    document.getElementById("hiveSystem").style.display="block";
    refreshMembers();
}

// Logout
function logout(){
    document.getElementById("hiveSystem").style.display="none";
    document.getElementById("authScreen").style.display="flex";
    currentUser=null;
    isOwner=false;
}

// Owner Panel
function toggleOwnerPanel(){
    const panel=document.getElementById("ownerPanel");
    panel.style.display=(panel.style.display==="block"?"none":"block");
}

// Members display
function refreshMembers(){
    const displayDiv=document.getElementById("membersListDisplay");
    displayDiv.innerHTML="";
    users.filter(u=>u.isMember).forEach(u=>{
        const div=document.createElement("div");
        div.className="member";
        div.textContent=u.username + (u.isOwner ? " (Owner Panel)" : "");
        displayDiv.appendChild(div);
    });

    // Owner panel member list
    const membersDiv=document.getElementById("membersList");
    membersDiv.innerHTML="";
    if(!isOwner) return;
    users.forEach((u,index)=>{
        const div=document.createElement("div");
        div.className="member";
        div.innerHTML=`${u.username} ${u.isOwner?"(Owner)":"(Member)"} <div class="memberControl">
            <button class="edit" onclick="removeMember(${index})">Remove Member</button>
        </div>`;
        membersDiv.appendChild(div);
    });
    localStorage.setItem("hiveUsers", JSON.stringify(users));
}

// Grant member access
function grantMember(){
    const username=document.getElementById("grantUsername").value.trim();
    const user=users.find(u=>u.username===username);
    if(!user) return alert("User not found");
    user.isMember=true;
    localStorage.setItem("hiveUsers", JSON.stringify(users));
    document.getElementById("grantUsername").value="";
    refreshMembers();
}

// Grant Owner Panel access
function grantOwner(){
    const username=document.getElementById("grantUsername").value.trim();
    const user=users.find(u=>u.username===username);
    if(!user) return alert("User not found");
    user.isOwner=true;
    localStorage.setItem("hiveUsers", JSON.stringify(users));
    document.getElementById("grantUsername").value="";
    refreshMembers();
}

// Remove member
function removeMember(index){
    const u = users[index];
    if(confirm(`Remove ${u.username} from members?`)){
        u.isMember=false;
        u.isOwner=false;
        refreshMembers();
    }
}
</script>
</body>
</html>
