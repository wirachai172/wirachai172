<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ตารางบันทึกรายวัน</title>
    <style>
        body {
            font-family: 'Roboto', sans-serif;
            background-color: #f5f5f5;
            color: #333;
            margin: 0;
            padding: 0;
            background-image: url('https://example.com/demon-slayer-background.jpg');
            background-size: cover;
            background-position: center;
        }
        .user-info {
            position: absolute;
            top: 10px;
            right: 10px;
            display: flex;
            align-items: center;
        }
        .user-info span {
            margin-right: 10px;
            font-weight: bold;
        }
        .logout-button {
            background-color: #ff4d4d;
            color: #fff;
            border: none;
            padding: 8px 16px;
            border-radius: 4px;
            cursor: pointer;
        }
        .logout-button:hover {
            background-color: #cc0000;
        }
        h2 {
            text-align: center;
            color: #1d2d50;
            margin: 20px 0;
            font-family: 'Montserrat', sans-serif;
        }
        .container {
            width: 80%;
            margin: 20px auto;
        }
        table {
            width: 100%;
            margin: 20px auto;
            border-collapse: collapse;
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }
        table, th, td {
            border: 1px solid #ddd;
            text-align: left;
        }
        th, td {
            padding: 12px;
        }
        th {
            background-color: #1d2d50;
            color: #fff;
        }
        td {
            background-color: #f9f9f9;
        }
        form {
            margin: 20px auto;
            width: 100%;
            background-color: rgba(255, 255, 255, 0.8);
            border-radius: 8px;
            padding: 20px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }
        label {
            display: block;
            margin-bottom: 8px;
            font-weight: bold;
        }
        input[type="date"],
        input[type="text"],
        input[type="number"] {
            width: calc(100% - 22px);
            padding: 10px;
            margin-bottom: 10px;
            border-radius: 4px;
            border: 1px solid #ddd;
        }
        button {
            background-color: #1d2d50;
            color: #fff;
            border: none;
            padding: 10px 20px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
            font-weight: bold;
        }
        button:hover {
            background-color: #0a1f3e;
        }
        p {
            text-align: center;
            font-size: 18px;
            margin: 10px 0;
        }
        .user-selector {
            margin: 20px 0;
            text-align: center;
        }
    </style>
</head>
<body>

<div class="user-info">
    <span id="user-name">ผู้ใช้: <span id="username-display"></span></span>
    <button class="logout-button" id="logout-button">ล็อกเอ้าท์</button>
</div>

<div class="container">
    <h2>ตารางบันทึกรายวัน</h2>

    <form id="daily-log-form">
        <label for="date">วันที่:</label>
        <input type="date" id="date" name="date" required>
        <label for="description">รายละเอียด:</label>
        <input type="text" id="description" name="description" required>
        <label for="amount">จำนวนเงิน (บาท):</label>
        <input type="number" id="amount" name="amount" required>
        <button type="submit">บันทึก</button>
    </form>

    <table id="daily-log-table">
        <thead>
            <tr>
                <th>ลำดับ</th>
                <th>วันที่</th>
                <th>รายละเอียด</th>
                <th>จำนวนเงิน (บาท)</th>
            </tr>
        </thead>
        <tbody>
            <!-- Rows will be added dynamically -->
        </tbody>
    </table>

    <button id="calculate-total">คำนวณจำนวนเงินรวม</button>
    <p id="total-amount">จำนวนเงินรวม: 0 บาท</p>
    <p id="tax-amount">ภาษี (3%): 0 บาท</p>
    <p id="amount-after-tax">จำนวนเงินหลังหักภาษี: 0 บาท</p>
</div>

<script>
    const form = document.getElementById('daily-log-form');
    const tableBody = document.querySelector('#daily-log-table tbody');
    const logoutButton = document.getElementById('logout-button');
    const usernameDisplay = document.getElementById('username-display');

    let rowCount = 0;

    function loadFromLocalStorage(username) {
        tableBody.innerHTML = '';
        rowCount = 0;

        if (!username) return;

        let logs = JSON.parse(localStorage.getItem(username)) || [];

        logs.forEach(function(log) {
            const newRow = `
                <tr>
                    <td>${++rowCount}</td>
                    <td>${log.date}</td>
                    <td>${log.description}</td>
                    <td>${log.amount}</td>
                </tr>
            `;
            tableBody.insertAdjacentHTML('beforeend', newRow);
        });

        updateCalculations();
    }

    function saveToLocalStorage(username, date, description, amount) {
        let log = {
            date: date,
            description: description,
            amount: amount
        };

        let logs = JSON.parse(localStorage.getItem(username)) || [];
        logs.push(log);
        localStorage.setItem(username, JSON.stringify(logs));
    }

    function updateCalculations() {
        let totalAmount = 0;
        tableBody.querySelectorAll('tr').forEach(function(row) {
            const amountCell = row.querySelectorAll('td')[3];
            if (amountCell) {
                totalAmount += parseFloat(amountCell.textContent) || 0;
            }
        });

        const tax = totalAmount * 0.03;
        const amountAfterTax = totalAmount - tax;

        document.getElementById('total-amount').textContent = `จำนวนเงินรวม: ${totalAmount.toFixed(2)} บาท`;
        document.getElementById('tax-amount').textContent = `ภาษี (3%): ${tax.toFixed(2)} บาท`;
        document.getElementById('amount-after-tax').textContent = `จำนวนเงินหลังหักภาษี: ${amountAfterTax.toFixed(2)} บาท`;
    }

    document.getElementById('calculate-total').addEventListener('click', function() {
        updateCalculations();
    });

    form.addEventListener('submit', function(event) {
        event.preventDefault();
        
        const date = document.getElementById('date').value;
        const description = document.getElementById('description').value;
        const amount = parseInt(document.getElementById('amount').value);
        const username = localStorage.getItem('currentUser');

        if (!username) {
            alert('กรุณาล็อกอินก่อน');
            return;
        }

        document.getElementById('date').value = '';
        document.getElementById('description').value = '';
        document.getElementById('amount').value = '';

        const newRow = `
            <tr>
                <td>${++rowCount}</td>
                <td>${date}</td>
                <td>${description}</td>
                <td>${amount}</td>
            </tr>
        `;
        tableBody.insertAdjacentHTML('beforeend', newRow);

        saveToLocalStorage(username, date, description, amount);
        updateCalculations();
    });

    logoutButton.addEventListener('click', function() {
        localStorage.removeItem('currentUser');
        window.location.href = 'login.html'; // Redirect to login page
    });

    // Load user info and logs on page load
    document.addEventListener('DOMContentLoaded', function() {
        const username = localStorage.getItem('currentUser');
        if (username) {
            usernameDisplay.textContent = username;
            loadFromLocalStorage(username);
        } else {
            window.location.href = 'login.html'; // Redirect to login page if not logged in
        }
    });
</script>

</body>
</html>
