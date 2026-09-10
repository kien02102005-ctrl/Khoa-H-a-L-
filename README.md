<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WORKSPACE - Khoa Hóa Lý (Realtime KPI Master Sync)</title>
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- FontAwesome Icon -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <!-- Firebase SDK (Modular v9/v10 Compat) -->
    <script src="https://www.gstatic.com/firebasejs/10.8.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.8.0/firebase-database-compat.js"></script>

    <style>
        :root {
            --primary: #1e40af;
            --primary-hover: #1d4ed8;
            --sidebar-bg: #0f172a;
            --bg-light: #f1f5f9;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--bg-light);
            height: 100vh;
            overflow: hidden;
        }

        /* --- 1. GIAO DIỆN ĐĂNG NHẬP --- */
        #login-screen {
            position: fixed;
            top: 0; left: 0; width: 100vw; height: 100vh;
            background: linear-gradient(135deg, #1e3a8a, #3b82f6);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 9999;
        }

        .login-card {
            background: #fff;
            padding: 40px 30px;
            border-radius: 12px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.2);
            width: 380px;
            text-align: center;
        }

        .login-card .icon {
            font-size: 48px;
            color: #2563eb;
            margin-bottom: 15px;
        }

        .login-card h2 {
            color: #1e293b;
            margin-bottom: 5px;
            text-transform: uppercase;
        }

        .login-card p {
            color: #64748b;
            font-size: 13px;
            margin-bottom: 25px;
        }

        .form-group {
            text-align: left;
            margin-bottom: 18px;
        }

        .form-group label {
            display: block;
            font-size: 13px;
            font-weight: 600;
            color: #334155;
            margin-bottom: 5px;
        }

        .form-group input, .form-group select {
            width: 100%;
            padding: 10px 12px;
            border: 1px solid #cbd5e1;
            border-radius: 6px;
            outline: none;
            font-size: 14px;
        }

        .form-group input:focus, .form-group select:focus {
            border-color: #2563eb;
        }

        .btn-login {
            width: 100%;
            padding: 12px;
            background-color: #2563eb;
            color: #fff;
            border: none;
            border-radius: 6px;
            font-weight: bold;
            font-size: 15px;
            cursor: pointer;
            transition: 0.2s;
        }

        .btn-login:hover {
            background-color: #1d4ed8;
        }

        /* --- 2. GIAO DIỆN CHÍNH (APP MAIN) --- */
        #app-screen {
            display: none;
            height: 100vh;
        }

        /* Sidebar */
        .sidebar {
            width: 240px;
            background-color: var(--sidebar-bg);
            color: #fff;
            display: flex;
            flex-direction: column;
            flex-shrink: 0;
        }

        .sidebar-brand {
            padding: 20px;
            font-size: 20px;
            font-weight: bold;
            display: flex;
            align-items: center;
            gap: 10px;
            border-bottom: 1px solid #334155;
        }

        .user-profile {
            padding: 15px 20px;
            border-bottom: 1px solid #334155;
        }

        .user-profile .name {
            font-weight: 600;
            font-size: 15px;
        }

        .user-profile .badge {
            display: inline-block;
            background-color: #ef4444;
            color: #fff;
            font-size: 10px;
            padding: 2px 8px;
            border-radius: 10px;
            margin-top: 4px;
        }

        .nav-list {
            list-style: none;
            padding: 15px 0;
        }

        .nav-item {
            padding: 12px 20px;
            display: flex;
            align-items: center;
            gap: 12px;
            cursor: pointer;
            color: #94a3b8;
            transition: 0.2s;
            font-size: 14px;
        }

        .nav-item:hover, .nav-item.active {
            background-color: #2563eb;
            color: #fff;
        }

        /* Main Content */
        .main-content {
            flex: 1;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        .top-bar {
            background-color: #fff;
            padding: 15px 25px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 1px solid #e2e8f0;
        }

        .top-bar h2 {
            font-size: 18px;
            color: #1e293b;
        }

        .top-actions {
            display: flex;
            gap: 10px;
            align-items: center;
        }

        .admin-select-box {
            background: #f8fafc;
            border: 1px solid #cbd5e1;
            padding: 6px 12px;
            border-radius: 6px;
            font-size: 13px;
            display: none;
            align-items: center;
            gap: 8px;
        }

        .admin-select-box select {
            padding: 4px 8px;
            border-radius: 4px;
            border: 1px solid #cbd5e1;
            outline: none;
            font-weight: bold;
            color: #1e40af;
        }

        .btn-action {
            padding: 8px 14px;
            border-radius: 6px;
            border: 1px solid #cbd5e1;
            background: #fff;
            cursor: pointer;
            font-size: 13px;
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .btn-action.btn-danger {
            background-color: #ef4444;
            color: white;
            border: none;
        }

        .tab-content {
            flex: 1;
            padding: 20px;
            overflow-y: auto;
            display: none;
        }

        .tab-content.active {
            display: block;
        }

        /* --- STYLES CHO CÁC TAB --- */
        .dashboard-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
            gap: 20px;
            padding: 10px;
        }

        .card {
            background: #ffffff;
            border-radius: 12px;
            padding: 24px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
            border: 1px solid #f1f5f9;
            margin-bottom: 20px;
        }

        .card h3 {
            font-size: 18px;
            font-weight: 700;
            color: #334155;
            margin-bottom: 20px;
        }

        .chart-container {
            position: relative;
            height: 300px;
            width: 100%;
        }

        /* Form Nhập Task trong Tab Danh Sách */
        .task-input-bar {
            background: #fff;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 1px 3px rgba(0,0,0,0.1);
            margin-bottom: 20px;
            display: flex;
            gap: 12px;
            flex-wrap: wrap;
            align-items: center;
        }

        .task-input-bar input[type="text"],
        .task-input-bar select,
        .task-input-bar input[type="date"] {
            padding: 8px 12px;
            border: 1px solid #cbd5e1;
            border-radius: 6px;
            outline: none;
            font-size: 14px;
        }

        .task-input-bar input[type="text"] { flex: 2; min-width: 200px; }
        .task-input-bar select { flex: 1; min-width: 120px; }
        .task-input-bar input[type="date"] { flex: 1; min-width: 140px; cursor: pointer; }

        /* Tab Danh sách */
        .table-container {
            background: #fff;
            border-radius: 8px;
            overflow: hidden;
            box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        }

        table {
            width: 100%;
            border-collapse: collapse;
            text-align: left;
            font-size: 14px;
        }

        th, td {
            padding: 12px 16px;
            border-bottom: 1px solid #e2e8f0;
        }

        th {
            background-color: #f8fafc;
            color: #475569;
        }

        .inline-date-picker {
            border: 1px solid #cbd5e1;
            border-radius: 4px;
            padding: 4px 6px;
            font-size: 13px;
            outline: none;
            cursor: pointer;
            background: #fff;
        }

        tr.row-overdue {
            background-color: #fef2f2 !important;
        }

        tr.row-overdue td {
            color: #991b1b;
        }

        .badge-overdue {
            background-color: #ef4444;
            color: #ffffff;
            font-size: 11px;
            padding: 3px 8px;
            border-radius: 4px;
            font-weight: bold;
            display: inline-flex;
            align-items: center;
            gap: 4px;
            margin-left: 6px;
        }

        .status-tag {
            padding: 4px 8px;
            border-radius: 4px;
            font-size: 12px;
            font-weight: 500;
        }

        .status-doing { background: #dbeafe; color: #1d4ed8; }
        .status-todo { background: #f3e8ff; color: #6b21a8; }
        .status-done { background: #dcfce7; color: #15803d; }

        /* Tab Kanban */
        .kanban-board {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 20px;
            height: 100%;
        }

        .kanban-col {
            background: #e2e8f0;
            border-radius: 8px;
            padding: 15px;
            min-height: 400px;
        }

        .kanban-col-header {
            font-weight: bold;
            margin-bottom: 15px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .kanban-card {
            background: #fff;
            padding: 15px;
            border-radius: 6px;
            margin-bottom: 10px;
            box-shadow: 0 1px 2px rgba(0,0,0,0.1);
            cursor: grab;
            border-left: 4px solid transparent;
        }

        .kanban-card.card-overdue {
            background-color: #fef2f2;
            border-left: 4px solid #ef4444;
        }

        .kanban-card:active {
            cursor: grabbing;
        }

        .kanban-card .id { color: #2563eb; font-size: 12px; font-weight: bold; }
        .kanban-card .title { font-size: 14px; font-weight: 600; margin: 5px 0 10px; }
        .kanban-card .meta { font-size: 12px; color: #64748b; display: flex; justify-content: space-between; align-items: center; }

        /* Tab KPI Styling */
        .kpi-section-title {
            background: #1e40af;
            color: #ffffff;
            padding: 10px 16px;
            font-size: 15px;
            font-weight: bold;
            border-radius: 6px 6px 0 0;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .row-sub-header {
            background-color: #f1f5f9;
            font-weight: bold;
            color: #1e293b;
        }

        .kpi-table input[type="number"] {
            width: 70px;
            padding: 4px 6px;
            border: 1px solid #cbd5e1;
            border-radius: 4px;
            text-align: center;
            font-weight: 600;
        }

        .btn-sm {
            padding: 4px 8px;
            font-size: 11px;
            border-radius: 4px;
            border: none;
            cursor: pointer;
            margin-right: 2px;
        }
        .btn-edit { background-color: #f59e0b; color: white; }
        .btn-delete { background-color: #ef4444; color: white; }

        .kpi-target-bar {
            background: #e0f2fe;
            border: 1px solid #bae6fd;
            padding: 12px 20px;
            border-radius: 8px;
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 15px;
            flex-wrap: wrap;
        }

        .kpi-target-bar select {
            padding: 6px 12px;
            border-radius: 6px;
            border: 1px solid #0284c7;
            font-weight: bold;
            color: #0369a1;
            outline: none;
        }

        .kpi-badge-type {
            display: inline-block;
            padding: 4px 10px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: bold;
            margin-left: 10px;
        }
        .type-leader { background-color: #fef3c7; color: #b45309; border: 1px solid #fde68a; }
        .type-staff { background-color: #e0e7ff; color: #3730a3; border: 1px solid #c7d2fe; }

        .kpi-total-box {
            display: flex;
            justify-content: flex-end;
            gap: 30px;
            margin-top: 20px;
            font-size: 16px;
            font-weight: bold;
            background: #f8fafc;
            padding: 15px 20px;
            border-radius: 8px;
            border: 1px solid #e2e8f0;
        }

        .sync-status {
            font-size: 12px;
            padding: 4px 8px;
            border-radius: 4px;
            background: #dcfce7;
            color: #15803d;
            display: flex;
            align-items: center;
            gap: 5px;
        }
    </style>
</head>
<body>

    <!-- 1. MÀN HÌNH ĐĂNG NHẬP -->
    <div id="login-screen">
        <div class="login-card">
            <div class="icon"><i class="fa-solid fa-flask"></i></div>
            <h2>KHOA HÓA LÝ</h2>
            <p id="form-sub-title">Đăng nhập hệ thống quản trị công việc (Realtime)</p>
            
            <div style="display: flex; gap: 10px; margin-bottom: 20px; border-bottom: 2px solid #e2e8f0; padding-bottom: 10px;">
                <button type="button" id="tab-login-btn" onclick="toggleAuthTab('login')" style="flex: 1; padding: 8px; border: none; background: none; font-weight: bold; color: #2563eb; border-bottom: 2px solid #2563eb; cursor: pointer;">ĐĂNG NHẬP</button>
                <button type="button" id="tab-register-btn" onclick="toggleAuthTab('register')" style="flex: 1; padding: 8px; border: none; background: none; font-weight: bold; color: #64748b; cursor: pointer;">ĐĂNG KÝ</button>
            </div>

            <form id="login-form" onsubmit="handleLogin(event)">
                <div class="form-group">
                    <label>Tên đăng nhập</label>
                    <input type="text" id="login-username" placeholder="Nhập tên đăng nhập..." required>
                </div>
                <div class="form-group">
                    <label>Mật khẩu</label>
                    <input type="password" id="login-password" placeholder="Nhập mật khẩu..." required>
                </div>
                <button type="submit" class="btn-login">ĐĂNG NHẬP</button>
            </form>

            <form id="register-form" onsubmit="handleRegister(event)" style="display: none;">
                <div class="form-group">
                    <label>Gmail</label>
                    <input type="email" id="reg-email" placeholder="example@gmail.com" required>
                </div>
                <div class="form-group">
                    <label>Tên đăng nhập</label>
                    <input type="text" id="reg-username" placeholder="Tạo tên đăng nhập..." required>
                </div>
                <div class="form-group">
                    <label>Mật khẩu</label>
                    <input type="password" id="reg-password" placeholder="Nhập mật khẩu..." required>
                </div>
                <div class="form-group">
                    <label>Xác nhận mật khẩu</label>
                    <input type="password" id="reg-confirm-password" placeholder="Nhập lại mật khẩu..." required>
                </div>
                <div class="form-group">
                    <label>Loại Bảng KPI áp dụng</label>
                    <select id="reg-kpi-type">
                        <option value="staff">Bảng KPI Dành cho Cán bộ / Nhân viên</option>
                        <option value="leader">Bảng KPI Dành cho Lãnh đạo</option>
                    </select>
                </div>
                <button type="submit" class="btn-login" style="background-color: #10b981;">ĐĂNG KÝ TÀI KHOẢN</button>
            </form>
        </div>
    </div>

    <!-- 2. MÀN HÌNH CHÍNH WEB APP -->
    <div id="app-screen">
        <!-- Sidebar -->
        <div class="sidebar">
            <div class="sidebar-brand">
                <i class="fa-solid fa-shapes"></i> WORKSPACE
            </div>
            <div class="user-profile">
                <div class="name" id="user-display-name">Cán bộ</div>
                <span class="badge" id="user-role-badge">User</span>
            </div>
            <ul class="nav-list">
                <li class="nav-item active" onclick="switchTab('tong-quan', this)">
                    <i class="fa-solid fa-chart-pie"></i> Tổng quan
                </li>
                <li class="nav-item" onclick="switchTab('danh-sach', this)">
                    <i class="fa-solid fa-list-check"></i> Danh sách
                </li>
                <li class="nav-item" onclick="switchTab('kanban', this)">
                    <i class="fa-solid fa-table-columns"></i> Kanban
                </li>
                <li class="nav-item" onclick="switchTab('gantt', this)">
                    <i class="fa-solid fa-bars-progress"></i> Sơ đồ Gantt
                </li>
                <li class="nav-item" onclick="switchTab('kpi', this)">
                    <i class="fa-solid fa-award"></i> Đánh giá KPI
                </li>
                <li class="nav-item" id="nav-admin-users" style="display: none;" onclick="switchTab('admin-users', this)">
                    <i class="fa-solid fa-users-gear"></i> Quản lý Users
                </li>
            </ul>
        </div>

        <!-- Main Content -->
        <div class="main-content">
            <!-- Top Bar -->
            <div class="top-bar">
                <h2 id="page-title">Dashboard Thống Kê</h2>
                <div class="top-actions">
                    <div class="sync-status"><i class="fa-solid fa-arrows-rotate fa-spin"></i> Đồng bộ Realtime</div>
                    <div class="admin-select-box" id="admin-user-selector">
                        <span><i class="fa-solid fa-user-pen"></i> Xem data công việc của:</span>
                        <select id="select-target-user" onchange="changeTargetUser(this.value)"></select>
                    </div>
                    <button class="btn-action btn-danger" onclick="logout()"><i class="fa-solid fa-power-off"></i> Đăng xuất</button>
                </div>
            </div>

            <!-- Tab 1: Tổng quan -->
            <div id="tab-tong-quan" class="tab-content active">
                <div class="dashboard-grid">
                    <div class="card">
                        <h3>Tỷ lệ Trạng thái</h3>
                        <div class="chart-container">
                            <canvas id="statusChart"></canvas>
                        </div>
                    </div>
                    <div class="card">
                        <h3>Mức độ Ưu tiên</h3>
                        <div class="chart-container">
                            <canvas id="priorityChart"></canvas>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Tab 2: Danh sách -->
            <div id="tab-danh-sach" class="tab-content">
                <h3 style="font-size: 16px; color: #334155; margin-bottom: 12px;">Quản lý Công Việc Độc Lập</h3>
                
                <div class="task-input-bar">
                    <input type="text" id="newTaskName" placeholder="Nhập tên công việc mới..." />
                    <select id="newTaskPriority">
                        <option value="Bình thường">Bình thường</option>
                        <option value="Cao">Cao</option>
                        <option value="Thấp">Thấp</option>
                    </select>
                    <input type="date" id="newTaskDueDate" />
                    <button class="btn-login" style="width: auto; padding: 8px 18px;" onclick="addNewTask()"><i class="fa-solid fa-plus"></i> Thêm công việc</button>
                </div>

                <div class="table-container">
                    <table>
                        <thead>
                            <tr>
                                <th>Mã</th>
                                <th>Tên công việc</th>
                                <th>Người nhận</th>
                                <th>Mức ưu tiên</th>
                                <th>Trạng thái</th>
                                <th>Hạn chót</th>
                                <th>Thao tác</th>
                            </tr>
                        </thead>
                        <tbody id="task-table-body"></tbody>
                    </table>
                </div>
            </div>

            <!-- Tab 3: Kanban -->
            <div id="tab-kanban" class="tab-content">
                <div class="kanban-board">
                    <div class="kanban-col" id="col-todo" ondragover="allowDrop(event)" ondrop="drop(event, 'Chưa làm')">
                        <div class="kanban-col-header">🌙 Chưa làm <span id="count-todo">0</span></div>
                        <div class="kanban-cards" id="cards-todo"></div>
                    </div>
                    <div class="kanban-col" id="col-doing" ondragover="allowDrop(event)" ondrop="drop(event, 'Đang làm')">
                        <div class="kanban-col-header">⌛ Đang làm <span id="count-doing">0</span></div>
                        <div class="kanban-cards" id="cards-doing"></div>
                    </div>
                    <div class="kanban-col" id="col-done" ondragover="allowDrop(event)" ondrop="drop(event, 'Hoàn thành')">
                        <div class="kanban-col-header">✔️ Hoàn thành <span id="count-done">0</span></div>
                        <div class="kanban-cards" id="cards-done"></div>
                    </div>
                </div>
            </div>

            <!-- Tab 4: Gantt -->
            <div id="tab-gantt" class="tab-content">
                <div class="card">
                    <h3>Lộ trình triển khai</h3>
                    <p style="color: #64748b; font-size: 14px;">(Sơ đồ tiến độ công việc cá nhân)</p>
                </div>
            </div>

            <!-- Tab 5: Đánh giá KPI -->
            <div id="tab-kpi" class="tab-content">
                <div class="kpi-target-bar" id="kpi-admin-target-bar" style="display: none;">
                    <i class="fa-solid fa-user-check" style="font-size: 20px; color: #0284c7;"></i>
                    <span style="font-weight: 600; color: #0369a1;">Chọn tài khoản để chấm KPI:</span>
                    <select id="select-kpi-target-user" onchange="changeKPITargetUser(this.value)"></select>

                    <div style="margin-left: 20px; display: flex; align-items: center; gap: 8px;">
                        <span style="font-weight: 600; color: #0369a1;"><i class="fa-solid fa-arrow-right-arrow-left"></i> Chuyển Bảng KPI:</span>
                        <select id="select-kpi-type-change" onchange="adminChangeUserKPIType(this.value)">
                            <option value="staff">Bảng KPI Dành cho Cán bộ / Nhân viên</option>
                            <option value="leader">Bảng KPI Dành cho Lãnh đạo</option>
                        </select>
                    </div>
                </div>

                <div class="card">
                    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
                        <h3 style="font-size: 18px; margin: 0; display: flex; align-items: center;">
                            PHIẾU ĐÁNH GIÁ KPI CỦA: <span id="kpi-target-name-display" style="color: #2563eb; text-transform: uppercase; margin-left: 8px;"></span>
                            <span id="kpi-type-badge-display" class="kpi-badge-type"></span>
                        </h3>
                    </div>

                    <!-- BẢNG A -->
                    <div style="margin-bottom: 25px;">
                        <div class="kpi-section-title">
                            <span>
                                A. QUẢN LÝ & CHUYÊN MÔN (ĐIỂM TỐI ĐA: <span id="max-score-A-display" style="color: #fde047;">50</span> ĐIỂM)
                                <button class="btn-sm btn-edit btn-add-kpi-class" style="margin-left: 10px;" onclick="editSectionMaxScore('A')"><i class="fa-solid fa-pen"></i> Sửa Điểm</button>
                            </span>
                            <button class="btn-sm btn-login btn-add-kpi-class" style="width: auto; background-color: #10b981;" onclick="addSubSection('A')">
                                <i class="fa-solid fa-plus"></i> Thêm Mục La Mã (I, II...)
                            </button>
                        </div>
                        <div class="table-container" style="border-radius: 0 0 8px 8px;">
                            <table class="kpi-table">
                                <thead>
                                    <tr>
                                        <th style="width: 50px; text-align: center;">STT</th>
                                        <th>Nội dung Đánh giá</th>
                                        <th style="width: 250px;">Tiêu chí đánh giá</th>
                                        <th style="width: 100px; text-align: center;">Điểm tối đa</th>
                                        <th style="width: 100px; text-align: center;">Điểm tự chấm</th>
                                        <th style="width: 120px; text-align: center; background-color: #f0fdf4; color: #166534;">Điểm Đánh Giá</th>
                                        <th style="width: 160px; text-align: center;" class="kpi-admin-col">Thao tác Admin</th>
                                    </tr>
                                </thead>
                                <tbody id="kpi-tbody-A"></tbody>
                            </table>
                        </div>
                    </div>

                    <!-- BẢNG B -->
                    <div style="margin-bottom: 25px;">
                        <div class="kpi-section-title" style="background-color: #0d9488;">
                            <span>
                                B. Ý THỨC CHẤP HÀNH & KỶ LUẬT (ĐIỂM TỐI ĐA: <span id="max-score-B-display" style="color: #fde047;">30</span> ĐIỂM)
                                <button class="btn-sm btn-edit btn-add-kpi-class" style="margin-left: 10px;" onclick="editSectionMaxScore('B')"><i class="fa-solid fa-pen"></i> Sửa Điểm</button>
                            </span>
                            <button class="btn-sm btn-login btn-add-kpi-class" style="width: auto; background-color: #10b981;" onclick="addSubSection('B')">
                                <i class="fa-solid fa-plus"></i> Thêm Mục La Mã (I, II...)
                            </button>
                        </div>
                        <div class="table-container" style="border-radius: 0 0 8px 8px;">
                            <table class="kpi-table">
                                <thead>
                                    <tr>
                                        <th style="width: 50px; text-align: center;">STT</th>
                                        <th>Nội dung Đánh giá</th>
                                        <th style="width: 250px;">Tiêu chí đánh giá</th>
                                        <th style="width: 100px; text-align: center;">Điểm tối đa</th>
                                        <th style="width: 100px; text-align: center;">Điểm tự chấm</th>
                                        <th style="width: 120px; text-align: center; background-color: #f0fdf4; color: #166534;">Điểm Đánh Giá</th>
                                        <th style="width: 160px; text-align: center;" class="kpi-admin-col">Thao tác Admin</th>
                                    </tr>
                                </thead>
                                <tbody id="kpi-tbody-B"></tbody>
                            </table>
                        </div>
                    </div>

                    <!-- BẢNG C -->
                    <div style="margin-bottom: 25px;">
                        <div class="kpi-section-title" style="background-color: #7c3aed;">
                            <span>
                                C. NĂNG LỰC ĐỔI MỚI & NHIỆM VỤ ĐỘT XUẤT (ĐIỂM TỐI ĐA: <span id="max-score-C-display" style="color: #fde047;">20</span> ĐIỂM)
                                <button class="btn-sm btn-edit btn-add-kpi-class" style="margin-left: 10px;" onclick="editSectionMaxScore('C')"><i class="fa-solid fa-pen"></i> Sửa Điểm</button>
                            </span>
                            <button class="btn-sm btn-login btn-add-kpi-class" style="width: auto; background-color: #10b981;" onclick="addSubSection('C')">
                                <i class="fa-solid fa-plus"></i> Thêm Mục La Mã (I, II...)
                            </button>
                        </div>
                        <div class="table-container" style="border-radius: 0 0 8px 8px;">
                            <table class="kpi-table">
                                <thead>
                                    <tr>
                                        <th style="width: 50px; text-align: center;">STT</th>
                                        <th>Nội dung Đánh giá</th>
                                        <th style="width: 250px;">Tiêu chí đánh giá</th>
                                        <th style="width: 100px; text-align: center;">Điểm tối đa</th>
                                        <th style="width: 100px; text-align: center;">Điểm tự chấm</th>
                                        <th style="width: 120px; text-align: center; background-color: #f0fdf4; color: #166534;">Điểm Đánh Giá</th>
                                        <th style="width: 160px; text-align: center;" class="kpi-admin-col">Thao tác Admin</th>
                                    </tr>
                                </thead>
                                <tbody id="kpi-tbody-C"></tbody>
                            </table>
                        </div>
                    </div>
                    
                    <!-- Bảng Tổng Điểm -->
                    <div class="kpi-total-box">
                        <div>
                            TỔNG ĐIỂM TỰ CHẤM: 
                            <span id="kpi-total-self" style="color: #2563eb;">0</span> / 
                            <span id="kpi-total-max" style="color: #64748b;">0</span>
                        </div>
                        <div style="border-left: 2px solid #cbd5e1; padding-left: 20px;">
                            TỔNG ĐIỂM ĐÁNH GIÁ: 
                            <span id="kpi-total-admin" style="color: #059669;">0</span>
                        </div>
                    </div>
                    
                    <div style="margin-top: 15px; text-align: right; font-size: 13px; color: #475569;">
                        <i class="fa-regular fa-clock" style="color: #0284c7;"></i> Lần cuối lưu điểm: <span id="kpi-last-time-saved" style="font-weight: bold; color: #1e293b;">Chưa có dữ liệu</span>
                    </div>

                    <div style="text-align: center; margin-top: 20px;">
                        <button id="btn-save-kpi-admin" class="btn-login" style="width: auto; padding: 10px 30px; background-color: #2563eb; display: none;" onclick="saveKPIRatingByAdmin()">
                            <i class="fa-solid fa-floppy-disk"></i> LƯU KẾT QUẢ ĐÁNH GIÁ (ADMIN)
                        </button>
                        <button id="btn-save-kpi-user" class="btn-login" style="width: auto; padding: 10px 30px; background-color: #10b981; display: none;" onclick="saveKPIRatingByUser()">
                            <i class="fa-solid fa-user-check"></i> LƯU KẾT QUẢ TỰ CHẤM
                        </button>
                    </div>
                </div>
            </div>

            <!-- Tab 6: Quản lý Users -->
            <div id="tab-admin-users" class="tab-content">
                <div class="card">
                    <h3>Danh Sách Tài Khoản Trong Hệ Thống (Online Sync)</h3>
                    <p style="color: #64748b; font-size: 13px; margin-bottom: 15px;">* Bạn có thể phân loại bảng đánh giá KPI cho từng tài khoản (Bao gồm cả Admin) tại cột "Loại Bảng KPI".</p>
                    <div class="table-container">
                        <table>
                            <thead>
                                <tr>
                                    <th>STT</th>
                                    <th>Tên đăng nhập</th>
                                    <th>Email</th>
                                    <th>Loại Bảng KPI</th>
                                    <th>Thao tác</th>
                                </tr>
                            </thead>
                            <tbody id="user-management-body"></tbody>
                        </table>
                    </div>
                </div>
            </div>

        </div>
    </div>

    <!-- JAVASCRIPT XỬ LÝ DỮ LIỆU CƠ SỞ DỮ LIỆU REALTIME -->
    <script>
        // CẤU HÌNH FIREBASE
        const firebaseConfig = {
            apiKey: "AiZaSyDvMPWqdqgTJ5SzMrhhV56EOpv95yow4VA",
            authDomain: "kien02102005-381b4.firebaseapp.com",
            projectId: "kien02102005-381b4",
            storageBucket: "kien02102005-381b4.firebasestorage.app",
            messagingSenderId: "554013353586",
            appId: "1:554013353586:web:8a64d8ba969104053404eb",
            measurementId: "G-TJE25HB0PQ",
            databaseURL: "https://kien02102005-381b4-default-rtdb.firebaseio.com"
        };

        firebase.initializeApp(firebaseConfig);
        const db = firebase.database();

        const ADMIN_USERNAME = 'linhnguyenxuan';
        const ADMIN_PASSWORD = '051214';

        let currentUser = '';          
        let targetUser = '';           
        let kpiTargetUser = '';        
        let isAdmin = false;
        let lastKpiTimestamp = 'Chưa có dữ liệu';

        let registeredUsers = [];
        let tasks = [];
        let kpiDataList = [];
        let sectionMaxScores = { A: 50, B: 30, C: 20 };

        // Cache Template KPI cấp Master
        let masterKPITemplates = {
            staff: { sectionMaxScores: { A: 50, B: 30, C: 20 }, kpiDataList: [] },
            leader: { sectionMaxScores: { A: 50, B: 30, C: 20 }, kpiDataList: [] }
        };

        let statusChartInstance = null;
        let priorityChartInstance = null;

        function convertToDisplayDate(isoDateStr) {
            if (!isoDateStr) return '';
            if (isoDateStr.includes('/')) return isoDateStr;
            const parts = isoDateStr.split('-');
            if (parts.length === 3) return `${parts[2]}/${parts[1]}/${parts[0]}`;
            return isoDateStr;
        }

        function convertToISODate(displayDateStr) {
            if (!displayDateStr) return '';
            if (displayDateStr.includes('-')) return displayDateStr;
            const parts = displayDateStr.split('/');
            if (parts.length === 3) return `${parts[2]}-${parts[1].padStart(2, '0')}-${parts[0].padStart(2, '0')}`;
            return displayDateStr;
        }

        // --- CẤU TRÚC MẪU BAN ĐẦU CHO 2 BẢNG KPI ---
        const defaultStaffKPIStructure = [
            {
                id: 'sub_a1', section: 'A', code: 'I', title: 'Nhiệm vụ chuyên môn và giảng dạy (Cán bộ / Nhân viên)', maxScore: 30,
                items: [
                    { id: 'item_a1_1', title: 'Thực hiện đúng tiến độ bài giảng / nhiệm vụ giao', criteria: 'Hoàn thành 100% công việc đúng giờ', maxScore: 15, selfScore: 15, adminScore: 15 },
                    { id: 'item_a1_2', title: 'Thực hiện nghiên cứu khoa học cơ bản', criteria: 'Có báo cáo hoặc đóng góp chuyên môn', maxScore: 15, selfScore: 13, adminScore: 14 }
                ]
            },
            {
                id: 'sub_a2', section: 'A', code: 'II', title: 'Quản lý thiết bị & Phòng thí nghiệm', maxScore: 20,
                items: [
                    { id: 'item_a2_1', title: 'Bảo quản tốt hóa chất, thiết bị PTN', criteria: 'Không để hỏng hóc, thất thoát', maxScore: 20, selfScore: 18, adminScore: 19 }
                ]
            },
            {
                id: 'sub_b1', section: 'B', code: 'I', title: 'Ý thức chấp hành quy chế & Kỷ luật', maxScore: 30,
                items: [
                    { id: 'item_b1_1', title: 'Chấp hành giờ giấc và nội quy đơn vị', criteria: 'Đi làm đúng giờ, nghỉ có phép', maxScore: 30, selfScore: 30, adminScore: 30 }
                ]
            },
            {
                id: 'sub_c1', section: 'C', code: 'I', title: 'Đổi mới sáng tạo & Nhiệm vụ đột xuất', maxScore: 20,
                items: [
                    { id: 'item_c1_1', title: 'Hoàn thành nhiệm vụ đột xuất', criteria: 'Đạt yêu cầu của Lãnh đạo giao', maxScore: 20, selfScore: 18, adminScore: 19 }
                ]
            }
        ];

        const defaultLeaderKPIStructure = [
            {
                id: 'sub_a1', section: 'A', code: 'I', title: 'Năng lực Lãnh đạo & Quản lý điều hành (Lãnh đạo)', maxScore: 30,
                items: [
                    { id: 'item_a1_1', title: 'Xây dựng kế hoạch và chỉ đạo thực hiện nhiệm vụ khoa', criteria: '100% chỉ tiêu năm đạt tiến độ', maxScore: 15, selfScore: 15, adminScore: 15 },
                    { id: 'item_a1_2', title: 'Quản lý nhân sự và phát triển đội ngũ', criteria: 'Không có cán bộ vi phạm kỷ luật', maxScore: 15, selfScore: 14, adminScore: 15 }
                ]
            },
            {
                id: 'sub_a2', section: 'A', code: 'II', title: 'Chỉ đạo Nghiên cứu Khoa học & Hợp tác', maxScore: 20,
                items: [
                    { id: 'item_a2_1', title: 'Chỉ đạo các đề tài KH công nghệ cấp Bộ/Nhà nước', criteria: 'Đạt nghiệm thu đúng hạn', maxScore: 20, selfScore: 19, adminScore: 19 }
                ]
            },
            {
                id: 'sub_b1', section: 'B', code: 'I', title: 'Gương mẫu chấp hành & Nêu gương Lãnh đạo', maxScore: 30,
                items: [
                    { id: 'item_b1_1', title: 'Nêu gương thực hiện quy chế làm việc', criteria: 'Đạt chuẩn mực quản lý', maxScore: 30, selfScore: 30, adminScore: 30 }
                ]
            },
            {
                id: 'sub_c1', section: 'C', code: 'I', title: 'Strategical Innovation & Quyết định chiến lược', maxScore: 20,
                items: [
                    { id: 'item_c1_1', title: 'Sáng kiến phát triển thương hiệu / Khoa Hóa Lý', criteria: 'Đưa vào áp dụng hiệu quả', maxScore: 20, selfScore: 20, adminScore: 20 }
                ]
            }
        ];

        const defaultTasks = [
            { id: 'T001', name: 'Nghiên cứu tài liệu khoa học', status: 'Đang làm', date: '02/09/2026', priority: 'Cao' },
            { id: 'T002', name: 'Chuẩn bị hóa chất phòng thí nghiệm', status: 'Chưa làm', date: '30/09/2026', priority: 'Bình thường' },
            { id: 'T003', name: 'Viết báo cáo tổng kết tháng', status: 'Hoàn thành', date: '01/09/2026', priority: 'Thấp' }
        ];

        window.addEventListener('DOMContentLoaded', () => {
            const dateInput = document.getElementById('newTaskDueDate');
            if (dateInput) {
                const today = new Date().toISOString().split('T')[0];
                dateInput.value = today;
            }
        });

        // LẮNG NGHE MASTER TEMPLATES KPI
        function listenRealtimeTemplates() {
            db.ref('kpiTemplates').on('value', snapshot => {
                const data = snapshot.val();
                if (data) {
                    masterKPITemplates = data;
                } else {
                    // Khởi tạo Template mặc định trên Firebase nếu chưa có
                    masterKPITemplates = {
                        staff: { sectionMaxScores: { A: 50, B: 30, C: 20 }, kpiDataList: defaultStaffKPIStructure },
                        leader: { sectionMaxScores: { A: 50, B: 30, C: 20 }, kpiDataList: defaultLeaderKPIStructure }
                    };
                    db.ref('kpiTemplates').set(masterKPITemplates);
                }
                
                // Cập nhật lại UI nếu đang mở tab KPI
                if (kpiTargetUser) {
                    loadAndMergeUserKPI(kpiTargetUser);
                }
            });
        }

        function listenRealtimeUsers() {
            db.ref('users').on('value', snapshot => {
                const data = snapshot.val();
                let loadedUsers = data ? Object.values(data) : [];
                
                // Đảm bảo Admin luôn có trong danh sách tài khoản
                const hasAdmin = loadedUsers.some(u => u.username === ADMIN_USERNAME);
                if (!hasAdmin) {
                    const adminObj = { email: 'admin@hoaly.edu.vn', username: ADMIN_USERNAME, password: ADMIN_PASSWORD, kpiType: 'leader' };
                    db.ref(`users/${ADMIN_USERNAME}`).set(adminObj);
                    loadedUsers.push(adminObj);
                }

                registeredUsers = loadedUsers;
                if (isAdmin) {
                    populateAdminUserSelector();
                    populateKPITargetSelector();
                    renderUserManagementTable();
                }
            });
        }

        function listenRealtimeTasks() {
            if (!targetUser) return;
            db.ref(`tasks/${targetUser}`).on('value', snapshot => {
                const data = snapshot.val();
                if (data) {
                    tasks = Object.values(data);
                } else {
                    tasks = defaultTasks.map(t => ({ ...t, user: targetUser }));
                    saveUserData();
                }
                initApp();
            });
        }

        function listenRealtimeKPI() {
            if (!kpiTargetUser) return;
            
            // Cập nhật trạng thái select Bảng KPI trên giao diện
            const targetUserInfo = registeredUsers.find(u => u.username === kpiTargetUser);
            if (targetUserInfo) {
                document.getElementById('select-kpi-type-change').value = targetUserInfo.kpiType || 'staff';
            }
            
            loadAndMergeUserKPI(kpiTargetUser);
        }

        // TẢI VÀ HỢP NHẤT DỮ LIỆU KPI USER VỚI MASTER TEMPLATE
        function loadAndMergeUserKPI(username) {
            const targetUserInfo = registeredUsers.find(u => u.username === username);
            const userKpiType = (targetUserInfo && targetUserInfo.kpiType) ? targetUserInfo.kpiType : 'staff';
            const currentTemplate = masterKPITemplates[userKpiType] || masterKPITemplates['staff'];

            db.ref(`kpi/${username}`).on('value', snapshot => {
                const data = snapshot.val();
                sectionMaxScores = currentTemplate.sectionMaxScores || { A: 50, B: 30, C: 20 };
                
                if (data && data.kpiDataList) {
                    lastKpiTimestamp = data.timestamp || 'Chưa ghi nhận';
                    // Ghép cấu trúc Master với Điểm đã chấm cá nhân
                    kpiDataList = mergeKPIWithTemplate(currentTemplate.kpiDataList, data.kpiDataList);
                } else {
                    lastKpiTimestamp = 'Chưa ghi nhận';
                    kpiDataList = JSON.parse(JSON.stringify(currentTemplate.kpiDataList));
                }
                renderKPITable();
            });
        }

        // Hàm giúp giữ lại Điểm đã chấm khi cấu trúc Bảng Master thay đổi
        function mergeKPIWithTemplate(templateList, userList) {
            const templateCopy = JSON.parse(JSON.stringify(templateList || []));
            const userItemMap = {};

            if (userList) {
                userList.forEach(sub => {
                    if (sub.items) {
                        sub.items.forEach(item => {
                            userItemMap[item.id] = {
                                selfScore: item.selfScore,
                                adminScore: item.adminScore
                            };
                        });
                    }
                });
            }

            templateCopy.forEach(sub => {
                if (sub.items) {
                    sub.items.forEach(item => {
                        if (userItemMap[item.id]) {
                            item.selfScore = userItemMap[item.id].selfScore !== undefined ? userItemMap[item.id].selfScore : item.maxScore;
                            item.adminScore = userItemMap[item.id].adminScore !== undefined ? userItemMap[item.id].adminScore : item.maxScore;
                        }
                    });
                }
            });

            return templateCopy;
        }

        function saveUserData() {
            if (!targetUser) return;
            db.ref(`tasks/${targetUser}`).set(tasks);
        }

        // HÀM LƯU KPI CỦA TÀI KHOẢN HIỆN TẠI
        function saveKPIRatingStorage(timestamp = null) {
            if (!kpiTargetUser) return;
            let timeSaved = timestamp || lastKpiTimestamp;
            db.ref(`kpi/${kpiTargetUser}`).set({
                timestamp: timeSaved,
                kpiDataList: kpiDataList
            });
        }

        // HÀM LƯU MASTER TEMPLATE KHI ADMIN SỬA BẢNG (KÍCH HOẠT ĐỒNG BỘ HÀNG LOẠT)
        function saveMasterTemplateAndSync(type, updatedStructure, updatedMaxScores) {
            if (!isAdmin) return;
            
            masterKPITemplates[type].kpiDataList = updatedStructure;
            if (updatedMaxScores) {
                masterKPITemplates[type].sectionMaxScores = updatedMaxScores;
            }

            // Đồng bộ Master Template lên Firebase -> Tất cả User nhóm này sẽ nhận cấu trúc mới ngay lập tức
            db.ref(`kpiTemplates/${type}`).set(masterKPITemplates[type], (err) => {
                if (!err) {
                    // Cập nhật lại UI hiện tại
                    sectionMaxScores = masterKPITemplates[type].sectionMaxScores;
                    kpiDataList = masterKPITemplates[type].kpiDataList;
                    renderKPITable();
                }
            });
        }

        function isTaskOverdue(dateStr, status) {
            if (status === 'Hoàn thành' || !dateStr) return false;
            let day, month, year;
            if (dateStr.includes('-')) {
                [year, month, day] = dateStr.split('-');
            } else if (dateStr.includes('/')) {
                [day, month, year] = dateStr.split('/');
            } else {
                return false;
            }
            const dueDate = new Date(parseInt(year), parseInt(month) - 1, parseInt(day), 23, 59, 59);
            return dueDate < new Date();
        }

        function toggleAuthTab(tab) {
            const loginForm = document.getElementById('login-form');
            const registerForm = document.getElementById('register-form');
            const loginBtn = document.getElementById('tab-login-btn');
            const regBtn = document.getElementById('tab-register-btn');
            const subTitle = document.getElementById('form-sub-title');

            if (tab === 'login') {
                loginForm.style.display = 'block'; registerForm.style.display = 'none';
                loginBtn.style.color = '#2563eb'; loginBtn.style.borderBottom = '2px solid #2563eb';
                regBtn.style.color = '#64748b'; regBtn.style.borderBottom = 'none';
                subTitle.innerText = 'Đăng nhập hệ thống quản trị công việc (Realtime)';
            } else {
                loginForm.style.display = 'none'; registerForm.style.display = 'block';
                regBtn.style.color = '#10b981'; regBtn.style.borderBottom = '2px solid #10b981';
                loginBtn.style.color = '#64748b'; loginBtn.style.borderBottom = 'none';
                subTitle.innerText = 'Tạo tài khoản mới cho cán bộ';
            }
        }

        function handleRegister(e) {
            e.preventDefault();
            const email = document.getElementById('reg-email').value.trim();
            const username = document.getElementById('reg-username').value.trim().toLowerCase();
            const password = document.getElementById('reg-password').value;
            const confirmPassword = document.getElementById('reg-confirm-password').value;
            const kpiType = document.getElementById('reg-kpi-type').value;

            if (username === ADMIN_USERNAME) { alert('Tên đăng nhập trùng với Admin!'); return; }
            if (password !== confirmPassword) { alert('Mật khẩu không khớp!'); return; }
            if (registeredUsers.some(u => u.username === username)) { alert('Tên đăng nhập đã tồn tại!'); return; }

            const newUser = { email, username, password, kpiType: kpiType };
            db.ref(`users/${username}`).set(newUser, (err) => {
                if (!err) {
                    alert('Đăng ký tài khoản thành công!');
                    document.getElementById('register-form').reset();
                    toggleAuthTab('login');
                    document.getElementById('login-username').value = username;
                } else {
                    alert('Lỗi đăng ký: ' + err.message);
                }
            });
        }

        function handleLogin(e) {
            e.preventDefault();
            const usernameInput = document.getElementById('login-username').value.trim().toLowerCase();
            const passwordInput = document.getElementById('login-password').value;

            const validUser = registeredUsers.find(u => u.username === usernameInput && u.password === passwordInput);

            if (usernameInput === ADMIN_USERNAME && passwordInput === ADMIN_PASSWORD) {
                currentUser = ADMIN_USERNAME; targetUser = ADMIN_USERNAME; kpiTargetUser = ADMIN_USERNAME; isAdmin = true;
            } else if (validUser) {
                currentUser = usernameInput; targetUser = usernameInput; kpiTargetUser = usernameInput; isAdmin = false;
            } else {
                alert('Tên đăng nhập hoặc mật khẩu không chính xác!'); return;
            }

            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('app-screen').style.display = 'flex';
            document.getElementById('user-display-name').innerText = currentUser;
            const badge = document.getElementById('user-role-badge');

            if (isAdmin) {
                badge.innerText = 'ADMIN'; badge.style.backgroundColor = '#10b981';
                document.getElementById('nav-admin-users').style.display = 'flex';
                document.getElementById('admin-user-selector').style.display = 'flex';
                document.getElementById('kpi-admin-target-bar').style.display = 'flex';
                document.querySelectorAll('.btn-add-kpi-class').forEach(btn => btn.style.display = 'inline-block');
                document.getElementById('btn-save-kpi-admin').style.display = 'inline-block';
                document.getElementById('btn-save-kpi-user').style.display = 'none';
            } else {
                badge.innerText = 'User'; badge.style.backgroundColor = '#2563eb';
                document.getElementById('nav-admin-users').style.display = 'none';
                document.getElementById('admin-user-selector').style.display = 'none';
                document.getElementById('kpi-admin-target-bar').style.display = 'none';
                document.querySelectorAll('.btn-add-kpi-class').forEach(btn => btn.style.display = 'none');
                document.getElementById('btn-save-kpi-admin').style.display = 'none';
                document.getElementById('btn-save-kpi-user').style.display = 'inline-block';
            }

            listenRealtimeTemplates();
            listenRealtimeTasks();
            listenRealtimeKPI();
        }

        function populateAdminUserSelector() {
            const select = document.getElementById('select-target-user');
            select.innerHTML = '';
            registeredUsers.forEach(u => {
                select.innerHTML += `<option value="${u.username}">${u.username} ${u.username === ADMIN_USERNAME ? '(Admin)' : ''}</option>`;
            });
            select.value = targetUser;
        }

        function populateKPITargetSelector() {
            const select = document.getElementById('select-kpi-target-user');
            select.innerHTML = '';
            registeredUsers.forEach(u => {
                select.innerHTML += `<option value="${u.username}">${u.username} ${u.username === ADMIN_USERNAME ? '(Admin)' : ''}</option>`;
            });
            select.value = kpiTargetUser;
        }

        function changeTargetUser(newTarget) {
            targetUser = newTarget;
            listenRealtimeTasks();
        }

        function changeKPITargetUser(newTarget) {
            kpiTargetUser = newTarget;
            listenRealtimeKPI();
        }

        // Admin đổi Bảng KPI cho bất kỳ User nào (kể cả chính Admin)
        function adminChangeUserKPIType(newType) {
            if (!isAdmin || !kpiTargetUser) return;

            if (confirm(`Bạn có chắc chắn muốn chuyển đổi BẢNG KPI của tài khoản ${kpiTargetUser} sang [${newType === 'leader' ? 'Bảng Lãnh đạo' : 'Bảng Cán bộ'}]?`)) {
                db.ref(`users/${kpiTargetUser}/kpiType`).set(newType, (err) => {
                    if (!err) {
                        db.ref(`kpi/${kpiTargetUser}`).remove(); // Reset dữ liệu chấm cá nhân cũ để áp dụng cấu trúc mới
                        alert("Đã chuyển đổi Bảng KPI và đồng bộ cấu trúc mới thành công!");
                    }
                });
            } else {
                // Nếu Hủy, trả lại giá trị ban đầu cho select
                const targetUserInfo = registeredUsers.find(u => u.username === kpiTargetUser);
                if (targetUserInfo) {
                    document.getElementById('select-kpi-type-change').value = targetUserInfo.kpiType || 'staff';
                }
            }
        }

        function logout() {
            location.reload();
        }

        function initApp() {
            renderTable(); renderKanban(); initCharts();
            if (isAdmin) renderUserManagementTable();
        }

        function switchTab(tabId, element) {
            document.querySelectorAll('.nav-item').forEach(el => el.classList.remove('active'));
            document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
            element.classList.add('active');
            document.getElementById(`tab-${tabId}`).classList.add('active');
        }

        function renderTable() {
            const tbody = document.getElementById('task-table-body');
            tbody.innerHTML = '';
            tasks.forEach((task, index) => {
                let statusClass = task.status === 'Chưa làm' ? 'status-todo' : task.status === 'Hoàn thành' ? 'status-done' : 'status-doing';
                const overdue = isTaskOverdue(task.date, task.status);
                const isoDate = convertToISODate(task.date);

                tbody.innerHTML += `
                    <tr class="${overdue ? 'row-overdue' : ''}">
                        <td style="color: #2563eb; font-weight: bold;">${task.id}</td>
                        <td><b>${task.name}</b> ${overdue ? `<span class="badge-overdue"><i class="fa-solid fa-triangle-exclamation"></i> QUÁ HẠN</span>` : ''}</td>
                        <td>${task.user || targetUser}</td>
                        <td><span style="font-size: 12px; font-weight: bold; color: ${task.priority === 'Cao' ? '#ef4444' : task.priority === 'Bình thường' ? '#f59e0b' : '#10b981'}">${task.priority}</span></td>
                        <td><span class="status-tag ${statusClass}">${task.status}</span></td>
                        <td>
                            <input type="date" class="inline-date-picker" value="${isoDate}" onchange="updateTaskDueDate(${index}, this.value)">
                        </td>
                        <td>
                            <button class="btn-sm btn-edit" onclick="editTask(${index})"><i class="fa-solid fa-pen"></i> Sửa</button>
                            <button class="btn-sm btn-delete" onclick="deleteTask(${index})"><i class="fa-solid fa-trash"></i> Xóa</button>
                        </td>
                    </tr>`;
            });
        }

        function updateTaskDueDate(index, newIsoDate) {
            if (!newIsoDate) return;
            tasks[index].date = convertToDisplayDate(newIsoDate);
            saveUserData();
        }

        function addNewTask() {
            const taskInput = document.getElementById('newTaskName');
            const priorityInput = document.getElementById('newTaskPriority');
            const dueDateInput = document.getElementById('newTaskDueDate');

            const taskName = taskInput.value.trim();
            if (!taskName) {
                alert("Vui lòng nhập tên công việc!");
                return;
            }

            const priority = priorityInput.value;
            const dateStr = convertToDisplayDate(dueDateInput.value);

            tasks.push({ 
                id: 'T' + String(tasks.length + 1).padStart(3, '0'), 
                name: taskName, 
                user: targetUser, 
                status: 'Chưa làm', 
                date: dateStr, 
                priority: priority 
            });

            saveUserData();
            taskInput.value = '';
        }

        function editTask(index) {
            const task = tasks[index];
            const newName = prompt("Sửa tên công việc:", task.name); if (newName === null) return;
            const newPriority = prompt("Cập nhật mức ưu tiên (Cao / Bình thường / Thấp):", task.priority);
            const newStatus = prompt("Cập nhật trạng thái (Chưa làm / Đang làm / Hoàn thành):", task.status);
            
            if (newName.trim() !== '') task.name = newName.trim();
            if (['Cao', 'Bình thường', 'Thấp'].includes(newPriority)) task.priority = newPriority;
            if (['Chưa làm', 'Đang làm', 'Hoàn thành'].includes(newStatus)) task.status = newStatus;
            
            saveUserData();
        }

        function deleteTask(index) {
            if (confirm("Xóa công việc này?")) { tasks.splice(index, 1); saveUserData(); }
        }

        function renderKanban() {
            const todoBox = document.getElementById('cards-todo'), doingBox = document.getElementById('cards-doing'), doneBox = document.getElementById('cards-done');
            todoBox.innerHTML = ''; doingBox.innerHTML = ''; doneBox.innerHTML = '';
            let countTodo = 0, countDoing = 0, countDone = 0;

            tasks.forEach(task => {
                const overdue = isTaskOverdue(task.date, task.status);
                const displayDate = convertToDisplayDate(task.date);
                const cardHTML = `
                    <div class="kanban-card ${overdue ? 'card-overdue' : ''}" draggable="true" ondragstart="drag(event, '${task.id}')">
                        <div style="display: flex; justify-content: space-between;"><span class="id">${task.id}</span>${overdue ? `<span style="color: #dc2626; font-size: 11px; font-weight: bold;">Quá hạn</span>` : ''}</div>
                        <div class="title">${task.name}</div>
                        <div class="meta"><span>${task.user || targetUser}</span><span>${displayDate}</span></div>
                    </div>`;
                if (task.status === 'Chưa làm') { todoBox.innerHTML += cardHTML; countTodo++; }
                else if (task.status === 'Đang làm') { doingBox.innerHTML += cardHTML; countDoing++; }
                else if (task.status === 'Hoàn thành') { doneBox.innerHTML += cardHTML; countDone++; }
            });
            document.getElementById('count-todo').innerText = countTodo;
            document.getElementById('count-doing').innerText = countDoing;
            document.getElementById('count-done').innerText = countDone;
        }

        function allowDrop(ev) { ev.preventDefault(); }
        function drag(ev, id) { ev.dataTransfer.setData("text", id); }
        function drop(ev, newStatus) {
            ev.preventDefault();
            const id = ev.dataTransfer.getData("text");
            const task = tasks.find(t => t.id === id);
            if (task) { task.status = newStatus; saveUserData(); }
        }

        /* --- XỬ LÝ ĐIỂM VÀ CẤU TRÚC BẢNG KPI (REALTIME MASTER SYNC) --- */
        function getCurrentTargetKPIType() {
            const targetUserInfo = registeredUsers.find(u => u.username === kpiTargetUser);
            return (targetUserInfo && targetUserInfo.kpiType) ? targetUserInfo.kpiType : 'staff';
        }

        function editSectionMaxScore(section) {
            if (!isAdmin) return;
            const kpiType = getCurrentTargetKPIType();
            const currentVal = sectionMaxScores[section] || 0;
            const input = prompt(`[Admin] Nhập ĐIỂM TỐI ĐA mới cho Mục lớn ${section} (Áp dụng đồng loạt cho Bảng ${kpiType === 'leader' ? 'Lãnh đạo' : 'Cán bộ'}):`, currentVal);
            if (input === null) return;
            const newVal = Number(input);
            if (isNaN(newVal) || newVal <= 0) { alert("Điểm phải là số dương!"); return; }

            const currentSubSum = kpiDataList
                .filter(sub => sub.section === section)
                .reduce((sum, sub) => sum + sub.maxScore, 0);

            if (currentSubSum > newVal) {
                alert(`Không thể đặt điểm tối đa Mục lớn ${section} là ${newVal}!\nTổng điểm các Mục La Mã hiện tại là ${currentSubSum}.`);
                return;
            }
            
            const updatedMaxScores = { ...sectionMaxScores, [section]: newVal };
            saveMasterTemplateAndSync(kpiType, kpiDataList, updatedMaxScores);
        }

        function renderKPITable() {
            document.getElementById('kpi-target-name-display').innerText = kpiTargetUser;
            document.getElementById('kpi-last-time-saved').innerText = lastKpiTimestamp;
            document.getElementById('max-score-A-display').innerText = sectionMaxScores.A;
            document.getElementById('max-score-B-display').innerText = sectionMaxScores.B;
            document.getElementById('max-score-C-display').innerText = sectionMaxScores.C;

            // Hiển thị Badge Nhãn loại KPI
            const userKpiType = getCurrentTargetKPIType();
            const badgeEl = document.getElementById('kpi-type-badge-display');
            if (userKpiType === 'leader') {
                badgeEl.innerText = "Bảng KPI: Lãnh Đạo";
                badgeEl.className = "kpi-badge-type type-leader";
            } else {
                badgeEl.innerText = "Bảng KPI: Cán Bộ / Nhân Viên";
                badgeEl.className = "kpi-badge-type type-staff";
            }

            const tbodyA = document.getElementById('kpi-tbody-A');
            const tbodyB = document.getElementById('kpi-tbody-B');
            const tbodyC = document.getElementById('kpi-tbody-C');

            tbodyA.innerHTML = ''; tbodyB.innerHTML = ''; tbodyC.innerHTML = '';
            document.querySelectorAll('.kpi-admin-col').forEach(col => col.style.display = isAdmin ? 'table-cell' : 'none');

            ['A', 'B', 'C'].forEach(sectionCode => {
                const targetTbody = sectionCode === 'A' ? tbodyA : sectionCode === 'B' ? tbodyB : tbodyC;
                const subSections = kpiDataList.filter(sub => sub.section === sectionCode);

                if (subSections.length === 0) {
                    targetTbody.innerHTML = `<tr><td colspan="${isAdmin ? 7 : 6}" style="text-align: center; color: #64748b; padding: 15px;">Chưa có mục La Mã nào.</td></tr>`;
                } else {
                    subSections.forEach(sub => {
                        let subAdminAction = '';
                        if (isAdmin) {
                            subAdminAction = `
                                <td style="text-align: center;" class="kpi-admin-col">
                                    <button class="btn-sm btn-edit" onclick="editSubSection('${sub.id}')"><i class="fa-solid fa-pen"></i> Sửa</button>
                                    <button class="btn-sm btn-delete" onclick="deleteSubSection('${sub.id}')"><i class="fa-solid fa-trash"></i> Xóa</button>
                                    <button class="btn-sm" style="background-color: #10b981; color: white;" onclick="addKPICriteriaItem('${sub.id}')">+ Tiêu chí</button>
                                </td>`;
                        }

                        targetTbody.innerHTML += `
                            <tr class="row-sub-header">
                                <td style="text-align: center; color: #1e40af;"><b>Mục ${sub.code}</b></td>
                                <td style="color: #1e3a8a;"><b>${sub.title}</b></td>
                                <td style="text-align: center; color: #94a3b8;">-</td>
                                <td style="text-align: center; color: #1e40af;"><b>${sub.maxScore}</b></td>
                                <td style="text-align: center; color: #64748b;">-</td>
                                <td style="text-align: center; color: #64748b;">-</td>
                                ${subAdminAction}
                            </tr>`;

                        if (!sub.items || sub.items.length === 0) {
                            targetTbody.innerHTML += `
                                <tr>
                                    <td></td>
                                    <td colspan="${isAdmin ? 6 : 5}" style="font-style: italic; color: #94a3b8; padding-left: 30px;">(Chưa có tiêu chí nhỏ nào)</td>
                                </tr>`;
                        } else {
                            sub.items.forEach((item, itemIdx) => {
                                let selfScoreHTML = `<span style="font-weight: bold; color: #2563eb;">${item.selfScore !== undefined ? item.selfScore : item.maxScore}</span>`;
                                if (!isAdmin && currentUser === kpiTargetUser) {
                                    selfScoreHTML = `<input type="number" value="${item.selfScore !== undefined ? item.selfScore : item.maxScore}" min="0" max="${item.maxScore}" step="0.5" onchange="updateKPISelfScoreItem('${sub.id}', '${item.id}', this.value)">`;
                                }

                                let adminScoreHTML = `<span style="font-weight: bold; color: #059669;">${item.adminScore !== undefined ? item.adminScore : item.maxScore}</span>`;
                                if (isAdmin) {
                                    adminScoreHTML = `<input type="number" value="${item.adminScore !== undefined ? item.adminScore : item.maxScore}" min="0" max="${item.maxScore}" step="0.5" onchange="updateKPIAdminScoreItem('${sub.id}', '${item.id}', this.value)">`;
                                }

                                let itemAdminAction = '';
                                if (isAdmin) {
                                    itemAdminAction = `
                                        <td style="text-align: center;" class="kpi-admin-col">
                                            <button class="btn-sm btn-edit" onclick="editKPICriteriaItem('${sub.id}', '${item.id}')"><i class="fa-solid fa-pen"></i> Sửa</button>
                                            <button class="btn-sm btn-delete" onclick="deleteKPICriteriaItem('${sub.id}', '${item.id}')"><i class="fa-solid fa-trash"></i> Xóa</button>
                                        </td>`;
                                }

                                targetTbody.innerHTML += `
                                    <tr>
                                        <td style="text-align: center; padding-left: 15px;">${itemIdx + 1}</td>
                                        <td style="padding-left: 20px;">${item.title}</td>
                                        <td style="color: #475569; font-size: 13px;">${item.criteria || '(Chưa có)'}</td>
                                        <td style="text-align: center; color: #0284c7;">${item.maxScore}</td>
                                        <td style="text-align: center;">${selfScoreHTML}</td>
                                        <td style="text-align: center; background-color: #f8fafc;">${adminScoreHTML}</td>
                                        ${itemAdminAction}
                                    </tr>`;
                            });
                        }
                    });
                }
            });
            calcKPITotal();
        }

        function addSubSection(section) {
            if (!isAdmin) return;
            const kpiType = getCurrentTargetKPIType();

            const code = prompt(`Nhập ký hiệu Mục La Mã (VD: I, II, III...):`);
            if (!code || !code.trim()) return;

            const title = prompt(`Nhập tiêu đề Mục La Mã [Mục ${section}]:`);
            if (!title || !title.trim()) return;

            const maxSecScore = sectionMaxScores[section] || 0;
            const currentSubSum = kpiDataList
                .filter(sub => sub.section === section)
                .reduce((sum, sub) => sum + sub.maxScore, 0);

            const remainingScore = maxSecScore - currentSubSum;
            if (remainingScore <= 0) {
                alert(`Mục lớn ${section} đã hết quỹ điểm!`);
                return;
            }

            const maxScoreInput = prompt(`Điểm tối đa cho Mục La Mã này (Tối đa còn lại: ${remainingScore}):`, remainingScore);
            const maxScore = Number(maxScoreInput);

            if (isNaN(maxScore) || maxScore <= 0 || currentSubSum + maxScore > maxSecScore) {
                alert("Điểm tối đa không hợp lệ!");
                return;
            }

            const updatedStructure = [...kpiDataList, {
                id: 'sub_' + Date.now(),
                section: section,
                code: code.trim().toUpperCase(),
                title: title.trim(),
                maxScore: maxScore,
                items: []
            }];

            saveMasterTemplateAndSync(kpiType, updatedStructure);
        }

        function editSubSection(subId) {
            if (!isAdmin) return;
            const kpiType = getCurrentTargetKPIType();
            const sub = kpiDataList.find(s => s.id === subId); if (!sub) return;

            const newCode = prompt("Sửa ký hiệu La Mã:", sub.code); if (newCode === null) return;
            const newTitle = prompt("Sửa tên Mục La Mã:", sub.title); if (newTitle === null) return;
            const newMaxInput = prompt("Sửa Điểm tối đa Mục La Mã:", sub.maxScore); if (newMaxInput === null) return;

            const newMaxScore = Number(newMaxInput);
            if (isNaN(newMaxScore) || newMaxScore <= 0) return;

            sub.code = newCode.trim().toUpperCase();
            sub.title = newTitle.trim();
            sub.maxScore = newMaxScore;

            saveMasterTemplateAndSync(kpiType, kpiDataList);
        }

        function deleteSubSection(subId) {
            if (!isAdmin) return;
            const kpiType = getCurrentTargetKPIType();
            if (confirm(`Bạn muốn xóa Mục La Mã này? Việc này sẽ đồng loạt xóa mục này trên tất cả tài khoản Bảng [${kpiType === 'leader' ? 'Lãnh đạo' : 'Cán bộ'}]!`)) {
                const updatedStructure = kpiDataList.filter(s => s.id !== subId);
                saveMasterTemplateAndSync(kpiType, updatedStructure);
            }
        }

        function addKPICriteriaItem(subId) {
            if (!isAdmin) return;
            const kpiType = getCurrentTargetKPIType();
            const sub = kpiDataList.find(s => s.id === subId); if (!sub) return;

            const title = prompt(`[Mục ${sub.code}] Nhập Nội dung Đánh giá mới:`); if (!title || !title.trim()) return;
            const criteria = prompt(`[Mục ${sub.code}] Nhập Tiêu chí đánh giá cụ thể:`); if (criteria === null) return;

            const currentItemsSum = (sub.items || []).reduce((sum, item) => sum + item.maxScore, 0);
            const remaining = sub.maxScore - currentItemsSum;

            const maxScoreInput = prompt(`Điểm tối đa tiêu chí (Tối đa còn lại: ${remaining}):`, remaining);
            const maxScore = Number(maxScoreInput);

            if (isNaN(maxScore) || maxScore <= 0 || currentItemsSum + maxScore > sub.maxScore) {
                alert("Điểm không hợp lệ!");
                return;
            }

            if (!sub.items) sub.items = [];
            sub.items.push({
                id: 'item_' + Date.now(),
                title: title.trim(),
                criteria: criteria.trim(),
                maxScore: maxScore,
                selfScore: maxScore,
                adminScore: maxScore
            });

            saveMasterTemplateAndSync(kpiType, kpiDataList);
        }

        function editKPICriteriaItem(subId, itemId) {
            if (!isAdmin) return;
            const kpiType = getCurrentTargetKPIType();
            const sub = kpiDataList.find(s => s.id === subId); if (!sub) return;
            const item = sub.items.find(i => i.id === itemId); if (!item) return;

            const newTitle = prompt("Sửa Nội dung Đánh giá:", item.title); if (newTitle === null) return;
            const newCriteria = prompt("Sửa Tiêu chí đánh giá:", item.criteria || ""); if (newCriteria === null) return;
            const newMaxInput = prompt("Sửa Điểm tối đa:", item.maxScore); if (newMaxInput === null) return;

            const newMax = Number(newMaxInput);
            if (isNaN(newMax) || newMax <= 0) return;

            item.title = newTitle.trim();
            item.criteria = newCriteria.trim();
            item.maxScore = newMax;

            saveMasterTemplateAndSync(kpiType, kpiDataList);
        }

        function deleteKPICriteriaItem(subId, itemId) {
            if (!isAdmin) return;
            const kpiType = getCurrentTargetKPIType();
            const sub = kpiDataList.find(s => s.id === subId); if (!sub) return;
            if (confirm(`Xóa tiêu chí này? Việc này sẽ đồng loạt xóa ở tất cả tài khoản Bảng [${kpiType === 'leader' ? 'Lãnh đạo' : 'Cán bộ'}]!`)) {
                sub.items = sub.items.filter(i => i.id !== itemId);
                saveMasterTemplateAndSync(kpiType, updatedStructure);
            }
        }

        function updateKPISelfScoreItem(subId, itemId, value) {
            const sub = kpiDataList.find(s => s.id === subId); if (!sub) return;
            const item = sub.items.find(i => i.id === itemId); if (!item) return;
            let val = Number(value); if (isNaN(val)) val = 0;
            if (val < 0) val = 0; if (val > item.maxScore) val = item.maxScore;
            item.selfScore = val;
            calcKPITotal();
        }

        function updateKPIAdminScoreItem(subId, itemId, value) {
            if (!isAdmin) return;
            const sub = kpiDataList.find(s => s.id === subId); if (!sub) return;
            const item = sub.items.find(i => i.id === itemId); if (!item) return;
            let val = Number(value); if (isNaN(val)) val = 0;
            if (val < 0) val = 0; if (val > item.maxScore) val = item.maxScore;
            item.adminScore = val;
            calcKPITotal();
        }

        function calcKPITotal() {
            let selfTotal = 0, adminTotal = 0;
            kpiDataList.forEach(sub => {
                if (sub.items) {
                    sub.items.forEach(item => {
                        selfTotal += Number(item.selfScore !== undefined ? item.selfScore : item.maxScore);
                        adminTotal += Number(item.adminScore !== undefined ? item.adminScore : item.maxScore);
                    });
                }
            });

            const maxTotal = (Number(sectionMaxScores.A) || 0) + (Number(sectionMaxScores.B) || 0) + (Number(sectionMaxScores.C) || 0);
            document.getElementById('kpi-total-self').innerText = selfTotal.toFixed(1);
            document.getElementById('kpi-total-max').innerText = maxTotal;
            document.getElementById('kpi-total-admin').innerText = adminTotal.toFixed(1);
        }

        function saveKPIRatingByAdmin() {
            if (!isAdmin) return;
            const now = new Date();
            const dateStr = String(now.getDate()).padStart(2, '0') + '/' + String(now.getMonth() + 1).padStart(2, '0') + '/' + now.getFullYear();
            const timeStr = String(now.getHours()).padStart(2, '0') + ':' + String(now.getMinutes()).padStart(2, '0') + ':' + String(now.getSeconds()).padStart(2, '0');
            lastKpiTimestamp = `${timeStr} - ${dateStr}`;
            saveKPIRatingStorage(lastKpiTimestamp);
            alert(`Đã lưu kết quả đánh giá KPI cho ${kpiTargetUser} lúc ${lastKpiTimestamp}!`);
        }

        function saveKPIRatingByUser() {
            saveKPIRatingStorage();
            alert("Đã lưu kết quả TỰ CHẤM thành công!");
        }

        function initCharts() {
            const ctx1 = document.getElementById('statusChart').getContext('2d');
            const ctx2 = document.getElementById('priorityChart').getContext('2d');
            if (statusChartInstance) statusChartInstance.destroy();
            if (priorityChartInstance) priorityChartInstance.destroy();

            let todo = tasks.filter(t => t.status === 'Chưa làm').length;
            let doing = tasks.filter(t => t.status === 'Đang làm').length;
            let done = tasks.filter(t => t.status === 'Hoàn thành').length;

            statusChartInstance = new Chart(ctx1, {
                type: 'doughnut',
                data: {
                    labels: [`Chưa làm (${todo})`, `Đang làm (${doing})`, `Hoàn thành (${done})`],
                    datasets: [{ data: [todo, doing, done], backgroundColor: ['#a5a6f6', '#2563eb', '#06b6d4'] }]
                },
                options: { responsive: true, maintainAspectRatio: false }
            });

            let high = tasks.filter(t => t.priority === 'Cao').length;
            let normal = tasks.filter(t => t.priority === 'Bình thường').length;
            let low = tasks.filter(t => t.priority === 'Thấp').length;

            priorityChartInstance = new Chart(ctx2, {
                type: 'bar',
                data: {
                    labels: ['Cao', 'Bình thường', 'Thấp'],
                    datasets: [{ label: 'Số lượng công việc', data: [high, normal, low], backgroundColor: ['#ff4d6d', '#e69100', '#10b981'] }]
                },
                options: { responsive: true, maintainAspectRatio: false }
            });
        }

        // Bảng Quản lý Users dành riêng cho Admin
        function renderUserManagementTable() {
            const tbody = document.getElementById('user-management-body');
            tbody.innerHTML = '';
            registeredUsers.forEach((u, i) => {
                const userType = u.kpiType || 'staff';
                const kpiTypeLabel = userType === 'leader' ? 
                    `<span class="kpi-badge-type type-leader">Bảng Lãnh đạo</span>` : 
                    `<span class="kpi-badge-type type-staff">Bảng Cán bộ</span>`;

                tbody.innerHTML += `
                    <tr>
                        <td>${i + 1}</td>
                        <td><b>${u.username}</b> ${u.username === ADMIN_USERNAME ? '<span style="color: #10b981; font-size: 11px; font-weight: bold;">(Admin)</span>' : ''}</td>
                        <td>${u.email}</td>
                        <td>
                            <select onchange="updateUserKPITypeFromTable('${u.username}', this.value)" style="padding: 4px 8px; border-radius: 4px; border: 1px solid #cbd5e1; font-weight: bold;">
                                <option value="staff" ${userType === 'staff' ? 'selected' : ''}>Cán bộ / Nhân viên</option>
                                <option value="leader" ${userType === 'leader' ? 'selected' : ''}>Lãnh đạo</option>
                            </select>
                            ${kpiTypeLabel}
                        </td>
                        <td>
                            <button class="btn-sm btn-edit" onclick="adminEditUserTasks('${u.username}')"><i class="fa-solid fa-pen-to-square"></i> Xem Công Việc</button>
                        </td>
                    </tr>`;
            });
        }

        function updateUserKPITypeFromTable(username, newType) {
            if (!isAdmin) return;
            if (confirm(`Xác nhận đổi Bảng KPI cho tài khoản ${username} sang [${newType === 'leader' ? 'Bảng Lãnh đạo' : 'Bảng Cán bộ'}]?`)) {
                db.ref(`users/${username}/kpiType`).set(newType, (err) => {
                    if (!err) {
                        db.ref(`kpi/${username}`).remove(); // Xóa dữ liệu cũ để áp dụng ngay Bảng KPI mới
                        alert(`Đã đổi loại bảng KPI của ${username} sang [${newType === 'leader' ? 'Bảng Lãnh đạo' : 'Bảng Cán bộ'}]`);
                    }
                });
            } else {
                renderUserManagementTable();
            }
        }

        function adminEditUserTasks(username) {
            document.getElementById('select-target-user').value = username;
            changeTargetUser(username);
            switchTab('danh-sach', document.querySelectorAll('.nav-item')[1]);
        }

        listenRealtimeUsers();
    </script>
</body>
</html>
