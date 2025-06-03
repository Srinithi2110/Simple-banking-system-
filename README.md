# Simple-banking-system-
<?php
include("db.php");

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $name = $_POST["name"];
    $email = $_POST["email"];
    $password = $_POST["password"];
    $confirm_password = $_POST["confirm_password"];

    // Check if passwords match
    if ($password != $confirm_password) {
        $error = "Passwords do not match!";
    } else {
        // Check if email already exists
        $check_sql = "SELECT * FROM users WHERE email='$email'";
        $check_result = $conn->query($check_sql);

        if ($check_result->num_rows > 0) {
            $error = "Email is already registered!";
        } else {
            // Insert user into users table
            $insert_user = "INSERT INTO users (name, email, password) VALUES ('$name', '$email', '$password')";
            if ($conn->query($insert_user) === TRUE) {
                $user_id = $conn->insert_id;

                // Create account with zero balance
                $create_account = "INSERT INTO accounts (user_id, balance) VALUES ($user_id, 0.00)";
                $conn->query($create_account);

                $success = "Registration successful! You can now <a href='index.php'>login</a>.";
            } else {
                $error = "Error: " . $conn->error;
            }
        }
    }
}
?>

<!DOCTYPE html>
<html>
<head>
    <title>Register - Simple Banking System</title>
</head>
<body>
    <h2>Create New Account</h2>
    <?php 
    if (isset($error)) echo "<p style='color:red;'>$error</p>"; 
    if (isset($success)) echo "<p style='color:green;'>$success</p>"; 
    ?>
    <form method="POST" action="">
        Name: <input type="text" name="name" required><br><br>
        Email: <input type="email" name="email" required><br><br>
        Password: <input type="password" name="password" required><br><br>
        Confirm Password: <input type="password" name="confirm_password" required><br><br>
        <input type="submit" value="Register">
    </form>
    <p>Already have an account? <a href="index.php">Login here</a></p>
</body>
</html>
