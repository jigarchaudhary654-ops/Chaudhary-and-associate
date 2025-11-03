# Chaudhary-and-associate

<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Invoice - Hirava Production</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 20px;
      background: #f2f2f2;
    }
    .invoice {
      width: 800px;
      margin: auto;
      background: white;
      padding: 20px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }
    h2, h4 {
      text-align: center;
      margin: 0;
    }
    h4 {
      margin-bottom: 10px;
      text-transform: uppercase;
      letter-spacing: 1px;
    }
    .company-address {
      text-align: center;
      font-size: 14px;
      margin-top: 5px;
      color: #333;
      line-height: 1.4;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 10px;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 8px;
      text-align: left;
    }
    th {
      background: #e0e0e0;
    }
    .header {
      display: flex;
      justify-content: space-between;
      margin-bottom: 10px;
    }
    input, select {
      border: none;
      outline: none;
      width: 100%;
    }
    .buttons {
      text-align: right;
      margin-bottom: 15px;
    }
    button {
      padding: 8px 14px;
      margin-left: 10px;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    .print-btn { background-color: #4CAF50; color: white; }
    .pdf-btn { background-color: #2196F3; color: white; }
    .add-btn { background-color: #FF9800; color: white; }
    .remove-btn { background-color: #f44336; color: white; }
    .clear-btn { background-color: #9C27B0; color: white; }

    /* Totals + Footer Layout */
    .bottom-section {
      display: flex;
      justify-content: space-between;
      margin-top: 40px;
    }
    .bank-details {
      width: 45%;
    }
    .totals {
      width: 45%;
    }
    .totals table {
      width: 100%;
      border-collapse: collapse;
    }
    .totals td {
      border: none;
      padding: 6px;
      text-align: right;
    }
    .totals tr td:first-child {
      text-align: left;
    }
    .totals tr:last-child td {
      font-weight: bold;
      border-top: 1px solid #ccc;
    }
    .amount-words {
      font-style: italic;
      text-align: right;
      margin-top: 8px;
    }
    .signature {
      text-align: right;
      margin-top: 40px;
      font-style: italic;
    }
    .signature img {
      height: 60px;
      display: block;
      margin-left: auto;
    }
  </style>
</head>
<body>
  <div class="invoice" id="invoiceArea">
    <div class="buttons">
      <button class="add-btn" onclick="addRow()">Add Item</button>
      <button class="remove-btn" onclick="removeRow()">Remove Item</button>
      <button class="print-btn" onclick="printInvoice()">Print Invoice</button>
      <button class="pdf-btn" onclick="downloadPDF()">Download PDF</button>
      <button class="clear-btn" onclick="clearInvoice()">Clear Data</button>
    </div>

    <h4>TAX INVOICE</h4>
    <h2>HIRAVA PRODUCTION</h2>
    <div class="company-address">
      Address: Sant Kabir Main Road, Rajkot, Gujarat<br>
      Phone: +91 98765 43210 &nbsp; | &nbsp; Email: hiravaproduction@gmail.com
    </div>

    <div class="header">
      <div>
        <p><strong>Name:</strong> <input type="text" id="name"></p>
        <p><strong>Address:</strong> <input type="text" id="address"></p>
        <p><strong>State:</strong> <input type="text" id="state"></p>
        <p><strong>GSTIN:</strong> <input type="text" id="gstin"></p>
      </div>
      <div>
        <p><strong>Invoice No:</strong> <input type="text" id="invoiceNo"></p>
        <p><strong>Date:</strong> <input type="date" id="date"></p>
        <p><strong>Transport:</strong> <input type="text" id="transport"></p>
      </div>
    </div>

    <table id="invoiceTable">
      <thead>
        <tr>
          <th>No.</th>
          <th>Particulars</th>
          <th>HSN Code</th>
          <th>Unit</th>
          <th>Qty</th>
          <th>Rate</th>
          <th>Amount</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>

    <!-- Bottom section with Bank details and Totals -->
    <div class="bottom-section">
      <div class="bank-details">
        <p><strong>Bank Details:</strong><br>
        A/c Name: HIRAVA PRODUCTION<br>
        A/c No: 138305501295<br>
        IFSC Code: ICIC0001383</p>
      </div>

      <div class="totals">
        <table>
          <tr><td>Sub Total</td><td id="subtotal">0.00</td></tr>
          <tr>
            <td>Packing Charge</td>
            <td><input type="number" id="packingCharge" value="0" oninput="calculateTotals()" style="text-align:right; width:80px;"></td>
          </tr>
          <tr><td>CGST 1.5%</td><td id="cgst">0.00</td></tr>
          <tr><td>SGST 1.5%</td><td id="sgst">0.00</td></tr>
          <tr><td>IGST 3%</td><td id="igst">0.00</td></tr>
          <tr><td>Round Off</td><td id="roundOff">0.00</td></tr>
          <tr><td>Total Amount</td><td id="total">0.00</td></tr>
        </table>

        <div class="amount-words" id="amountWords">Amount in words: Zero Rupees Only</div>

        <div class="signature">
          <img src="https://upload.wikimedia.org/wikipedia/commons/5/59/Signature_example.svg" alt="Signature">
          <p><strong>Authorised Signatory</strong><br>Hirava Production</p>
        </div>
      </div>
    </div>
  </div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
  <script>
    // --- Existing JavaScript (unchanged) ---
    function generateInvoiceNo() {
      const today = new Date();
      const unique = today.getFullYear().toString().slice(2) +
        (today.getMonth()+1).toString().padStart(2, '0') +
        today.getDate().toString().padStart(2, '0') +
        Math.floor(Math.random() * 1000);
      document.getElementById('invoiceNo').value = unique;
    }

    function autoPopulateGSTIN() {
      const gstinField = document.getElementById('gstin');
      if (!gstinField.value) gstinField.value = '24AAACB5343E1Z7';
    }

    function numberToWords(num) {
      const a = ['', 'One', 'Two', 'Three', 'Four', 'Five', 'Six', 'Seven', 'Eight', 'Nine', 'Ten',
                 'Eleven', 'Twelve', 'Thirteen', 'Fourteen', 'Fifteen', 'Sixteen', 'Seventeen', 'Eighteen', 'Nineteen'];
      const b = ['', '', 'Twenty', 'Thirty', 'Forty', 'Fifty', 'Sixty', 'Seventy', 'Eighty', 'Ninety'];
      function inWords(n) {
        if ((n = n.toString()).length > 9) return 'Overflow';
        const nStr = ('000000000' + n).substr(-9).match(/^(\d{2})(\d{2})(\d{2})(\d{1})(\d{2})$/);
        if (!nStr) return '';
        let str = '';
        str += (nStr[1] != 0) ? (a[Number(nStr[1])] || b[nStr[1][0]] + ' ' + a[nStr[1][1]]) + ' Crore ' : '';
        str += (nStr[2] != 0) ? (a[Number(nStr[2])] || b[nStr[2][0]] + ' ' + a[nStr[2][1]]) + ' Lakh ' : '';
        str += (nStr[3] != 0) ? (a[Number(nStr[3])] || b[nStr[3][0]] + ' ' + a[nStr[3][1]]) + ' Thousand ' : '';
        str += (nStr[4] != 0) ? (a[Number(nStr[4])] || b[nStr[4][0]] + ' ' + a[nStr[4][1]]) + ' Hundred ' : '';
        str += (nStr[5] != 0) ? ((str != '') ? 'and ' : '') +
          (a[Number(nStr[5])] || b[nStr[5][0]] + ' ' + a[nStr[5][1]]) + ' ' : '';
        return str.trim() + 'Rupees Only';
      }
      return inWords(Math.floor(num));
    }

    function calculateTotals() {
      let subtotal = 0;
      document.querySelectorAll('#invoiceTable tbody tr').forEach(row => {
        const qty = parseFloat(row.cells[4].querySelector('input').value) || 0;
        const rate = parseFloat(row.cells[5].querySelector('input').value) || 0;
        const amount = qty * rate;
        row.querySelector('.amount').textContent = amount.toFixed(2);
        subtotal += amount;
      });
      const packing = parseFloat(document.getElementById('packingCharge').value) || 0;
      const totalBeforeTax = subtotal + packing;
      const cgst = totalBeforeTax * 0.015;
      const sgst = totalBeforeTax * 0.015;
      const igst = totalBeforeTax * 0.03;
      let total = totalBeforeTax + cgst + sgst + igst;
      const rounded = Math.round(total);
      const roundOff = (rounded - total).toFixed(2);
      total = rounded;
      document.getElementById('subtotal').textContent = subtotal.toFixed(2);
      document.getElementById('cgst').textContent = cgst.toFixed(2);
      document.getElementById('sgst').textContent = sgst.toFixed(2);
      document.getElementById('igst').textContent = igst.toFixed(2);
      document.getElementById('roundOff').textContent = roundOff;
      document.getElementById('total').textContent = total.toFixed(2);
      document.getElementById('amountWords').textContent = 'Amount in words: ' + numberToWords(total);
      autoSaveInvoice();
    }

    function addRow(item = {}) {
      const table = document.querySelector('#invoiceTable tbody');
      const rowCount = table.rows.length;
      const newRow = document.createElement('tr');
      newRow.innerHTML = `
        <td>${rowCount + 1}</td>
        <td><input type="text" value="${item.name || ''}" placeholder="Item Name"></td>
        <td><input type="text" value="${item.hsn || ''}" placeholder="HSN Code"></td>
        <td><input type="text" value="${item.unit || ''}" placeholder="Unit"></td>
        <td><input type="number" value="${item.qty || 0}" placeholder="Qty"></td>
        <td><input type="number" value="${item.rate || 0}" placeholder="Rate"></td>
        <td class="amount">0.00</td>
      `;
      table.appendChild(newRow);
      newRow.querySelectorAll('input').forEach(input => input.addEventListener('input', calculateTotals));
      calculateTotals();
    }

    function removeRow() {
      const table = document.querySelector('#invoiceTable tbody');
      if (table.rows.length > 1) {
        table.deleteRow(-1);
        calculateTotals();
      }
    }

    function printInvoice() { window.print(); }

    function downloadPDF() {
      const element = document.getElementById('invoiceArea');
      const opt = {
        margin: 0.3,
        filename: 'Invoice_' + document.getElementById('invoiceNo').value + '.pdf',
        image: { type: 'jpeg', quality: 0.98 },
        html2canvas: { scale: 2 },
        jsPDF: { unit: 'in', format: 'a4', orientation: 'portrait' }
      };
      html2pdf().set(opt).from(element).save();
    }

    function autoSaveInvoice() {
      const data = {
        name: document.getElementById('name').value,
        address: document.getElementById('address').value,
        state: document.getElementById('state').value,
        gstin: document.getElementById('gstin').value,
        invoiceNo: document.getElementById('invoiceNo').value,
        date: document.getElementById('date').value,
        transport: document.getElementById('transport').value,
        packingCharge: document.getElementById('packingCharge').value,
        items: Array.from(document.querySelectorAll('#invoiceTable tbody tr')).map(row => ({
          name: row.cells[1].querySelector('input').value,
          hsn: row.cells[2].querySelector('input').value,
          unit: row.cells[3].querySelector('input').value,
          qty: row.cells[4].querySelector('input').value,
          rate: row.cells[5].querySelector('input').value
        }))
      };
      localStorage.setItem('savedInvoice', JSON.stringify(data));
    }

    function loadInvoice() {
      const saved = localStorage.getItem('savedInvoice');
      autoPopulateGSTIN();
      if (!saved) {
        addRow();
        generateInvoiceNo();
        calculateTotals();
        return;
      }
      const data = JSON.parse(saved);
      document.getElementById('name').value = data.name;
      document.getElementById('address').value = data.address;
      document.getElementById('state').value = data.state;
      document.getElementById('gstin').value = data.gstin;
      document.getElementById('invoiceNo').value = data.invoiceNo;
      document.getElementById('date').value = data.date;
      document.getElementById('transport').value = data.transport;
      document.getElementById('packingCharge').value = data.packingCharge || 0;
      data.items.forEach(item => addRow(item));
      calculateTotals();
    }

    function clearInvoice() {
      localStorage.removeItem('savedInvoice');
      document.querySelector('#invoiceTable tbody').innerHTML = '';
      addRow();
      document.getElementById('name').value = '';
      document.getElementById('address').value = '';
      document.getElementById('state').value = '';
      document.getElementById('gstin').value = '';
      document.getElementById('transport').value = '';
      document.getElementById('packingCharge').value = 0;
      generateInvoiceNo();
      autoPopulateGSTIN();
      calculateTotals();
    }

    window.addEventListener('load', loadInvoice);
  </script>
</body>
</html>
