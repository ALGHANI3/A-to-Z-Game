<!DOCTYPE html>
<html lang="en" dir="ltr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Daily Buy & Sell Tracker</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdfmake/0.1.68/pdfmake.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdfmake/0.1.68/vfs_fonts.js"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        /* Ensure table content is aligned left for LTR */
        th, td {
            text-align: left;
            padding-left: 0.75rem; /* Tailwind's pl-3 */
            padding-right: 0.75rem; /* Tailwind's pr-3 */
        }
        .remove-btn {
            background-color: #ef4444; /* Tailwind red-500 */
            color: white;
            padding: 0.25rem 0.5rem; /* Tailwind px-2 py-1 */
            border-radius: 0.375rem; /* Tailwind rounded-md */
            font-size: 0.875rem; /* Tailwind text-sm */
            border: none;
            cursor: pointer;
        }
        .remove-btn:hover {
            background-color: #dc2626; /* Tailwind red-600 */
        }
        .action-column {
            width: 100px; /* Fixed width for action column */
        }
    </style>
</head>
<body class="bg-gray-100">
    <div class="container mx-auto p-4 sm:p-6 md:p-8">
        <header class="mb-8 text-center">
            <h1 class="text-3xl sm:text-4xl font-bold text-teal-700">Daily Buy & Sell Tracker</h1>
        </header>

        <section class="mb-8 bg-white p-6 rounded-xl shadow-lg">
            <h2 class="text-2xl font-semibold text-teal-600 mb-5 border-b pb-3">Add New Item</h2>
            <form id="addItemForm" class="space-y-4">
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                    <div>
                        <label for="itemName" class="block text-sm font-medium text-gray-700 mb-1">Item Name</label>
                        <input type="text" id="itemName" name="itemName" required class="mt-1 block w-full px-3 py-2 bg-white border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-teal-500 focus:border-teal-500 sm:text-sm">
                    </div>
                    <div>
                        <label for="quantity" class="block text-sm font-medium text-gray-700 mb-1">Quantity</label>
                        <input type="number" id="quantity" name="quantity" required min="0" class="mt-1 block w-full px-3 py-2 bg-white border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-teal-500 focus:border-teal-500 sm:text-sm">
                    </div>
                </div>
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                    <div>
                        <label for="purchasePrice" class="block text-sm font-medium text-gray-700 mb-1">Total Purchase Price</label>
                        <input type="number" id="purchasePrice" name="purchasePrice" required min="0" step="any" class="mt-1 block w-full px-3 py-2 bg-white border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-teal-500 focus:border-teal-500 sm:text-sm">
                    </div>
                    <div>
                        <label for="salePrice" class="block text-sm font-medium text-gray-700 mb-1">Total Sale Price</label>
                        <input type="number" id="salePrice" name="salePrice" required min="0" step="any" class="mt-1 block w-full px-3 py-2 bg-white border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-teal-500 focus:border-teal-500 sm:text-sm">
                    </div>
                </div>
                <div>
                    <button type="submit" class="w-full sm:w-auto mt-2 px-6 py-2.5 bg-teal-600 text-white font-medium text-sm leading-tight uppercase rounded-lg shadow-md hover:bg-teal-700 hover:shadow-lg focus:bg-teal-700 focus:shadow-lg focus:outline-none focus:ring-0 active:bg-teal-800 active:shadow-lg transition duration-150 ease-in-out">
                        Add Item
                    </button>
                </div>
            </form>
        </section>

        <section class="mb-8 bg-white p-6 rounded-xl shadow-lg">
            <h2 class="text-2xl font-semibold text-teal-600 mb-5 border-b pb-3">Items List</h2>
            <div class="overflow-x-auto">
                <table id="itemsTable" class="min-w-full divide-y divide-gray-200">
                    <thead class="bg-gray-50">
                        <tr>
                            <th scope="col" class="px-6 py-3 text-xs font-medium text-gray-500 uppercase tracking-wider">Item Name</th>
                            <th scope="col" class="px-6 py-3 text-xs font-medium text-gray-500 uppercase tracking-wider">Quantity</th>
                            <th scope="col" class="px-6 py-3 text-xs font-medium text-gray-500 uppercase tracking-wider">Total Purchase Price</th>
                            <th scope="col" class="px-6 py-3 text-xs font-medium text-gray-500 uppercase tracking-wider">Total Sale Price</th>
                            <th scope="col" class="px-6 py-3 text-xs font-medium text-gray-500 uppercase tracking-wider">Profit</th>
                            <th scope="col" class="px-6 py-3 text-xs font-medium text-gray-500 uppercase tracking-wider action-column">Actions</th>
                        </tr>
                    </thead>
                    <tbody id="itemsTableBody" class="bg-white divide-y divide-gray-200">
                        </tbody>
                </table>
            </div>
            <p id="noItemsMessage" class="text-center text-gray-500 py-4">No items added yet.</p>
        </section>

        <section class="mb-8 bg-white p-6 rounded-xl shadow-lg">
            <h2 class="text-2xl font-semibold text-teal-600 mb-5 border-b pb-3">Summary</h2>
            <div class="grid grid-cols-1 sm:grid-cols-3 gap-4 text-center">
                <div class="p-4 bg-blue-50 rounded-lg">
                    <p class="text-sm font-medium text-blue-600">Total Purchase</p>
                    <p id="totalPurchase" class="text-2xl font-bold text-blue-800">$0.00</p>
                </div>
                <div class="p-4 bg-green-50 rounded-lg">
                    <p class="text-sm font-medium text-green-600">Total Sale</p>
                    <p id="totalSale" class="text-2xl font-bold text-green-800">$0.00</p>
                </div>
                <div class="p-4 bg-yellow-50 rounded-lg">
                    <p class="text-sm font-medium text-yellow-600">Total Profit</p>
                    <p id="totalProfit" class="text-2xl font-bold text-yellow-800">$0.00</p>
                </div>
            </div>
        </section>

        <section class="text-center">
            <button id="downloadPdfButton" class="px-8 py-3 bg-indigo-600 text-white font-medium text-sm leading-tight uppercase rounded-lg shadow-md hover:bg-indigo-700 hover:shadow-lg focus:bg-indigo-700 focus:shadow-lg focus:outline-none focus:ring-0 active:bg-indigo-800 active:shadow-lg transition duration-150 ease-in-out">
                Download PDF Report
            </button>
        </section>

        <footer class="mt-12 text-center text-sm text-gray-500">
            <p>&copy; <span id="currentYear"></span> Daily Tracker. All rights reserved.</p>
        </footer>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const addItemForm = document.getElementById('addItemForm');
            const itemsTableBody = document.getElementById('itemsTableBody');
            const noItemsMessage = document.getElementById('noItemsMessage');
            
            const totalPurchaseEl = document.getElementById('totalPurchase');
            const totalSaleEl = document.getElementById('totalSale');
            const totalProfitEl = document.getElementById('totalProfit');
            const downloadPdfButton = document.getElementById('downloadPdfButton');
            document.getElementById('currentYear').textContent = new Date().getFullYear();

            let items = []; // Array to store item objects

            // Standard pdfMake font definition (Roboto is default in vfs_fonts.js)
            pdfMake.fonts = {
                Roboto: {
                    normal: 'Roboto-Regular.ttf',
                    bold: 'Roboto-Medium.ttf',
                    italics: 'Roboto-Italic.ttf',
                    bolditalics: 'Roboto-MediumItalic.ttf'
                }
            };

            // Event Listener for adding an item
            addItemForm.addEventListener('submit', (e) => {
                e.preventDefault();
                const itemName = document.getElementById('itemName').value.trim();
                const quantity = parseFloat(document.getElementById('quantity').value);
                const purchasePrice = parseFloat(document.getElementById('purchasePrice').value);
                const salePrice = parseFloat(document.getElementById('salePrice').value);

                if (!itemName || isNaN(quantity) || quantity <= 0 || isNaN(purchasePrice) || purchasePrice < 0 || isNaN(salePrice) || salePrice < 0) {
                    console.error('Invalid input');
                    const tempMsg = document.createElement('p');
                    tempMsg.textContent = 'Please fill all fields correctly. Quantity and prices must be greater than zero.';
                    tempMsg.className = 'text-red-500 text-sm mb-2 text-center';
                    addItemForm.prepend(tempMsg);
                    setTimeout(() => tempMsg.remove(), 3000);
                    return;
                }

                const profit = salePrice - purchasePrice;
                const newItem = {
                    id: Date.now(), // Unique ID for the item
                    name: itemName,
                    quantity: quantity,
                    purchasePrice: purchasePrice,
                    salePrice: salePrice,
                    profit: profit
                };

                items.push(newItem);
                renderTable();
                updateTotals();
                addItemForm.reset();
                document.getElementById('itemName').focus();
            });

            // Function to render the table
            function renderTable() {
                itemsTableBody.innerHTML = ''; // Clear existing rows

                if (items.length === 0) {
                    noItemsMessage.style.display = 'block';
                    itemsTable.classList.add('hidden'); // Hide table if no items
                } else {
                    noItemsMessage.style.display = 'none';
                    itemsTable.classList.remove('hidden'); // Show table
                    items.forEach(item => {
                        const row = itemsTableBody.insertRow();
                        row.insertCell().textContent = item.name;
                        row.insertCell().textContent = item.quantity.toLocaleString('en-US');
                        row.insertCell().textContent = item.purchasePrice.toLocaleString('en-US', { style: 'currency', currency: 'USD', minimumFractionDigits: 2 });
                        row.insertCell().textContent = item.salePrice.toLocaleString('en-US', { style: 'currency', currency: 'USD', minimumFractionDigits: 2 });
                        const profitCell = row.insertCell();
                        profitCell.textContent = item.profit.toLocaleString('en-US', { style: 'currency', currency: 'USD', minimumFractionDigits: 2 });
                        profitCell.className = item.profit >= 0 ? 'text-green-600' : 'text-red-600';
                        
                        const actionsCell = row.insertCell();
                        actionsCell.classList.add('action-column');
                        const removeButton = document.createElement('button');
                        removeButton.textContent = 'Remove';
                        removeButton.classList.add('remove-btn');
                        removeButton.onclick = () => removeItem(item.id);
                        actionsCell.appendChild(removeButton);
                    });
                }
            }

            // Function to remove an item
            window.removeItem = (itemId) => { // Attach to window to be accessible from inline onclick
                items = items.filter(item => item.id !== itemId);
                renderTable();
                updateTotals();
            };

            // Function to update totals
            function updateTotals() {
                const totalPurchase = items.reduce((sum, item) => sum + item.purchasePrice, 0);
                const totalSale = items.reduce((sum, item) => sum + item.salePrice, 0);
                const totalProfit = items.reduce((sum, item) => sum + item.profit, 0);

                totalPurchaseEl.textContent = totalPurchase.toLocaleString('en-US', { style: 'currency', currency: 'USD', minimumFractionDigits: 2 });
                totalSaleEl.textContent = totalSale.toLocaleString('en-US', { style: 'currency', currency: 'USD', minimumFractionDigits: 2 });
                totalProfitEl.textContent = totalProfit.toLocaleString('en-US', { style: 'currency', currency: 'USD', minimumFractionDigits: 2 });
                totalProfitEl.className = `text-2xl font-bold ${totalProfit >= 0 ? 'text-yellow-800' : 'text-red-600'}`;
            }

            // Function to download PDF
            downloadPdfButton.addEventListener('click', () => {
                if (items.length === 0) {
                    const tempMsg = document.createElement('p');
                    tempMsg.textContent = 'No items to generate a report.';
                    tempMsg.className = 'text-red-500 text-sm my-2 text-center';
                    downloadPdfButton.insertAdjacentElement('afterend', tempMsg);
                    setTimeout(() => tempMsg.remove(), 3000);
                    return;
                }
                const reportTitle = 'Daily Buy & Sell Report';
                const tableBody = [
                    // Table Headers
                    [
                        { text: 'Item Name', style: 'tableHeader', alignment: 'left' },
                        { text: 'Quantity', style: 'tableHeader', alignment: 'left' },
                        { text: 'Total Purchase Price', style: 'tableHeader', alignment: 'left' },
                        { text: 'Total Sale Price', style: 'tableHeader', alignment: 'left' },
                        { text: 'Profit', style: 'tableHeader', alignment: 'left' }
                    ]
                ];

                items.forEach(item => {
                    tableBody.push([
                        { text: item.name, alignment: 'left' },
                        { text: item.quantity.toLocaleString('en-US'), alignment: 'left' },
                        { text: item.purchasePrice.toLocaleString('en-US', {minimumFractionDigits: 2}), alignment: 'left' },
                        { text: item.salePrice.toLocaleString('en-US', {minimumFractionDigits: 2}), alignment: 'left' },
                        { text: item.profit.toLocaleString('en-US', {minimumFractionDigits: 2}), alignment: 'left' }
                    ]);
                });
                
                const totalPurchase = items.reduce((sum, item) => sum + item.purchasePrice, 0);
                const totalSale = items.reduce((sum, item) => sum + item.salePrice, 0);
                const totalProfitVal = items.reduce((sum, item) => sum + item.profit, 0);

                const documentDefinition = {
                    pageSize: 'A4',
                    pageOrientation: 'portrait',
                    content: [
                        { text: reportTitle, style: 'header', alignment: 'center', margin: [0, 0, 0, 20] },
                        {
                            table: {
                                headerRows: 1,
                                widths: ['*', 'auto', 'auto', 'auto', 'auto'], // Columns: Name, Qty, Purchase, Sale, Profit
                                body: tableBody
                            },
                            layout: {
                                hLineWidth: function (i, node) { return (i === 0 || i === node.table.body.length) ? 2 : 1; },
                                vLineWidth: function (i, node) { return (i === 0 || i === node.table.widths.length) ? 2 : 1; },
                                hLineColor: function (i, node) { return (i === 0 || i === node.table.body.length) ? 'black' : 'gray'; },
                                vLineColor: function (i, node) { return (i === 0 || i === node.table.widths.length) ? 'black' : 'gray'; },
                                paddingLeft: function(i, node) { return 5; },
                                paddingRight: function(i, node) { return 5; },
                                paddingTop: function(i, node) { return 5; },
                                paddingBottom: function(i, node) { return 5; }
                            }
                        },
                        {
                            columns: [
                                { text: '' }, // Spacer
                                {
                                    stack: [
                                        { text: `Total Purchase: ${totalPurchase.toLocaleString('en-US', { style: 'currency', currency: 'USD' })}`, style: 'totals', alignment: 'right', margin: [0, 20, 0, 5] },
                                        { text: `Total Sale: ${totalSale.toLocaleString('en-US', { style: 'currency', currency: 'USD' })}`, style: 'totals', alignment: 'right', margin: [0, 0, 0, 5] },
                                        { text: `Total Profit: ${totalProfitVal.toLocaleString('en-US', { style: 'currency', currency: 'USD' })}`, style: 'totalsSummary', alignment: 'right', margin: [0, 0, 0, 5] }
                                    ],
                                    width: '*'
                                }
                            ]
                        }
                    ],
                    defaultStyle: {
                        font: 'Roboto',
                        alignment: 'left' // Default alignment for text in LTR
                    },
                    styles: {
                        header: {
                            fontSize: 18,
                            bold: true,
                        },
                        tableHeader: {
                            bold: true,
                            fontSize: 10,
                            color: 'black',
                            fillColor: '#eeeeee',
                            alignment: 'left' 
                        },
                        totals: {
                            fontSize: 12,
                            bold: false,
                        },
                        totalsSummary: {
                            fontSize: 14,
                            bold: true,
                        }
                    }
                };
                pdfMake.createPdf(documentDefinition).download(`daily_report_${new Date().toISOString().slice(0,10)}.pdf`);
            });

            // Initial render
            renderTable();
            updateTotals();
        });
    </script>
</body>
</html>
