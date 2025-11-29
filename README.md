<html lang="th">
<head>
  <meta charset="UTF-8" />
  <title>ระบบยืมคืนอุปกรณ์กีฬา โรงเรียนสุรศักดิ์มนตรี</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <!-- SweetAlert2 -->
  <script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>
  <!-- Chart.js -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    * {
      box-sizing: border-box;
      font-family: "Sarabun", system-ui, sans-serif;
    }
    body {
      margin: 0;
      background: #ffedd5;
      color: #333;
    }
    .app {
      display: flex;
      min-height: 100vh;
    }
    .sidebar {
      width: 260px;
      background: linear-gradient(180deg, #ff9800, #ffb74d);
      color: #fff;
      padding: 20px 15px;
    }
    .sidebar h1 {
      font-size: 1.2rem;
      margin: 0 0 15px;
      line-height: 1.4;
    }
    .sidebar small {
      display: block;
      opacity: 0.9;
      margin-bottom: 20px;
    }
    .nav-btn {
      width: 100%;
      text-align: left;
      padding: 10px 12px;
      margin-bottom: 8px;
      border: none;
      border-radius: 8px;
      background: rgba(255,255,255,0.15);
      color: #fff;
      cursor: pointer;
      font-size: 0.95rem;
      display: flex;
      align-items: center;
      gap: 8px;
      transition: background 0.2s, transform 0.1s;
    }
    .nav-btn span.icon { font-size: 1.1rem; }
    .nav-btn.active,
    .nav-btn:hover {
      background: rgba(255,255,255,0.3);
      transform: translateY(-1px);
    }
    .main-content {
      flex: 1;
      padding: 20px;
    }
    header h2 { margin: 0 0 5px; }
    header p {
      margin: 0 0 16px;
      color: #555;
      font-size: 0.9rem;
    }
    .page {
      display: none;
      background: #fff8f0;
      border-radius: 16px;
      padding: 20px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.05);
    }
    .page.active { display: block; }
    .card-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
      gap: 15px;
      margin-bottom: 20px;
    }
    .card {
      background: #ffffff;
      border-radius: 12px;
      padding: 15px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.05);
    }
    .card h3 { margin: 0 0 8px; font-size: 1rem; }
    .card p { margin: 0; font-size: 0.9rem; color: #555; }
    .form-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
      gap: 15px;
      margin-bottom: 10px;
    }
    label {
      display: block;
      font-size: 0.9rem;
      margin-bottom: 4px;
      color: #444;
    }
    input[type="text"],
    input[type="number"],
    input[type="date"],
    select,
    textarea {
      width: 100%;
      padding: 8px 10px;
      border-radius: 8px;
      border: 1px solid #ddd;
      font-size: 0.9rem;
    }
    textarea {
      resize: vertical;
      min-height: 60px;
    }
    .btn-row {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      margin: 10px 0 15px;
    }
    .btn {
      border: none;
      border-radius: 999px;
      padding: 8px 16px;
      font-size: 0.9rem;
      cursor: pointer;
      display: inline-flex;
      align-items: center;
      gap: 6px;
      transition: transform 0.1s, box-shadow 0.1s;
      color: #fff;
    }
    .btn:active {
      transform: translateY(1px);
      box-shadow: none;
    }
    .btn-green {
      background: #4caf50;
      box-shadow: 0 2px 4px rgba(76,175,80,0.5);
    }
    .btn-blue {
      background: #2196f3;
      box-shadow: 0 2px 4px rgba(33,150,243,0.5);
    }
    .btn-yellow {
      background: #ffb300;
      box-shadow: 0 2px 4px rgba(255,179,0,0.5);
      color: #333;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 10px;
      font-size: 0.85rem;
    }
    th, td {
      border: 1px solid #eee;
      padding: 6px 8px;
      text-align: left;
    }
    th { background: #ffe0b2; }
    ul.announcement {
      list-style: disc;
      padding-left: 22px;
      margin: 4px 0 0;
      font-size: 0.88rem;
    }
    .section-title {
      margin-top: 0;
      margin-bottom: 12px;
      font-size: 1.05rem;
    }
    .chart-container {
      max-width: 480px;
      margin-top: 15px;
    }
    .muted {
      font-size: 0.8rem;
      color: #777;
    }
    @media (max-width: 768px) {
      .app { flex-direction: column; }
      .sidebar { width: 100%; }
    }
  </style>
</head>
<body>
<div class="app">
  <aside class="sidebar">
    <h1>ระบบยืมคืนอุปกรณ์กีฬา<br>โรงเรียนสุรศักดิ์มนตรี</h1>
    <small>Sports Equipment Borrowing System – Surasakmontree School</small>

    <button class="nav-btn active" data-page="page-dashboard">
      <span class="icon">🏠</span> หน้าแรก (Dashboard)
    </button>
    <button class="nav-btn" data-page="page-equipment">
      <span class="icon">🏀</span> เพิ่ม/จัดการอุปกรณ์
    </button>
    <button class="nav-btn" data-page="page-borrow">
      <span class="icon">🤝</span> ยืม–คืนอุปกรณ์
    </button>
    <button class="nav-btn" data-page="page-member">
      <span class="icon">👤</span> เพิ่มผู้ยืม (สมาชิก)
    </button>
    <button class="nav-btn" data-page="page-report">
      <span class="icon">📊</span> รายงานการยืมใช้
    </button>
  </aside>

  <main class="main-content">
    <header>
      <h2 id="page-title">หน้าแรก (Dashboard)</h2>
      <p id="page-subtitle">ภาพรวมการยืม–คืนอุปกรณ์กีฬา โรงเรียนสุรศักดิ์มนตรี</p>
    </header>

    <!-- Dashboard -->
    <section id="page-dashboard" class="page active">
      <div class="card-grid">
        <div class="card">
          <h3>ภาพรวมอุปกรณ์</h3>
          <p>จำนวนอุปกรณ์ทั้งหมด: <strong id="dash-total-equipment">0</strong></p>
          <p>จำนวนอุปกรณ์ที่มีการยืม: <strong id="dash-total-borrowed">0</strong></p>
        </div>
        <div class="card">
          <h3>การยืมวันนี้</h3>
          <p>จำนวนครั้งที่ยืมวันนี้: <strong id="dash-today-borrow">0</strong></p>
          <p>จำนวนสมาชิกทั้งหมด: <strong id="dash-total-members">0</strong></p>
        </div>
      </div>

      <div class="card">
        <h3>ข่าว / ประกาศ</h3>
        <ul class="announcement">
          <li>โปรดตรวจเช็กสภาพอุปกรณ์ก่อนและหลังการใช้งานทุกครั้ง</li>
          <li>อุปกรณ์ที่ยืมต้องคืนภายในวันเดียวกัน เว้นแต่ได้รับอนุญาตเป็นพิเศษจากครูพละ</li>
        </ul>
      </div>
    </section>

    <!-- หน้าอื่น ๆ ใช้โค้ดเดียวกับเวอร์ชันก่อน (Equipment, Borrow, Member, Report) -->
    <!-- คุณสามารถคัดลอกส่วนที่เหลือจากโค้ดเวอร์ชันก่อนแล้ววางต่อจากบรรทัดนี้ได้เลย -->
  </main>
</div>
</body>
</html>
