<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Student Form with Admin Login</title>
    <script src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY"></script>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; }
        form { max-width: 300px; margin: auto; }
        #map { height: 300px; width: 100%; margin-top: 20px; }
        #adminPanel { display: none; }
        #studentData { display: none; margin-top: 20px; }
        table { width: 100%; border-collapse: collapse; }
        th, td { border: 1px solid black; padding: 8px; text-align: left; }
    </style>
</head>
<body>
    <h2>Student Information Form</h2>
    <form id="studentForm">
        <label>Student Name:</label>
        <input type="text" id="name" required><br><br>
        
        <label>Father Name:</label>
        <input type="text" id="father" required><br><br>
        
        <label>Mobile Number:</label>
        <input type="tel" id="mobile" required><br><br>
        
        <button type="button" onclick="getLocation()">Mark My Location</button>
        <input type="hidden" id="latitude">
        <input type="hidden" id="longitude">
        
        <div id="map"></div>
        <br>
        <button type="button" onclick="saveStudentData()">Submit</button>
    </form>
    
    <h2>Admin Login</h2>
    <form id="loginForm">
        <label>Username:</label>
        <input type="text" id="adminUser" required><br><br>
        
        <label>Password:</label>
        <input type="password" id="adminPass" required><br><br>
        
        <button type="button" onclick="adminLogin()">Login</button>
    </form>
    
    <div id="adminPanel">
        <h2>Student Data</h2>
        <table>
            <thead>
                <tr>
                    <th>Student Name</th>
                    <th>Father Name</th>
                    <th>Mobile Number</th>
                    <th>Location</th>
                </tr>
            </thead>
            <tbody id="studentTableBody">
            </tbody>
        </table>
    </div>
    
    <script>
        function getLocation() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(showPosition, showError);
            } else {
                alert("Geolocation is not supported by this browser.");
            }
        }

        function showPosition(position) {
            var lat = position.coords.latitude;
            var lng = position.coords.longitude;
            document.getElementById("latitude").value = lat;
            document.getElementById("longitude").value = lng;
            var map = new google.maps.Map(document.getElementById("map"), {
                zoom: 15,
                center: { lat: lat, lng: lng }
            });
            new google.maps.Marker({
                position: { lat: lat, lng: lng },
                map: map,
                title: "Your Location"
            });
        }

        function showError(error) {
            alert("Error in fetching location: " + error.message);
        }

        function saveStudentData() {
            var name = document.getElementById("name").value;
            var father = document.getElementById("father").value;
            var mobile = document.getElementById("mobile").value;
            var latitude = document.getElementById("latitude").value;
            var longitude = document.getElementById("longitude").value;
            
            if (name && father && mobile && latitude && longitude) {
                var studentData = JSON.parse(localStorage.getItem("students")) || [];
                studentData.push({ name, father, mobile, latitude, longitude });
                localStorage.setItem("students", JSON.stringify(studentData));
                alert("Data saved successfully!");
                document.getElementById("studentForm").reset();
            } else {
                alert("Please fill all fields and mark your location.");
            }
        }
        
        function adminLogin() {
            var user = document.getElementById("adminUser").value;
            var pass = document.getElementById("adminPass").value;
            if (user === "admin" && pass === "password123") {
                document.getElementById("loginForm").style.display = "none";
                document.getElementById("adminPanel").style.display = "block";
                loadStudentData();
            } else {
                alert("Invalid login credentials");
            }
        }
        
        function loadStudentData() {
            var studentData = JSON.parse(localStorage.getItem("students")) || [];
            var tableBody = document.getElementById("studentTableBody");
            tableBody.innerHTML = "";
            studentData.forEach(student => {
                var row = `<tr>
                    <td>${student.name}</td>
                    <td>${student.father}</td>
                    <td>${student.mobile}</td>
                    <td><a href="https://www.google.com/maps?q=${student.latitude},${student.longitude}" target="_blank">View Location</a></td>
                </tr>`;
                tableBody.innerHTML += row;
            });
        }
    </script>
</body>
</html>
