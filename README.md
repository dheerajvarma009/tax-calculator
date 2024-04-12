# tax-calculator
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Tax Calculator</title>
<style>
    body {
        font-family: Arial, sans-serif;
        margin: 0;
        padding: 0;
        background-color: #f4f4f4;
    }

    .container {
        width: 400px;
        margin: 50px auto;
        background-color: #fff;
        padding: 20px;
        border-radius: 8px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        position: relative; 
    }

    .input-group {
        margin-bottom: 15px;
        position: relative; 
    }

    .input-group label {
        display: block;
        margin-bottom: 5px;
    }

    .input-group input, .input-group select {
        width: calc(100% - 30px); 
        padding: 8px;
        border: 1px solid #ccc;
        border-radius: 4px;
    }

    .error-icon {
        position: absolute;
        right: 5px;
        top: 50%;
        transform: translateY(-50%);
        cursor: pointer;
        display: none; /* Initially hidden */
    }

    .btn {
        display: block;
        width: 100%;
        padding: 10px;
        border: none;
        border-radius: 4px;
        background-color: #a34caf;
        color: #fff;
        cursor: pointer;
    }

    .btn:hover {
        background-color: #9745a0;
    }

    /* Popup styles */
    .popup {
        display: none;
        position: fixed;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        background-color: #fff;
        padding: 20px;
        border-radius: 8px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        z-index: 1000; 
        width: 500px; 
        max-width: 90%;
        height: 300px; 
        max-height: 80%; 
    }

    .close {
        position: absolute;
        top: 5px;
        right: 10px;
        cursor: pointer;
    }

    .result {
        margin-top: 20px;
    }

    /* Tooltip styles */
    .tooltip {
        position: absolute;
        background-color: #333;
        color: #fff;
        padding: 5px;
        border-radius: 4px;
        display: none;
        z-index: 1;
    }
</style>
</head>
<body>

<div class="container">
    <div class="input-group">
        <label for="grossIncome">Enter Gross Annual Income:</label>
        <div style="position:relative;">
            <span style="position:absolute;left:10px;top:7px;"></span>
            <span data-direction="w" aria-hidden="true" id=":r2:" class="Tooltip__StyledTooltip-sc-e45c7z-0 jmgTxV" popover="auto" style="top: 279.35px; left: 455.262px;">!</span>
         <input type="text" id="grossIncome">
            <div class="error-icon" id="grossIncomeError" onclick="showTooltip('grossIncomeError', 'Enter only numericals')">!</div>
        </div>
    </div>
    <div class="input-group">
        <label for="extraIncome">Enter Extra Income:</label>
        <input type="text" id="extraIncome">
        <div class="error-icon" id="extraIncomeError" onclick="showTooltip('extraIncomeError', 'Enter only numericals')">!</div>
    </div>
    <div class="input-group">
        <label for="ageGroup">Enter Age:</label>
        <input type="text" id="ageGroup">
        <div class="error-icon" id="ageGroupError" onclick="showTooltip('ageGroupError', 'Enter only numericals')">!</div>
    </div>
    <div class="input-group">
        <label for="deductions">Enter Total Applicable Deductions:</label>
        <input type="text" id="deductions">
        <div class="error-icon" id="deductionsError" onclick="showTooltip('deductionsError', 'Enter only numericals')">!</div>
    </div>
    <button class="btn" onclick="validateInputs()">Submit</button>
</div>

<!-- Popup for displaying overall income -->
<div class="popup" id="popup">
    <span class="close" onclick="closePopup()">&times;</span>
    <div id="result" class="result"></div>
    <button class="btn" onclick="closePopup()">Close</button>
</div>

<!-- Tooltip for error messages -->
<div class="tooltip" id="tooltip"></div>

<script>
    function showTooltip(errorId, message) {
        var errorElement = document.getElementById(errorId);
        var tooltip = document.getElementById('tooltip');
        tooltip.innerHTML = message;
        tooltip.style.display = 'block';
        tooltip.style.top = errorElement.offsetTop + errorElement.offsetHeight + 'px';
        tooltip.style.left = errorElement.offsetLeft + 'px';
    }

    function hideTooltips() {
        var tooltips = document.getElementsByClassName('tooltip');
        for (var i = 0; i < tooltips.length; i++) {
            tooltips[i].style.display = 'none';
        }
    }

    function validateInputs() {
        hideTooltips();
        var grossIncome = parseFloat(document.getElementById('grossIncome').value);
        var extraIncome = parseFloat(document.getElementById('extraIncome').value);
        var age = parseInt(document.getElementById('ageGroup').value);
        var deductions = parseFloat(document.getElementById('deductions').value);

        var valid = true;

        if (isNaN(grossIncome) || grossIncome <= 0 || !(/^\d+$/.test(grossIncome))) {
            valid = false;
            showTooltip('grossIncomeError', 'Invalid Gross Income');
        }

        if (isNaN(extraIncome) || extraIncome < 0 || !(/^\d+$/.test(extraIncome))) {
            valid = false;
            showTooltip('extraIncomeError', 'Invalid Extra Income');
        }

        if (isNaN(age) || age <= 0 || !(/^\d+$/.test(age))) {
            valid = false;
            showTooltip('ageGroupError', 'Invalid Age');
        }

        if (isNaN(deductions) || deductions < 0 || !(/^\d+$/.test(deductions))) {
            valid = false;
            showTooltip('deductionsError', 'Invalid Deductions');
        }

        if (valid) {
            calculateTax(grossIncome, extraIncome, age, deductions);
        }
    }

    function calculateTax(grossIncome, extraIncome, age, deductions) {
        var totalIncome = grossIncome + extraIncome - deductions;
        var tax = 0;

        if (totalIncome > 800000) {
            var taxableAmount = totalIncome - 800000;
            if (age < 40) {
                if (taxableAmount <= 1000000) {
                    tax = 0.3 * taxableAmount;
                } else {
                    tax = 0.3 * 1000000 + 0.4 * (taxableAmount - 2000000);
                }
            } else if (age >= 40 && age < 60) {
                if (taxableAmount <= 2000000) {
                    tax = 0.4 * taxableAmount;
                } else {
                    tax = 0.4 * 2000000 + 0.4 * (taxableAmount - 2000000);
                }
            } else {
                if (taxableAmount <= 2000000) {
                    tax = 0.1 * taxableAmount;
                } else {
                    tax = 0.1 * 2000000 + 0.4 * (taxableAmount - 2000000);
                }
            }
        }

        if (tax === 0) {
            document.getElementById('result').innerHTML = "No tax applicable.";
        } else {
            document.getElementById('result').innerHTML = "your overall income will be : " + tax.toFixed(2) + " Lakhs"+ "<br> after all deductions";
        } 

        var popup = document.getElementById('popup');
        popup.style.display = 'block';
    }

    function closePopup() {
        var popup = document.getElementById('popup');
        popup.style.display = 'none';
    }
</script>

</body>
</html>
