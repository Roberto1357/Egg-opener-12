# Egg-opener-12
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Egg Opener Game</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f7f7f7;
            padding: 20px;
            overflow: hidden;
        }
        .button {
            padding: 15px 25px;
            font-size: 20px;
            color: #fff;
            background-color: #007BFF;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin: 5px;
        }
        .button:hover {
            background-color: #0056b3;
        }
        .output {
            margin-top: 20px;
            font-size: 24px;
            color: #333;
        }
        .animation-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.7);
            display: none;
            align-items: center;
            justify-content: center;
            z-index: 1000;
            overflow: hidden;
        }
        .zoom-effect {
            animation: zoomOut 1s forwards, zoomIn 1s 1.2s forwards;
        }
        .pet-popup {
            position: absolute;
            background-color: white;
            border: 2px solid #ccc;
            border-radius: 10px;
            padding: 20px;
            text-align: center;
            opacity: 0;
            animation: fadeIn 1s 2.5s forwards;
            font-size: 36px;
            font-weight: bold;
            color: #333;
        }
        @keyframes zoomOut {
            from {
                transform: scale(1);
            }
            to {
                transform: scale(0.5);
            }
        }
        @keyframes zoomIn {
            from {
                transform: scale(0.5);
            }
            to {
                transform: scale(1);
            }
        }
        @keyframes fadeIn {
            from {
                opacity: 0;
            }
            to {
                opacity: 1;
            }
        }
        .inventory {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: white;
            display: none;
            flex-wrap: wrap;
            align-items: center;
            justify-content: center;
            overflow-y: auto;
            z-index: 1000;
        }
        .inventory.active {
            display: flex;
        }
        .pet-box {
            width: 120px;
            height: 150px;
            margin: 10px;
            background-color: #fff;
            border-radius: 10px;
            box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            position: relative;
        }
        .pet-box .count {
            position: absolute;
            top: 5px;
            right: 5px;
            background-color: #007BFF;
            color: white;
            font-size: 12px;
            padding: 3px 6px;
            border-radius: 50%;
        }
        .pet-box .name {
            font-size: 14px;
            font-weight: bold;
            margin-top: 10px;
        }
        .close-button {
            position: absolute;
            top: 10px;
            right: 10px;
            background-color: #ff0000;
            color: white;
            border: none;
            border-radius: 50%;
            width: 30px;
            height: 30px;
            font-size: 18px;
            cursor: pointer;
        }
        .table-container {
            margin-top: 40px;
        }
        .pet-list {
            text-align: left;
            margin-left: auto;
            margin-right: auto;
            max-width: 600px;
        }
        .pet-list table {
            width: 100%;
            border-collapse: collapse;
        }
        .pet-list th, .pet-list td {
            border: 1px solid #ccc;
            padding: 8px;
            text-align: center;
        }
        .pet-list th {
            background-color: #007BFF;
            color: white;
        }
    </style>
</head>
<body>
    <h1>Egg Opener Simulator</h1>
    <div>
        <button id="inventoryButton" class="button" style="margin-right: auto; float: left;" onclick="toggleInventory()">Inventar</button>
        <button id="openButton" class="button" onclick="openEgg()">Open Egg</button>
    </div>
    <div class="output" id="output">Click the button to open an egg!</div>
    <div id="popupContainer"></div>

    <!-- Animation Container -->
    <div class="animation-container" id="animationContainer">
        <div class="pet-popup" id="petPopup">Rare Fox!</div>
    </div>

    <!-- Inventory Overlay -->
    <div class="inventory" id="inventory">
        <button class="close-button" onclick="toggleInventory()">×</button>
        <!-- Pet boxes will be dynamically created here -->
    </div>

    <!-- Pet List Table -->
    <div class="table-container">
        <div class="pet-list">
            <h2>Pet Collection</h2>
            <table>
                <thead>
                    <tr>
                        <th>Pet</th>
                        <th>Probability</th>
                        <th>Count</th>
                    </tr>
                </thead>
                <tbody id="petTableBody">
                    <!-- Pet rows will be populated here -->
                </tbody>
            </table>
        </div>
    </div>

    <script>
        const pets = [
            { name: "Common Cat", chance: 60.0, count: 0 },
            { name: "Common Dog", chance: 20.0, count: 0 },
            { name: "Uncommon Bird", chance: 10.0, count: 0 },
            { name: "Rare Fox", chance: 5.0, count: 0 },
            { name: "Epic Dragon", chance: 2.5, count: 0 },
        ];
        const totalChance = pets.reduce((sum, pet) => sum + pet.chance, 0);

        const petTableBody = document.getElementById("petTableBody");
        pets.forEach(pet => {
            const row = document.createElement("tr");
            row.innerHTML = `<td>${pet.name}</td><td>${pet.chance}%</td><td id="${pet.name}-count">0</td>`;
            petTableBody.appendChild(row);
        });

        function openEgg() {
            const random = Math.random() * totalChance;
            let cumulativeChance = 0;

            for (const pet of pets) {
                cumulativeChance += pet.chance;
                if (random <= cumulativeChance) {
                    pet.count++;
                    document.getElementById(`${pet.name}-count`).innerText = pet.count;
                    if (pet.name === "Rare Fox") {
                        playRareAnimation(pet.name);
                    } else {
                        showPopup(pet.name);
                    }
                    return;
                }
            }
        }

        function showPopup(petName) {
            const popupContainer = document.getElementById("popupContainer");
            popupContainer.innerHTML = "";
            const popup = document.createElement("div");
            popup.className = "pet-popup";
            popup.textContent = `You got: ${petName}`;
            popupContainer.appendChild(popup);
            setTimeout(() => {
                popup.remove();
            }, 3000);
        }

        function playRareAnimation(petName) {
            const animationContainer = document.getElementById("animationContainer");
            const petPopup = document.getElementById("petPopup");
            const openButton = document.getElementById("openButton");
            const inventoryButton = document.getElementById("inventoryButton");

            openButton.disabled = true;
            inventoryButton.disabled = true;
            animationContainer.style.display = "flex";
            petPopup.textContent = petName;
            animationContainer.classList.add("zoom-effect");

            setTimeout(() => {
                animationContainer.style.display = "none";
                animationContainer.classList.remove("zoom-effect");
                openButton.disabled = false;
                inventoryButton.disabled = false;
            }, 3500);
        }

        function toggleInventory() {
            const inventory = document.getElementById("inventory");
            inventory.classList.toggle("active");
            if (inventory.classList.contains("active")) renderInventory();
        }

        function renderInventory() {
            const inventory = document.getElementById("inventory");
            inventory.innerHTML = `<button class="close-button" onclick="toggleInventory()">×</button>`;
            pets.forEach(pet => {
                const petBox = document.createElement("div");
                petBox.className = "pet-box";
                petBox.innerHTML = `<div class="count">${pet.count}</div><div class="name">${pet.name}</div>`;
                inventory.appendChild(petBox);
            });
        }
    </script>
</body>
</html>
