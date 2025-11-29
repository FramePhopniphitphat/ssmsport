<html lang="th">
<head>
  <meta charset="UTF-8" />
  <title>โปรแกรมบันทึกสมรรถภาพทางร่างกายนักเรียน</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <style>
    * { box-sizing: border-box; font-family: "Sarabun", system-ui, sans-serif; }
    body {
      margin: 0;
      background: #fef3c7; /* เหลืองอ่อน */
      color: #333;
      padding: 20px;
    }
    h1 { margin-top: 0; }
    .container {
      max-width: 1100px;
      margin: 0 auto;
      background: #fff;
      border-radius: 16px;
      padding: 20px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.05);
    }
    .grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
      gap: 10px 16px;
      margin-bottom: 10px;
    }
    label {
      font-size: 0.9rem;
      margin-bottom: 4px;
      display: block;
    }
    input, select {
      width: 100%;
      padding: 6px 8px;
      border-radius: 8px;
      border: 1px solid #ddd;
      font-size: 0.9rem;
    }
    .btn-row {
      margin: 10px 0 15px;
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
    }
    .btn {
      border: none;
      border-radius: 999px;
      padding: 8px 16px;
      cursor: pointer;
      font-size: 0.9rem;
      color: #fff;
    }
    .btn-green { background: #22c55e; }
    .btn-red   { background: #ef4444; }
    .btn-blue  { background: #3b82f6; }
    table {
      width: 100%;
      border-collapse: collapse;
      font-size: 0.8rem;
      margin-top: 10px;
    }
    th, td {
      border: 1px solid #eee;
      padding: 4px 6px;
      text-align: center;
    }
    th {
      background: #e5e7eb;
    }
    .small {
      font-size: 0.8rem;
      color: #6b7280;
      margin-top: 4px;
    }
  </style>
</head>
<body>
<div class="container">
  <h1>โปรแกรมบันทึกสมรรถภาพทางร่างกายนักเรียน</h1>
  <p class="small">
    ระบบตัวอย่าง: บันทึกผลการทดสอบสมรรถภาพ (น้ำหนัก ส่วนสูง ลุกนั่ง ดันพื้น Sit & Reach วิ่ง 50 ม. / 800-1000 ม.)
    และคำนวณ BMI อัตโนมัติ เก็บข้อมูลใน LocalStorage
  </p>

  <!-- ฟอร์มกรอกข้อมูล -->
  <h2>ฟอร์มบันทึกผลการทดสอบ</h2>
  <div class="grid">
    <div>
      <label for="std-id">รหัสนักเรียน</label>
      <input id="std-id" type="text" />
    </div>
    <div>
      <label for="std-name">ชื่อ–นามสกุล</label>
      <input id="std-name" type="text" />
    </div>
    <div>
      <label for="std-class">ห้อง/ชั้น</label>
      <input id="std-class" type="text" placeholder="เช่น ม.2/1" />
    </div>
    <div>
      <label for="test-date">วันที่ทดสอบ</label>
      <input id="test-date" type="date" />
    </div>
  </div>

  <h3>ข้อมูลด้านรูปร่าง</h3>
  <div class="grid">
    <div>
      <label for="weight">น้ำหนัก (kg)</label>
      <input id="weight" type="number" min="0" step="0.1" />
    </div>
    <div>
      <label for="height">ส่วนสูง (cm)</label>
      <input id="height" type="number" min="0" step="0.1" />
    </div>
  </div>

  <h3>ผลการทดสอบสมรรถภาพ</h3>
  <div class="grid">
    <div>
      <label for="situp">ลุกนั่ง 1 นาที (ครั้ง)</label>
      <input id="situp" type="number" min="0" />
    </div>
    <div>
      <label for="pushup">ดันพื้น 1 นาที (ครั้ง)</label>
      <input id="pushup" type="number" min="0" />
    </div>
    <div>
      <label for="sitreach">Sit &amp; Reach (cm)</label>
      <input id="sitreach" type="number" step="0.1" />
    </div>
    <div>
      <label for="run50">วิ่ง 50 เมตร (วินาที)</label>
      <input id="run50" type="number" step="0.01" />
    </div>
    <div>
      <label for="runLong">วิ่ง 800 / 1000 เมตร (นาที:วินาที)</label>
      <input id="runLong" type="text" placeholder="เช่น 4:35" />
    </div>
  </div>

  <div class="btn-row">
    <button class="btn btn-green" id="btnSave">💾 บันทึกข้อมูล</button>
    <button class="btn btn-blue" id="btnClearForm">🧹 ล้างฟอร์ม</button>
    <button class="btn btn-red" id="btnClearAll">🗑 ลบข้อมูลทั้งหมดในระบบ</button>
  </div>

  <hr>

  <!-- ตารางแสดงข้อมูล -->
  <h2>ตารางแสดงผลการทดสอบ</h2>
  <div id="table-container"></div>

</div>

<script>
  const LS_KEY = 'fitness_records';

  function getRecords() {
    try {
      return JSON.parse(localStorage.getItem(LS_KEY) || '[]');
    } catch (e) {
      return [];
    }
  }

  function setRecords(data) {
    localStorage.setItem(LS_KEY, JSON.stringify(data));
  }

  function calcBMI(weight, heightCm) {
    const w = Number(weight);
    const h = Number(heightCm) / 100;
    if (!w || !h) return '';
    const bmi = w / (h * h);
    return bmi.toFixed(1);
  }

  function saveRecord() {
    const stdId = document.getElementById('std-id').value.trim();
    const stdName = document.getElementById('std-name').value.trim();
    const stdClass = document.getElementById('std-class').value.trim();
    const testDate = document.getElementById('test-date').value;

    const weight = document.getElementById('weight').value;
    const height = document.getElementById('height').value;
    const situp = document.getElementById('situp').value;
    const pushup = document.getElementById('pushup').value;
    const sitreach = document.getElementById('sitreach').value;
    const run50 = document.getElementById('run50').value;
    const runLong = document.getElementById('runLong').value.trim();

    if (!stdId || !stdName || !testDate) {
      alert('กรุณากรอกรหัสนักเรียน ชื่อ และวันที่ทดสอบให้ครบ');
      return;
    }

    const bmi = calcBMI(weight, height);

    const records = getRecords();
    records.push({
      id: Date.now(),
      stdId,
      stdName,
      stdClass,
      testDate,
      weight,
      height,
      bmi,
      situp,
      pushup,
      sitreach,
      run50,
      runLong
    });
    setRecords(records);
    renderTable();
    clearForm();
    alert('บันทึกข้อมูลเรียบร้อย');
  }

  function clearForm() {
    document.getElementById('std-id').value = '';
    document.getElementById('std-name').value = '';
    document.getElementById('std-class').value = '';
    document.getElementById('test-date').value = '';
    document.getElementById('weight').value = '';
    document.getElementById('height').value = '';
    document.getElementById('situp').value = '';
    document.getElementById('pushup').value = '';
    document.getElementById('sitreach').value = '';
    document.getElementById('run50').value = '';
    document.getElementById('runLong').value = '';
  }

  function clearAllData() {
    if (confirm('ต้องการลบข้อมูลสมรรถภาพนักเรียนทั้งหมดหรือไม่?')) {
      localStorage.removeItem(LS_KEY);
      renderTable();
    }
  }

  function renderTable() {
    const container = document.getElementById('table-container');
    const records = getRecords();
    if (!records.length) {
      container.innerHTML = '<p>ยังไม่มีข้อมูลสมรรถภาพทางร่างกายที่บันทึก</p>';
      return;
    }
    let html = '<table><thead><tr>';
    html += '<th>ลำดับ</th><th>รหัส</th><th>ชื่อ–นามสกุล</th><th>ห้อง</th><th>วันที่ทดสอบ</th>';
    html += '<th>นน.(kg)</th><th>สส.(cm)</th><th>BMI</th>';
    html += '<th>ลุกนั่ง</th><th>ดันพื้น</th><th>Sit & Reach</th>';
    html += '<th>50 ม. (วิ)</th><th>800/1000 ม.</th>';
    html += '</tr></thead><tbody>';

    records.forEach((r, idx) => {
      html += `<tr>
        <td>${idx + 1}</td>
        <td>${r.stdId}</td>
        <td>${r.stdName}</td>
        <td>${r.stdClass || '-'}</td>
        <td>${r.testDate}</td>
        <td>${r.weight || '-'}</td>
        <td>${r.height || '-'}</td>
        <td>${r.bmi || '-'}</td>
        <td>${r.situp || '-'}</td>
        <td>${r.pushup || '-'}</td>
        <td>${r.sitreach || '-'}</td>
        <td>${r.run50 || '-'}</td>
        <td>${r.runLong || '-'}</td>
      </tr>`;
    });

    html += '</tbody></table>';
    container.innerHTML = html;
  }

  document.getElementById('btnSave').addEventListener('click', saveRecord);
  document.getElementById('btnClearForm').addEventListener('click', clearForm);
  document.getElementById('btnClearAll').addEventListener('click', clearAllData);

  // โหลดตารางตอนเปิดหน้า
  renderTable();
</script>
</body>
</html>
