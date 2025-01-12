<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Idea Hub</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(to right, #ff7e5f, #feb47b, #6a11cb, #2575fc, #00c6ff); /* Multi-color gradient */
            color: white;
            text-align: center;
            padding: 20px;
            margin: 0;
            height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            transition: background 0.3s ease;
        }
        .tab {
            display: none;
        }
        .tab.active {
            display: block;
            animation: fadeIn 0.3s ease-in-out;
        }
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        .button {
            padding: 10px 20px;
            background-color: #4CAF50;
            color: white;
            border: none;
            cursor: pointer;
            font-size: 16px;
            margin-top: 20px;
        }
        .button:hover {
            background-color: #45a049;
        }
        input, textarea {
            padding: 10px;
            width: 80%;
            margin: 10px;
        }
        .idea-container {
            display: flex;
            justify-content: center;
            align-items: center;
            flex-wrap: wrap;
            margin-top: 20px;
            max-height: 400px;
            overflow-y: auto;
            margin-bottom: 20px;
        }
        .idea-card {
            background: #444;
            margin: 10px;
            padding: 20px;
            width: 300px;
            border-radius: 5px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        .idea-card h3 {
            font-size: 18px;
        }
        .idea-card p {
            font-size: 14px;
            margin: 10px 0;
        }
        .log-out {
            position: absolute;
            top: 10px;
            right: 10px;
            font-size: 16px;
            background-color: red;
            color: white;
            border: none;
            padding: 10px;
            cursor: pointer;
        }
        .log-out:hover {
            background-color: darkred;
        }
        .plus-button {
            position: absolute;
            bottom: 20px;
            right: 20px;
            font-size: 36px;
            cursor: pointer;
        }
        .idea-list {
            text-align: left;
            margin-top: 20px;
        }
        .arrow-button {
            position: absolute;
            top: 10px;
            left: 10px;
            font-size: 24px;
            cursor: pointer;
            background-color: transparent;
            border: none;
            color: white;
            transition: color 0.3s ease;
        }
        .arrow-button:hover {
            color: #aaa;
        }

        /* Hide the plus button for investors */
        .investor .plus-button {
            display: none;
        }

        /* Style for the idea bar layout */
        .idea-bar {
            display: flex;
            align-items: center;
            justify-content: space-between;
            background-color: #444;
            padding: 10px;
            margin: 5px 0;
            border-radius: 5px;
            width: 80%;
            max-width: 600px;
            margin-left: auto;
            margin-right: auto;
        }
        .idea-bar button.delete {
            background-color: red;
            color: white;
            border: none;
            cursor: pointer;
        }
        .idea-bar button.delete:hover {
            background-color: darkred;
        }

        /* Style for the full idea detail */
        .idea-detail {
            background-color: #333;
            padding: 20px;
            margin: 20px 0;
            border-radius: 5px;
        }
        .idea-detail h3 {
            margin-top: 0;
        }
    </style>
</head>
<body>

    <!-- Tab 1: Ask Name -->
    <div class="tab active" id="tab1">
        <h2>Welcome to Idea Hub!</h2>
        <p>Please enter your name:</p>
        <input type="text" id="userName" placeholder="Enter your name" required>
        <button class="button" onclick="goToTab2()">Next</button>
    </div>

    <!-- Tab 2: Choose Investor or Inventor -->
    <div class="tab" id="tab2">
        <button class="arrow-button" onclick="goToTab1()">←</button>
        <h2>Hello, <span id="displayName"></span>! Are you an Investor or an Inventor?</h2>
        <button class="button" onclick="goToTab3('inventor')">Inventor</button>
        <button class="button" onclick="goToTab4('investor')">Investor</button>
    </div>

    <!-- Tab 3: Inventor - Post Idea -->
    <div class="tab" id="tab3">
        <button class="arrow-button" onclick="goToTab2()">←</button>
        <h2>Post Your Idea</h2>
        <input type="text" id="name" placeholder="Your Name">
        <input type="tel" id="phone" placeholder="Your Phone Number">
        <textarea id="ideaText" placeholder="Describe your idea"></textarea>
        <button class="button" onclick="postIdea()">Post Idea</button>
        <button class="log-out" onclick="logOut()">Log Out</button>
    </div>

    <!-- Tab 4: Hub of Ideas (For Investors and All Inventors) -->
    <div class="tab" id="tab4">
        <button class="arrow-button" onclick="goToTab2()">←</button>
        <h2>Idea Hub</h2>
        <div class="idea-container" id="ideaContainer"></div>
        <button class="log-out" onclick="logOut()">Log Out</button>
        <div class="plus-button">+</div>
        <div class="idea-list" id="yourIdeasButtonContainer"></div>
    </div>

    <!-- Tab 5: Your Ideas (For Inventors Only) -->
    <div class="tab" id="tab5">
        <button class="arrow-button" onclick="goToTab4(userType)">←</button>
        <h2>Your Posted Ideas</h2>
        <div id="yourIdeasContainer"></div>
        <button class="log-out" onclick="logOut()">Log Out</button>
    </div>

    <script>
        let currentUser = null;
        let ideas = [];
        let userType = '';

        // Function to navigate to Tab 2 (Name Entry)
        function goToTab2() {
            const name = document.getElementById("userName").value;
            if (name) {
                currentUser = name;
                document.getElementById("displayName").innerText = currentUser;
                setActiveTab('tab2');
            } else {
                alert("Please enter your name.");
            }
        }

        // Function to navigate to Tab 3 (Inventor) or Tab 4 (Investor)
        function goToTab3(type) {
            userType = type;
            setActiveTab('tab3');
        }

        // Function to navigate to Tab 4 (Hub of Ideas) for Investors
        function goToTab4(type) {
            userType = type;
            setActiveTab('tab4');
            const tab4 = document.getElementById('tab4');
            tab4.classList.remove('investor'); // Reset to default state
            if (userType === 'investor') {
                tab4.classList.add('investor'); // Add the investor class to hide the plus button
            }
            displayIdeas();
        }

        // Function to set the active tab
        function setActiveTab(tabId) {
            document.querySelectorAll('.tab').forEach(tab => tab.classList.remove('active'));
            document.getElementById(tabId).classList.add('active');
        }

        // Function to post an idea (for Inventors)
        function postIdea() {
            const name = document.getElementById("name").value;
            const phone = document.getElementById("phone").value;
            const ideaText = document.getElementById("ideaText").value;
            
            if (name && phone && ideaText) {
                const idea = {
                    name: name,
                    phone: phone,
                    text: ideaText
                };
                ideas.push(idea);
                alert("Your idea has been posted!");
                goToTab4(userType);
            } else {
                alert("Please fill out all fields.");
            }
        }

        // Display posted ideas in the Hub
        function displayIdeas() {
            const container = document.getElementById("ideaContainer");
            container.innerHTML = '';
            ideas.forEach((idea, index) => {
                const ideaCard = document.createElement('div');
                ideaCard.className = 'idea-card';
                ideaCard.innerHTML = `
                    <h3>Idea ${index + 1}</h3>
                    <p>${idea.text.substring(0, 100)}...</p>
                    <button class="button" onclick="viewIdeaDetail(${index})">Read More</button>
                `;
                container.appendChild(ideaCard);
            });
        }

        // View full idea details
        function viewIdeaDetail(index) {
            const idea = ideas[index];
            const ideaDetailContainer = document.createElement('div');
            ideaDetailContainer.className = 'idea-detail';
            ideaDetailContainer.innerHTML = `
                <h3>Full Idea:</h3>
                <p><strong>${idea.name}</strong> - ${idea.phone}</p>
                <p>${idea.text}</p>
            `;
            const existingDetail = document.querySelector('.idea-detail');
            if (existingDetail) {
                existingDetail.remove(); // Remove any existing idea detail
            }
            document.getElementById('ideaContainer').appendChild(ideaDetailContainer);
        }

        // Function to view your posted ideas (Inventor only)
        function viewYourIdeas() {
            setActiveTab('tab5');
            const container = document.getElementById("yourIdeasContainer");
            container.innerHTML = ''; // Clear the container before adding items
            ideas.forEach((idea, index) => {
                const ideaBar = document.createElement('div');
                ideaBar.className = 'idea-bar';
                ideaBar.innerHTML = `
                    <span>${idea.name}: ${idea.text}</span>
                    <button class="delete" onclick="deleteIdea(${index})">Delete</button>
                `;
                container.appendChild(ideaBar);
            });
        }

        // Function to delete a user's idea
        function deleteIdea(index) {
            ideas.splice(index, 1);
            viewYourIdeas(); // Refresh the list after deletion
        }

        // Log out function to reset all data
        function logOut() {
            currentUser = null;
            userType = '';
            ideas = [];
            setActiveTab('tab1');
            document.getElementById("userName").value = '';
        }
    </script>

</body>
</html>
