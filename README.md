# web_odev
todo_app
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL,
    password VARCHAR(255) NOT NULL
);

CREATE TABLE tasks (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    task VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
<?php
session_start();

$conn = new mysqli("localhost", "root", "", "todo_app");

if ($conn->connect_error) {
    die("Bağlantı hatası: " . $conn->connect_error);
}
?>
<?php
include "config.php";

if ($_POST) {
    $username = $_POST['username'];
    $email = $_POST['email'];
    $password = password_hash($_POST['password'], PASSWORD_DEFAULT);

    $conn->query("INSERT INTO users (username,email,password) 
                  VALUES ('$username','$email','$password')");
    header("Location: login.php");
}
?>

<form method="POST">
    <h2>Kayıt Ol</h2>
    <input type="text" name="username" placeholder="Kullanıcı Adı" required>
    <input type="email" name="email" placeholder="Email" required>
    <input type="password" name="password" placeholder="Şifre" required>
    <button>Kayıt Ol</button>
</form>
<?php
include "config.php";

if ($_POST) {
    $email = $_POST['email'];
    $password = $_POST['password'];

    $result = $conn->query("SELECT * FROM users WHERE email='$email'");
    $user = $result->fetch_assoc();

    if ($user && password_verify($password, $user['password'])) {
        $_SESSION['user_id'] = $user['id'];
        header("Location: index.php");
    } else {
        echo "Hatalı giriş!";
    }
}
?>

<form method="POST">
    <h2>Giriş Yap</h2>
    <input type="email" name="email" placeholder="Email" required>
    <input type="password" name="password" placeholder="Şifre" required>
    <button>Giriş Yap</button>
</form>
<?php
include "config.php";

if (!isset($_SESSION['user_id'])) {
    header("Location: login.php");
}

$user_id = $_SESSION['user_id'];
$tasks = $conn->query("SELECT * FROM tasks WHERE user_id=$user_id");
?>

<h2>Görevlerim</h2>

<form action="add-task.php" method="POST">
    <input type="text" name="task" placeholder="Yeni görev..." required>
    <button>Ekle</button>
</form>

<ul>
<?php while($row = $tasks->fetch_assoc()): ?>
    <li>
        <?= $row['task'] ?>
        <a href="delete-task.php?id=<?= $row['id'] ?>">Sil</a>
    </li>
<?php endwhile; ?>
</ul>

<a href="logout.php">Çıkış Yap</a>
<?php
include "config.php";

if ($_POST) {
    $task = $_POST['task'];
    $user_id = $_SESSION['user_id'];

    $conn->query("INSERT INTO tasks (user_id, task) 
                  VALUES ('$user_id','$task')");
}

header("Location: index.php");
<?php
include "config.php";

$id = $_GET['id'];
$conn->query("DELETE FROM tasks WHERE id=$id");

header("Location: index.php");
body {
    font-family: Arial;
    background: #f2f2f2;
    text-align: center;
}

form {
    margin: 20px;
}

input {
    padding: 8px;
    margin: 5px;
}

button {
    padding: 8px 15px;
    background: #4CAF50;
    color: white;
    border: none;
}
