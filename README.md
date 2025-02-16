# Calculator
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Basic Calculator</title>
    <style>
        *, *::before, *::after {
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            font-weight: normal;
        }

        body {
            margin: 0;
            padding: 0;
            background: linear-gradient(to right, #00AAFF, #00FF6C);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .calculator {
            background-color: rgba(255, 255, 255, 0.9);
            border-radius: 10px;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.25);
            width: 350px;
            overflow: hidden;
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            grid-template-rows: minmax(100px, auto) repeat(5, 70px);
            gap: 1px;
            padding: 1px;
        }

        .display {
            grid-column: 1 / -1;
            background-color: rgba(0, 0, 0, 0.8);
            color: white;
            display: flex;
            flex-direction: column;
            align-items: flex-end;
            justify-content: space-around;
            padding: 10px;
            word-wrap: break-word;
            word-break: break-all;
        }

        .previous-operand {
            color: rgba(255, 255, 255, 0.75);
            font-size: 1.5rem;
            min-height: 30px;
        }

        .current-operand {
            color: white;
            font-size: 2.5rem;
            min-height: 50px;
        }

        button {
            border: none;
            outline: none;
            background-color: rgba(255, 255, 255, 0.75);
            font-size: 1.5rem;
            cursor: pointer;
            transition: 0.2s;
        }

        button:hover {
            background-color: rgba(255, 255, 255, 0.9);
        }

        button:active {
            background-color: rgba(255, 255, 255, 0.6);
        }

        .span-two {
            grid-column: span 2;
        }

        .operator {
            background-color: rgba(255, 166, 0, 0.8);
            color: white;
        }

        .operator:hover {
            background-color: rgba(255, 166, 0, 1);
        }

        #all-clear, #clear {
            background-color: rgba(255, 50, 50, 0.8);
            color: white;
        }

        #all-clear:hover, #clear:hover {
            background-color: rgba(255, 50, 50, 1);
        }

        #equals {
            background-color: rgba(0, 128, 0, 0.8);
            color: white;
        }

        #equals:hover {
            background-color: rgba(0, 128, 0, 1);
        }

        /* Media query for smaller screens */
        @media (max-width: 400px) {
            .calculator {
                width: 95%;
                grid-template-rows: minmax(80px, auto) repeat(5, 60px);
            }
            
            button {
                font-size: 1.2rem;
            }
            
            .previous-operand {
                font-size: 1.2rem;
            }
            
            .current-operand {
                font-size: 2rem;
            }
        }
    </style>
</head>
<body>
    <div class="calculator">
        <div class="display">
            <div class="previous-operand" id="previous-operand"></div>
            <div class="current-operand" id="current-operand">0</div>
        </div>
        <button class="span-two" id="all-clear">AC</button>
        <button id="clear">C</button>
        <button class="operator" id="divide">÷</button>
        <button class="number" id="seven">7</button>
        <button class="number" id="eight">8</button>
        <button class="number" id="nine">9</button>
        <button class="operator" id="multiply">×</button>
        <button class="number" id="four">4</button>
        <button class="number" id="five">5</button>
        <button class="number" id="six">6</button>
        <button class="operator" id="subtract">-</button>
        <button class="number" id="one">1</button>
        <button class="number" id="two">2</button>
        <button class="number" id="three">3</button>
        <button class="operator" id="add">+</button>
        <button class="span-two number" id="zero">0</button>
        <button class="number" id="decimal">.</button>
        <button class="operator" id="equals">=</button>
    </div>
    
    <script>
        // Get DOM elements
        const previousOperandElement = document.getElementById('previous-operand');
        const currentOperandElement = document.getElementById('current-operand');
        const numberButtons = document.querySelectorAll('.number');
        const operatorButtons = document.querySelectorAll('.operator');
        const allClearButton = document.getElementById('all-clear');
        const clearButton = document.getElementById('clear');
        const equalsButton = document.getElementById('equals');

        // Initialize calculator state
        let currentOperand = '0';
        let previousOperand = '';
        let operation = undefined;
        let shouldResetCurrentOperand = false;

        // Update the display
        function updateDisplay() {
            currentOperandElement.textContent = currentOperand;
            
            if (operation != null) {
                previousOperandElement.textContent = `${previousOperand} ${operation}`;
            } else {
                previousOperandElement.textContent = previousOperand;
            }
        }

        // Append number to current operand
        function appendNumber(number) {
            if (shouldResetCurrentOperand) {
                currentOperand = number;
                shouldResetCurrentOperand = false;
            } else {
                // Handle special cases
                if (number === '.' && currentOperand.includes('.')) return;
                if (currentOperand === '0' && number !== '.') {
                    currentOperand = number;
                } else {
                    currentOperand += number;
                }
            }
            updateDisplay();
        }

        // Choose operation
        function chooseOperation(op) {
            if (currentOperand === '') return;
            
            if (previousOperand !== '') {
                compute();
            }
            
            operation = op;
            previousOperand = currentOperand;
            currentOperand = '';
            updateDisplay();
        }

        // Perform calculation
        function compute() {
            let computation;
            const prev = parseFloat(previousOperand);
            const current = parseFloat(currentOperand);
            
            if (isNaN(prev) || isNaN(current)) return;
            
            switch (operation) {
                case '+':
                    computation = prev + current;
                    break;
                case '-':
                    computation = prev - current;
                    break;
                case '×':
                    computation = prev * current;
                    break;
                case '÷':
                    if (current === 0) {
                        currentOperand = 'Error';
                        previousOperand = '';
                        operation = undefined;
                        updateDisplay();
                        return;
                    }
                    computation = prev / current;
                    break;
                default:
                    return;
            }
            
            // Format the result
            currentOperand = computation.toString();
            operation = undefined;
            previousOperand = '';
            shouldResetCurrentOperand = true;
            updateDisplay();
        }

        // Clear last entry
        function clearEntry() {
            currentOperand = '0';
            updateDisplay();
        }

        // Clear all entries
        function clearAll() {
            currentOperand = '0';
            previousOperand = '';
            operation = undefined;
            updateDisplay();
        }

        // Set up event listeners
        numberButtons.forEach(button => {
            button.addEventListener('click', () => {
                appendNumber(button.textContent);
            });
        });

        operatorButtons.forEach(button => {
            button.addEventListener('click', () => {
                if (button.id === 'equals') {
                    compute();
                } else {
                    chooseOperation(button.textContent);
                }
            });
        });

        clearButton.addEventListener('click', clearEntry);
        allClearButton.addEventListener('click', clearAll);

        // Handle keyboard input
        document.addEventListener('keydown', (event) => {
            if (/[0-9]/.test(event.key)) {
                appendNumber(event.key);
            } else if (event.key === '.') {
                appendNumber('.');
            } else if (event.key === '+') {
                chooseOperation('+');
            } else if (event.key === '-') {
                chooseOperation('-');
            } else if (event.key === '*') {
                chooseOperation('×');
            } else if (event.key === '/') {
                event.preventDefault(); // Prevent browser's find functionality
                chooseOperation('÷');
            } else if (event.key === 'Enter' || event.key === '=') {
                event.preventDefault(); // Prevent form submission if inside a form
                compute();
            } else if (event.key === 'Backspace') {
                clearEntry();
            } else if (event.key === 'Escape') {
                clearAll();
            }
        });

        // Initialize display
        updateDisplay();
    </script>
</body>
</html>
