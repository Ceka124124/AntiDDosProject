<?php
session_start();

$max_requests_per_second = 5; 
$ban_time = 300; 

$ip = $_SERVER['REMOTE_ADDR'];
$time = time();

$log_file = 'ip_logs.json';
if (!file_exists($log_file)) {
    file_put_contents($log_file, json_encode([]));
}

$logs = json_decode(file_get_contents($log_file), true);
if (!isset($logs[$ip])) {
    $logs[$ip] = ['requests' => [], 'banned_until' => 0, 'ban_count' => 0];
}

if ($logs[$ip]['banned_until'] > $time) {
    die("Amına Kodum - Yasaklandınız!");
}

$logs[$ip]['requests'] = array_filter($logs[$ip]['requests'], function ($timestamp) use ($time) {
    return ($timestamp > $time - 1);
});

$logs[$ip]['requests'][] = $time;


if (count($logs[$ip]['requests']) > $max_requests_per_second) {
    $logs[$ip]['ban_count'] += 1;
    $logs[$ip]['banned_until'] = $time + $ban_time;

    if ($logs[$ip]['ban_count'] >= 5) { 
        file_put_contents("banned_ips.txt", $ip . PHP_EOL, FILE_APPEND);
        die("Kalıcı yasak yediniz!");
    }

    file_put_contents($log_file, json_encode($logs));
    die("Amına Kodum - Çok fazla istek gönderildi, yasaklandınız!");
}

file_put_contents($log_file, json_encode($logs));


$banned_ips = file("kalici_yasak.txt", FILE_IGNORE_NEW_LINES);
if (in_array($ip, $banned_ips)) {
    die("Kalıcı Yasak! Erişiminiz Engellendi! Sg");
}

$bot_agents = ['bot', 'crawl', 'spider', 'wget', 'curl', 'python', 'httpclient'];
foreach ($bot_agents as $bot) {
    if (stripos($_SERVER['HTTP_USER_AGENT'], $bot) !== false) {
        die("Amına Kodum - DDos tespit edildi!");
    }
}

function waf_filter($data) {
    $bad_words = ['union', 'select', 'drop', 'insert', 'update', '<script>', '<?php', 'base64_'];
    foreach ($bad_words as $bad) {
        if (stripos($data, $bad) !== false) {
            die("Şüpheli İstek Engellendi!");
        }
    }
    return htmlspecialchars($data, ENT_QUOTES, 'UTF-8');
}
$_GET = array_map('waf_filter', $_GET);
$_POST = array_map('waf_filter', $_POST);
if (!isset($_SESSION['last_visit'])) {
    $_SESSION['last_visit'] = time();
} else {
    $time_diff = time() - $_SESSION['last_visit'];
    if ($time_diff < 1) { 
        die("Anormal trafik tespit edildi, erişim engellendi!");
    }
    $_SESSION['last_visit'] = time();
}

?>

<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Anti DDos Aktif</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background: black;
            color: lime;
            font-family: 'Courier New', monospace;
            text-align: center;
        }

        .hacker-bg {
            position: fixed;
            width: 100%;
            height: 100%;
            background: url('https://pin.it/6bnEUCpfZ.gif') no-repeat center center;
            background-size: cover;
            opacity: 0.2;
        }

        .content {
            position: relative;
            z-index: 2;
            margin-top: 20%;
        }

        .matrix {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
        }

        .matrix span {
            position: absolute;
            color: lime;
            font-size: 20px;
            animation: matrixFall linear infinite;
        }

        @keyframes matrixFall {
            from {
                transform: translateY(-100%);
                opacity: 1;
            }
            to {
                transform: translateY(100vh);
                opacity: 0;
            }
        }
    </style>
</head>
<body>

    <div class="hacker-bg"></div>
    <div class="content">
        <h1>DDos Vursana :D</h1>
        <p>ANTI DDOS - Koruma Çalışıyor</p>
    </div>

    <div class="matrix"></div>

    <script>
        function createMatrixEffect() {
            let matrix = document.querySelector('.matrix');
            for (let i = 0; i < 50; i++) {
                let span = document.createElement('span');
                span.innerText = Math.random() > 0.5 ? '0' : '1';
                span.style.left = Math.random() * 100 + 'vw';
                span.style.animationDuration = Math.random() * 2 + 3 + 's';
                matrix.appendChild(span);
            }
        }
        createMatrixEffect();
    </script>

</body>
</html>
