<!DOCTYPE html>
<html>
<head>
    <title>The Hive System</title>
    <style>
        body {
            background-color: #0a0a0a;
            color: white;
            font-family: Arial;
            margin: 0;
            padding: 0;
        }

        header {
            background: #111;
            padding: 20px;
            text-align: center;
            font-size: 28px;
            font-weight: bold;
        }

        .container {
            padding: 20px;
        }

        .panel {
            background: #1a1a1a;
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
        }

        .panel h2 {
            margin-top: 0;
        }

        button {
            background: #ffcc00;
            border: none;
            padding: 8px 15px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
        }

        button:hover {
            background: #ffe680;
        }

        input, select {
            padding: 8px;
            width: 90%;
            margin-bottom: 10px;
            border-radius: 5px;
            border: none;
        }

        .member-card {
            background: #262626;
            padding: 10px;
            border-radius: 8px;
            margin-bottom: 10px;
        }

        .role-card {
            background: #262626;
            padding: 10px;
            border-radius: 8px;
            margin-bottom: 10px;
        }

        .small-btn {
            padding: 5px 10px;
            background: #ff4444;
            margin-left: 5px;
        }
    </style>
</head>
<body>

<header>🐝 THE HIVE SYSTEM — OWNER MODE ENABLED</header>

<div class="container">

    <!-- OWNER PANEL -->
    <div class="panel">
        <h2>Owner Panel</h2>

        <h3>Create New Role</h3>
        <input id="roleName" placeholder="Role name...">
        <button onclick="createRole()">Add Role</button>

        <div id="roleList"></div>

        <hr>

        <h3>Add New Member</h3>
        <input id="memberName" placeholder="Member username...">
        <select id="memberRole"></select>
        <button onclick="addMember()">Add Member</button>

        <div id="memberList"></div>
    </div>

</div>

<script>
    let roles = ["Owner", "Admin", "Member"];
    let members = [
        { name: "j9vr", role: "Owner" }
    ];

    function updateRoleDropdown() {
        let dropdown = document.getElementById("memberRole");
        dropdown.innerHTML = "";
        roles.forEach(r => {
            let option = document.createElement("option");
            option.value = r;
            option.textContent = r;
            dropdown.appendChild(option);
        });
    }

    function displayRoles() {
        let list = document.getElementById("roleList");
        list.innerHTML = "";
        roles.forEach((role, index) => {
            list.innerHTML += `
                <div class="role-card">
                    ${role}
                    <button class="small-btn" onclick="deleteRole(${index})">Delete</button>
                </div>
            `;
        });
    }

    function createRole() {
        let name = document.getElementById("roleName").value;
        if (name.trim() === "") return alert("Enter role name");
        roles.push(name);
        updateRoleDropdown();
        displayRoles();
        document.getElementById("roleName").value = "";
    }

    function deleteRole(index) {
        if (roles[index] === "Owner") return alert("You cannot delete the Owner role.");
        roles.splice(index, 1);
        updateRoleDropdown();
        displayRoles();
    }

    function displayMembers() {
        let list = document.getElementById("memberList");
        list.innerHTML = "";
        members.forEach((member, index) => {
            list.innerHTML += `
                <div class="member-card">
                    <strong>${member.name}</strong>
                    <br>Role: ${member.role}
                    <br>
                    <button class="small-btn" onclick="editMember(${index})">Edit</button>
                    <button class="small-btn" onclick="removeMember(${index})">Remove</button>
                </div>
            `;
        });
    }

    function addMember() {
        let name = document.getElementById("memberName").value;
        let role = document.getElementById("memberRole").value;

        if (name.trim() === "") return alert("Enter a name");

        members.push({ name, role });
        displayMembers();
        document.getElementById("memberName").value = "";
    }

    function editMember(index) {
        let newName = prompt("New name:", members[index].name);
        if (!newName) return;

        let newRole = prompt("New role:", members[index].role);
        if (!newRole) return;

        members[index].name = newName;
        members[index].role = newRole;

        displayMembers();
    }

    function removeMember(index) {
        members.splice(index, 1);
        displayMembers();
    }

    // Initialize
    updateRoleDropdown();
    displayRoles();
    displayMembers();
</script>

</body>
</html>
