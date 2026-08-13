# Minh.site
Game top-up 
<?php

header("Content-Type: application/json");

// ===== CONFIG =====
$API_URL = "https://example.com/api/topup";
$API_KEY = "YOUR_TEST_API_KEY";

// ===== INPUT =====
$game = $_POST["game"] ?? "";
$player_id = $_POST["player_id"] ?? "";
$server_id = $_POST["server_id"] ?? "";
$product_id = $_POST["product_id"] ?? "";

// ===== VALIDATION =====
if (!$game || !$player_id || !$product_id) {
    echo json_encode([
        "success" => false,
        "message" => "Missing required fields"
    ]);
    exit;
}

// ===== REQUEST =====
$data = [
    "game" => $game,
    "player_id" => $player_id,
    "server_id" => $server_id,
    "product_id" => $product_id
];

$ch = curl_init($API_URL);

curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data));

curl_setopt($ch, CURLOPT_HTTPHEADER, [
    "Content-Type: application/json",
    "Authorization: Bearer " . $API_KEY
]);

$response = curl_exec($ch);
$http_code = curl_getinfo($ch, CURLINFO_HTTP_CODE);

if (curl_errno($ch)) {
    echo json_encode([
        "success" => false,
        "message" => curl_error($ch)
    ]);
    curl_close($ch);
    exit;
}

curl_close($ch);

// ===== RESPONSE =====
echo json_encode([
    "success" => $http_code >= 200 && $http_code < 300,
    "status_code" => $http_code,
    "provider_response" => json_decode($response, true) ?? $response
]);
?>
