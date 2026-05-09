<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>동도 도서관 키오스크</title>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@tabler/icons-webfont@3.19.0/dist/tabler-icons.min.css" />
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      font-family: 'Pretendard', 'Apple SD Gothic Neo', 'Noto Sans KR', sans-serif;
      background: #f5f4f0;
      min-height: 100vh;
      display: flex;
      justify-content: center;
      padding: 24px 16px 48px;
      color: #1a1a1a;
    }

    .kiosk {
      width: 100%;
      max-width: 680px;
    }

    /* ── 헤더 ── */
    .kiosk-header {
      background: #fff;
      border: 0.5px solid rgba(0,0,0,0.1);
      border-radius: 16px;
      padding: 1.25rem 1.5rem;
      display: flex; align-items: center; justify-content: space-between;
      margin-bottom: 14px;
      box-shadow: 0 1px 4px rgba(0,0,0,0.04);
    }
    .lib-name { font-size: 18px; font-weight: 600; color: #1a1a1a; }
    .lib-sub  { font-size: 12px; color: #888; margin-top: 2px; }
    .datetime { text-align: right; }
    .time     { font-size: 24px; font-weight: 600; color: #1a1a1a; display: block; }
    .datestr  { font-size: 12px; color: #888; margin-top: 2px; display: block; }

    /* ── 오늘의 행사 배너 ── */
    .today-banner {
      background: #fff;
      border: 0.5px solid rgba(0,0,0,0.1);
      border-radius: 16px;
      padding: 1rem 1.25rem;
      margin-bottom: 14px;
      cursor: pointer;
      display: flex; align-items: center; gap: 14px;
      transition: background 0.15s, border-color 0.15s;
      box-shadow: 0 1px 4px rgba(0,0,0,0.04);
    }
    .today-banner:hover { background: #fafaf8; border-color: rgba(0,0,0,0.18); }
    .today-icon { width: 44px; height: 44px; background: #FFF3E0; border-radius: 50%; display:flex; align-items:center; justify-content:center; font-size:22px; flex-shrink:0; color:#E65100; }
    .today-pill { background: #FFF3E0; color: #BF360C; font-size: 11px; font-weight: 600; padding: 3px 10px; border-radius: 100px; display:inline-block; margin-bottom:4px; }
    .today-title { font-size: 15px; font-weight: 600; color: #1a1a1a; }
    .today-meta  { font-size: 12px; color: #888; margin-top: 3px; }
    .today-arrow { margin-left:auto; color:#ccc; font-size:20px; flex-shrink:0; }

    /* ── 메뉴 그리드 ── */
    .menu-grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 12px;
      margin-bottom: 14px;
    }
    .menu-card {
      background: #fff;
      border: 0.5px solid rgba(0,0,0,0.1);
      border-radius: 16px;
      padding: 1.25rem 1rem;
      cursor: pointer;
      display: flex; flex-direction: column; align-items: center; gap: 10px;
      text-align: center; min-height: 112px; justify-content: center;
      transition: background 0.15s, border-color 0.15s, transform 0.1s;
      box-shadow: 0 1px 4px rgba(0,0,0,0.04);
    }
    .menu-card:hover  { background: #fafaf8; border-color: rgba(0,0,0,0.18); }
    .menu-card:active { transform: scale(0.97); }
    .menu-icon  { width:44px; height:44px; border-radius:50%; display:flex; align-items:center; justify-content:center; font-size:22px; }
    .menu-label { font-size: 14px; font-weight: 600; color: #1a1a1a; }
    .menu-desc  { font-size: 11px; color: #aaa; margin-top: 1px; }

    .ic-blue   { background:#E3F0FF; color:#1565C0; }
    .ic-teal   { background:#E0F4EE; color:#00695C; }
    .ic-purple { background:#EDE7F6; color:#512DA8; }
    .ic-coral  { background:#FBE9E7; color:#BF360C; }
    .ic-pink   { background:#FCE4EC; color:#880E4F; }
    .ic-gray   { background:#F3F3F1; color:#555; }
    .ic-amber  { background:#FFF8E1; color:#E65100; }

    /* ── 슬라이드 ── */
    .slide-section {
      border-radius: 16px;
      overflow: hidden;
      position: relative;
      border: 0.5px solid rgba(0,0,0,0.1);
      box-shadow: 0 1px 4px rgba(0,0,0,0.04);
    }
    .slide-track {
      display: flex;
      transition: transform 0.5s cubic-bezier(.4,0,.2,1);
    }
    .slide {
      min-width: 100%; height: 200px;
      position: relative;
      display: flex; align-items: flex-end;
      overflow: hidden;
    }
    .slide-bg {
      position: absolute; inset: 0;
      display: flex; align-items: center; justify-content: center;
      font-size: 100px; opacity: 0.15; pointer-events:none;
    }
    .slide-content { position:relative; z-index:1; padding:1.25rem 1.5rem; width:100%; }
    .slide-tag   { font-size:11px; font-weight:600; padding:3px 10px; border-radius:100px; display:inline-block; margin-bottom:8px; }
    .slide-title { font-size:19px; font-weight:700; line-height:1.3; margin-bottom:5px; }
    .slide-sub   { font-size:12px; line-height:1.5; }

    .sl1 { background:#0D2137; } .sl1 .slide-tag { background:#1565C0; color:#E3F0FF; } .sl1 .slide-title { color:#fff; } .sl1 .slide-sub { color:#90CAF9; }
    .sl2 { background:#0A2618; } .sl2 .slide-tag { background:#00695C; color:#E0F4EE; } .sl2 .slide-title { color:#fff; } .sl2 .slide-sub { color:#80CBC4; }
    .sl3 { background:#1A0A33; } .sl3 .slide-tag { background:#512DA8; color:#EDE7F6; } .sl3 .slide-title { color:#fff; } .sl3 .slide-sub { color:#CE93D8; }
    .sl4 { background:#3E1000; } .sl4 .slide-tag { background:#BF360C; color:#FBE9E7; } .sl4 .slide-title { color:#fff; } .sl4 .slide-sub { color:#FFAB91; }
    .sl5 { background:#0D2210; } .sl5 .slide-tag { background:#2E7D32; color:#E8F5E9; } .sl5 .slide-title { color:#fff; } .sl5 .slide-sub { color:#A5D6A7; }

    .slide-btn {
      position:absolute; top:50%; transform:translateY(-50%);
      z-index:10; background:rgba(0,0,0,0.3); border:none;
      width:34px; height:34px; border-radius:50%;
      display:flex; align-items:center; justify-content:center;
      cursor:pointer; color:#fff; font-size:18px; transition:background 0.15s;
    }
    .slide-btn:hover { background:rgba(0,0,0,0.5); }
    .slide-btn.prev { left:10px; }
    .slide-btn.next { right:10px; }
    .slide-dots { position:absolute; bottom:10px; left:50%; transform:translateX(-50%); display:flex; gap:6px; z-index:10; }
    .dot { width:6px; height:6px; border-radius:50%; background:rgba(255,255,255,0.35); cursor:pointer; transition:background 0.2s, transform 0.2s; }
    .dot.active { background:#fff; transform:scale(1.35); }

    /* ── 화면 공통 ── */
    .screen { display:none; background:#fff; border:0.5px solid rgba(0,0,0,0.1); border-radius:16px; padding:1.25rem 1.5rem; min-height:300px; box-shadow:0 1px 4px rgba(0,0,0,0.04); animation:fadeIn 0.15s ease; }
    .screen.active { display:block; }
    @keyframes fadeIn { from{opacity:0;transform:translateY(4px)}to{opacity:1;transform:translateY(0)} }

    .screen-header { display:flex; align-items:center; gap:10px; margin-bottom:1.25rem; padding-bottom:12px; border-bottom:0.5px solid rgba(0,0,0,0.08); }
    .back-btn { background:none; border:0.5px solid rgba(0,0,0,0.15); border-radius:10px; padding:6px 14px; font-size:13px; cursor:pointer; color:#555; display:flex; align-items:center; gap:4px; transition:background 0.1s; }
    .back-btn:hover { background:#f5f4f0; }
    .screen-title { font-size:16px; font-weight:600; color:#1a1a1a; }

    /* 이용안내 */
    .info-row { display:flex; gap:12px; padding:10px 0; border-bottom:0.5px solid rgba(0,0,0,0.07); }
    .info-row:last-child { border:none; }
    .info-label { font-size:13px; color:#888; min-width:82px; }
    .info-value { font-size:14px; color:#1a1a1a; font-weight:600; }
    .badge-open { background:#E8F5E9; color:#1B5E20; font-size:11px; padding:2px 8px; border-radius:100px; margin-left:6px; font-weight:600; display:inline-block; }
    .floor-grid { display:grid; grid-template-columns:repeat(2,1fr); gap:8px; margin-top:12px; }
    .floor-card { background:#f9f8f5; border-radius:10px; padding:10px 14px; }
    .floor-num  { font-size:12px; color:#aaa; }
    .floor-name { font-size:14px; font-weight:600; color:#1a1a1a; margin-top:2px; }

    /* 자료검색 */
    .search-row { display:flex; gap:8px; margin-bottom:16px; }
    .search-row input { flex:1; padding:10px 14px; border:0.5px solid rgba(0,0,0,0.18); border-radius:10px; font-size:14px; background:#fff; color:#1a1a1a; outline:none; transition:border-color 0.15s; }
    .search-row input:focus { border-color:#1565C0; }
    .search-row button { padding:10px 18px; border-radius:10px; font-size:14px; border:0.5px solid rgba(0,0,0,0.15); cursor:pointer; background:#f5f4f0; color:#1a1a1a; font-weight:600; transition:background 0.1s; }
    .search-row button:hover { background:#eae9e4; }
    .book-item { padding:12px 0; border-bottom:0.5px solid rgba(0,0,0,0.07); display:flex; gap:14px; align-items:flex-start; }
    .book-item:last-child { border:none; }
    .book-cover { width:44px; height:60px; border-radius:6px; display:flex; align-items:center; justify-content:center; font-size:22px; flex-shrink:0; background:#f5f4f0; }
    .avail-badge { font-size:11px; padding:2px 9px; border-radius:100px; font-weight:600; margin-top:6px; display:inline-block; }
    .avail-y { background:#E8F5E9; color:#1B5E20; }
    .avail-n { background:#FFEBEE; color:#B71C1C; }

    /* 문화행사 */
    .event-card { border:0.5px solid rgba(0,0,0,0.08); border-radius:12px; padding:12px 14px; margin-bottom:8px; display:flex; gap:14px; align-items:flex-start; }
    .event-date-box { background:#E3F0FF; border-radius:10px; width:50px; text-align:center; padding:6px 4px; flex-shrink:0; }
    .event-date-box .em { font-size:11px; color:#1565C0; font-weight:600; }
    .event-date-box .ed { font-size:22px; font-weight:700; color:#0D47A1; line-height:1; }
    .event-title { font-size:14px; font-weight:600; color:#1a1a1a; }
    .event-meta  { font-size:12px; color:#888; margin-top:3px; }
    .event-tag   { font-size:11px; padding:2px 9px; border-radius:100px; font-weight:600; display:inline-block; margin-top:6px; background:#EDE7F6; color:#4527A0; }

    /* 오늘의 행사 상세 */
    .today-hero { background:#f9f8f5; border-radius:14px; padding:1.25rem; margin-bottom:14px; display:flex; gap:16px; align-items:flex-start; }
    .today-hero-icon  { font-size:40px; flex-shrink:0; }
    .today-hero-badge { background:#FFF3E0; color:#BF360C; font-size:11px; font-weight:600; padding:3px 10px; border-radius:100px; display:inline-block; margin-bottom:8px; }
    .today-hero-title { font-size:16px; font-weight:700; color:#1a1a1a; margin-bottom:4px; }
    .today-hero-meta  { font-size:13px; color:#666; line-height:1.8; }
    .d-grid2 { display:grid; grid-template-columns:repeat(2,1fr); gap:8px; margin-bottom:14px; }
    .d-card  { border:0.5px solid rgba(0,0,0,0.08); border-radius:10px; padding:10px 14px; }
    .d-label { font-size:12px; color:#aaa; margin-bottom:3px; }
    .d-value { font-size:14px; font-weight:600; color:#1a1a1a; }
    .minor-card { border:0.5px solid rgba(0,0,0,0.08); border-radius:10px; padding:10px 14px; margin-bottom:6px; display:flex; justify-content:space-between; align-items:center; }
    .minor-name { font-size:13px; font-weight:600; color:#1a1a1a; }
    .minor-time { font-size:12px; color:#aaa; margin-top:2px; }
    .ts-soon { background:#FFF3E0; color:#BF360C; font-size:11px; padding:2px 10px; border-radius:100px; font-weight:600; }
    .ts-done { background:#F3F3F1; color:#888;   font-size:11px; padding:2px 10px; border-radius:100px; font-weight:600; }

    /* FAQ */
    .faq-item { border:0.5px solid rgba(0,0,0,0.08); border-radius:12px; margin-bottom:8px; overflow:hidden; }
    .faq-q { padding:13px 16px; font-size:14px; font-weight:600; color:#1a1a1a; cursor:pointer; display:flex; justify-content:space-between; align-items:center; background:#fff; transition:background 0.1s; }
    .faq-q:hover { background:#fafaf8; }
    .faq-a { display:none; padding:11px 16px; font-size:13px; color:#666; background:#f9f8f5; border-top:0.5px solid rgba(0,0,0,0.07); line-height:1.7; }
    .faq-a.open { display:block; }

    /* 동도튠 */
    .comic-grid { display:grid; grid-template-columns:repeat(3,1fr); gap:10px; }
    .comic-thumb { background:#f9f8f5; border-radius:12px; border:0.5px solid rgba(0,0,0,0.08); overflow:hidden; cursor:pointer; transition:border-color 0.15s; }
    .comic-thumb:hover { border-color:rgba(0,0,0,0.2); }
    .comic-img { height:90px; display:flex; align-items:center; justify-content:center; font-size:38px; }
    .comic-ep  { font-size:11px; color:#aaa; padding:6px 10px 2px; }
    .comic-ttl { font-size:13px; font-weight:600; color:#1a1a1a; padding:0 10px 10px; }

    /* 메타버스 */
    .meta-center { text-align:center; padding:2.5rem 1rem; }
    .meta-icon   { font-size:56px; margin-bottom:16px; color:#512DA8; }
    .meta-title  { font-size:17px; font-weight:700; color:#1a1a1a; margin-bottom:8px; }
    .meta-sub    { font-size:14px; color:#888; margin-bottom:24px; line-height:1.6; }
    .qr-box { background:#f9f8f5; border-radius:14px; padding:24px; display:inline-block; }
    .qr-inner { width:100px; height:100px; background:#eee; border-radius:10px; display:flex; align-items:center; justify-content:center; font-size:48px; color:#bbb; margin:0 auto; }
    .qr-label { font-size:12px; color:#aaa; margin-top:8px; }

    .main-view.hidden { display:none; }
  </style>
</head>
<body>
<div class="kiosk">

  <!-- 헤더 -->
  <div class="kiosk-header">
    <div>
      <div class="lib-name"><i class="ti ti-building-library" style="font-size:18px;vertical-align:-2px;margin-right:6px;"></i>동도 도서관</div>
      <div class="lib-sub">Dongdo Public Library</div>
    </div>
    <div class="datetime">
      <span class="time" id="clock">--:--</span>
      <span class="datestr" id="datestr">--</span>
    </div>
  </div>

  <div class="main-view" id="mainView">

    <!-- 오늘의 행사 배너 -->
    <div class="today-banner" onclick="showScreen('today')">
      <div class="today-icon"><i class="ti ti-calendar-star"></i></div>
      <div>
        <span class="today-pill">오늘의 문화행사</span>
        <div class="today-title">2026 인문독서 프로그램 — 봄 강좌</div>
        <div class="today-meta"><i class="ti ti-clock" style="font-size:12px;vertical-align:-1px;"></i> 14:00 – 16:00 &nbsp;|&nbsp; 지하 문화교육실 &nbsp;|&nbsp; <span style="color:#2E7D32;font-weight:600;">진행 예정</span></div>
      </div>
      <i class="ti ti-chevron-right today-arrow"></i>
    </div>

    <!-- 메뉴 -->
    <div class="menu-grid">
      <div class="menu-card" onclick="showScreen('info')">
        <div class="menu-icon ic-blue"><i class="ti ti-info-circle"></i></div>
        <div><div class="menu-label">이용안내</div><div class="menu-desc">시간 · 층별 안내</div></div>
      </div>
      <div class="menu-card" onclick="showScreen('search')">
        <div class="menu-icon ic-teal"><i class="ti ti-search"></i></div>
        <div><div class="menu-label">자료검색</div><div class="menu-desc">도서 · 위치 확인</div></div>
      </div>
      <div class="menu-card" onclick="showScreen('events')">
        <div class="menu-icon ic-purple"><i class="ti ti-calendar-event"></i></div>
        <div><div class="menu-label">문화행사 안내</div><div class="menu-desc">프로그램 · 일정</div></div>
      </div>
      <div class="menu-card" onclick="showScreen('faq')">
        <div class="menu-icon ic-coral"><i class="ti ti-help-circle"></i></div>
        <div><div class="menu-label">도움말 / FAQ</div><div class="menu-desc">자주 묻는 질문</div></div>
      </div>
      <div class="menu-card" onclick="showScreen('meta')">
        <div class="menu-icon ic-pink"><i class="ti ti-device-gamepad-2"></i></div>
        <div><div class="menu-label">메타버스 체험</div><div class="menu-desc">가상 도서관 입장</div></div>
      </div>
      <div class="menu-card" onclick="showScreen('comic')">
        <div class="menu-icon ic-gray"><i class="ti ti-pencil"></i></div>
        <div><div class="menu-label">동도튠 만화</div><div class="menu-desc">도서관 웹툰 보기</div></div>
      </div>
    </div>

    <!-- 슬라이드 홍보 배너 -->
    <div class="slide-section">
      <div class="slide-track" id="slideTrack">
        <div class="slide sl1"><div class="slide-bg">📖</div><div class="slide-content"><span class="slide-tag">5월 · 문화행사</span><div class="slide-title">2026 인문독서 프로그램<br>봄 강좌 참가자 모집</div><div class="slide-sub">5월 14일 (수) 14:00 · 지하 문화교육실 · 선착순 20명</div></div></div>
        <div class="slide sl2"><div class="slide-bg">🖨️</div><div class="slide-content"><span class="slide-tag">체험 프로그램</span><div class="slide-title">3D 프린팅 체험 교실<br>초등 5–6학년 모집</div><div class="slide-sub">5월 21일 (목) 10:00 · 3층 디지털자료실 · 무료</div></div></div>
        <div class="slide sl3"><div class="slide-bg">🎨</div><div class="slide-content"><span class="slide-tag">어린이 행사</span><div class="slide-title">북아트 워크숍<br>어린이 독후활동</div><div class="slide-sub">6월 7일 (일) 13:00 · 1층 어린이자료실 · 초등 1–4학년</div></div></div>
        <div class="slide sl4"><div class="slide-bg">🌐</div><div class="slide-content"><span class="slide-tag">신규 서비스</span><div class="slide-title">동도 메타버스 도서관<br>지금 바로 체험해보세요</div><div class="slide-sub">QR 스캔 한 번으로 가상 도서관 입장 · 상시 운영</div></div></div>
        <div class="slide sl5"><div class="slide-bg">📚</div><div class="slide-content"><span class="slide-tag">이달의 추천</span><div class="slide-title">5월 사서 추천 도서<br>지금 확인해보세요</div><div class="slide-sub">어린이 · 청소년 · 성인 부문별 큐레이션 · 2층 전시코너</div></div></div>
      </div>
      <button class="slide-btn prev" onclick="moveSlide(-1)" aria-label="이전"><i class="ti ti-chevron-left"></i></button>
      <button class="slide-btn next" onclick="moveSlide(1)"  aria-label="다음"><i class="ti ti-chevron-right"></i></button>
      <div class="slide-dots" id="slideDots"></div>
    </div>
  </div>

  <!-- 오늘의 문화행사 -->
  <div class="screen" id="screen-today">
    <div class="screen-header">
      <button class="back-btn" onclick="goBack()"><i class="ti ti-arrow-left"></i> 홈</button>
      <span class="screen-title">오늘의 문화행사</span>
    </div>
    <div class="today-hero">
      <div class="today-hero-icon">🎓</div>
      <div>
        <span class="today-hero-badge">오늘 · 5월 9일 (금)</span>
        <div class="today-hero-title">2026 인문독서 프로그램 — 봄 강좌</div>
        <div class="today-hero-meta">
          <i class="ti ti-clock" style="font-size:13px;vertical-align:-1px;"></i> 14:00 – 16:00<br>
          <i class="ti ti-map-pin" style="font-size:13px;vertical-align:-1px;"></i> 지하 문화교육실<br>
          <i class="ti ti-user" style="font-size:13px;vertical-align:-1px;"></i> 강사: 이민정 (문학평론가)
        </div>
      </div>
    </div>
    <div class="d-grid2">
      <div class="d-card"><div class="d-label">대상</div><div class="d-value">성인 일반</div></div>
      <div class="d-card"><div class="d-label">신청 현황</div><div class="d-value">18 / 20명</div></div>
      <div class="d-card"><div class="d-label">참가비</div><div class="d-value">무료</div></div>
      <div class="d-card"><div class="d-label">문의</div><div class="d-value">032-000-0000</div></div>
    </div>
    <div style="font-size:13px;color:#aaa;margin-bottom:8px;">오늘 그 외 일정</div>
    <div class="minor-card"><div><div class="minor-name">어린이 그림책 읽기</div><div class="minor-time">10:00 – 11:00 · 1층 어린이자료실</div></div><span class="ts-done">종료</span></div>
    <div class="minor-card"><div><div class="minor-name">인문독서 봄 강좌</div><div class="minor-time">14:00 – 16:00 · 지하 문화교육실</div></div><span class="ts-soon">예정</span></div>
    <div class="minor-card"><div><div class="minor-name">청소년 독서토론</div><div class="minor-time">16:30 – 18:00 · 2층 스터디룸</div></div><span class="ts-soon">예정</span></div>
  </div>

  <!-- 이용안내 -->
  <div class="screen" id="screen-info">
    <div class="screen-header">
      <button class="back-btn" onclick="goBack()"><i class="ti ti-arrow-left"></i> 홈</button>
      <span class="screen-title">이용안내</span>
    </div>
    <div class="info-row"><span class="info-label">운영시간</span><span class="info-value">평일 09:00 – 21:00 <span class="badge-open">운영중</span></span></div>
    <div class="info-row"><span class="info-label">주말·공휴일</span><span class="info-value">09:00 – 18:00</span></div>
    <div class="info-row"><span class="info-label">정기 휴관일</span><span class="info-value">매월 첫째·셋째 월요일, 법정공휴일 다음날</span></div>
    <div class="info-row"><span class="info-label">대출 한도</span><span class="info-value">1인 5권 / 14일</span></div>
    <div class="floor-grid">
      <div class="floor-card"><div class="floor-num">1층</div><div class="floor-name">어린이자료실 · 안내데스크</div></div>
      <div class="floor-card"><div class="floor-num">2층</div><div class="floor-name">일반자료실 · 열람실</div></div>
      <div class="floor-card"><div class="floor-num">3층</div><div class="floor-name">디지털자료실 · 스터디룸</div></div>
      <div class="floor-card"><div class="floor-num">지하</div><div class="floor-name">문화교육실 · 수유실</div></div>
    </div>
  </div>

  <!-- 자료검색 -->
  <div class="screen" id="screen-search">
    <div class="screen-header">
      <button class="back-btn" onclick="goBack()"><i class="ti ti-arrow-left"></i> 홈</button>
      <span class="screen-title">자료검색</span>
    </div>
    <div class="search-row">
      <input type="text" id="bookQuery" placeholder="제목 · 저자 · 키워드 입력" />
      <button onclick="searchBooks()"><i class="ti ti-search"></i> 검색</button>
    </div>
    <div id="bookResults"></div>
  </div>

  <!-- 문화행사 -->
  <div class="screen" id="screen-events">
    <div class="screen-header">
      <button class="back-btn" onclick="goBack()"><i class="ti ti-arrow-left"></i> 홈</button>
      <span class="screen-title">문화행사 안내</span>
    </div>
    <div class="event-card">
      <div class="event-date-box"><div class="em">5월</div><div class="ed">14</div></div>
      <div><div class="event-title">2026 인문독서 프로그램 — 봄 강좌</div><div class="event-meta"><i class="ti ti-clock" style="font-size:12px;vertical-align:-1px;"></i> 14:00 – 16:00 &nbsp;|&nbsp; 지하 문화교육실</div><span class="event-tag">신청 마감 D-3</span></div>
    </div>
    <div class="event-card">
      <div class="event-date-box"><div class="em">5월</div><div class="ed">21</div></div>
      <div><div class="event-title">3D 프린팅 체험 교실 (초등 5–6학년)</div><div class="event-meta"><i class="ti ti-clock" style="font-size:12px;vertical-align:-1px;"></i> 10:00 – 12:00 &nbsp;|&nbsp; 3층 디지털자료실</div><span class="event-tag">신청 가능</span></div>
    </div>
    <div class="event-card">
      <div class="event-date-box"><div class="em">6월</div><div class="ed">7</div></div>
      <div><div class="event-title">어린이 독후활동 — 북아트 워크숍</div><div class="event-meta"><i class="ti ti-clock" style="font-size:12px;vertical-align:-1px;"></i> 13:00 – 15:00 &nbsp;|&nbsp; 1층 어린이자료실</div><span class="event-tag">신청 가능</span></div>
    </div>
  </div>

  <!-- FAQ -->
  <div class="screen" id="screen-faq">
    <div class="screen-header">
      <button class="back-btn" onclick="goBack()"><i class="ti ti-arrow-left"></i> 홈</button>
      <span class="screen-title">도움말 / FAQ</span>
    </div>
    <div class="faq-item"><div class="faq-q" onclick="toggleFaq(this)">도서관 카드는 어디서 만드나요? <i class="ti ti-chevron-down"></i></div><div class="faq-a">1층 안내데스크에서 신분증을 지참하시면 바로 발급해 드립니다. 만 14세 미만은 보호자 동반이 필요합니다.</div></div>
    <div class="faq-item"><div class="faq-q" onclick="toggleFaq(this)">대출 기간 연장은 어떻게 하나요? <i class="ti ti-chevron-down"></i></div><div class="faq-a">이 키오스크의 도움말 메뉴, 도서관 앱, 또는 1층 안내데스크에서 1회 연장(14일) 가능합니다. 단, 이미 예약된 자료는 연장이 불가합니다.</div></div>
    <div class="faq-item"><div class="faq-q" onclick="toggleFaq(this)">스터디룸은 어떻게 예약하나요? <i class="ti ti-chevron-down"></i></div><div class="faq-a">도서관 홈페이지 또는 3층 담당자에게 문의하시면 됩니다. 당일 현장 예약도 가능합니다(잔여석 한함).</div></div>
    <div class="faq-item"><div class="faq-q" onclick="toggleFaq(this)">반납은 어디서 하나요? <i class="ti ti-chevron-down"></i></div><div class="faq-a">1층 입구 무인반납기 또는 2층 일반자료실 카운터에서 반납 가능합니다. 휴관일에는 무인반납기를 이용해 주세요.</div></div>
    <div class="faq-item"><div class="faq-q" onclick="toggleFaq(this)">프린터·복사기 이용은 유료인가요? <i class="ti ti-chevron-down"></i></div><div class="faq-a">흑백 A4 기준 1장 100원이며, 3층 디지털자료실에 비치되어 있습니다. 동전 또는 도서관 카드 충전 후 이용하실 수 있습니다.</div></div>
  </div>

  <!-- 메타버스 -->
  <div class="screen" id="screen-meta">
    <div class="screen-header">
      <button class="back-btn" onclick="goBack()"><i class="ti ti-arrow-left"></i> 홈</button>
      <span class="screen-title">메타버스 체험</span>
    </div>
    <div class="meta-center">
      <div class="meta-icon"><i class="ti ti-device-vr"></i></div>
      <div class="meta-title">동도 가상 도서관에 오신 것을 환영합니다</div>
      <div class="meta-sub">QR코드를 스캔하여 메타버스 도서관을 체험해 보세요.</div>
      <div class="qr-box">
        <div class="qr-inner"><i class="ti ti-qrcode"></i></div>
        <div class="qr-label">QR 스캔으로 입장</div>
      </div>
    </div>
  </div>

  <!-- 동도튠 만화 -->
  <div class="screen" id="screen-comic">
    <div class="screen-header">
      <button class="back-btn" onclick="goBack()"><i class="ti ti-arrow-left"></i> 홈</button>
      <span class="screen-title">동도튠 만화</span>
    </div>
    <div class="comic-grid">
      <div class="comic-thumb"><div class="comic-img">📚</div><div class="comic-ep">1화</div><div class="comic-ttl">동도 도서관의 하루</div></div>
      <div class="comic-thumb"><div class="comic-img">🦸</div><div class="comic-ep">2화</div><div class="comic-ttl">책 속의 영웅들</div></div>
      <div class="comic-thumb"><div class="comic-img">🔍</div><div class="comic-ep">3화</div><div class="comic-ttl">사라진 도서관 고양이</div></div>
      <div class="comic-thumb"><div class="comic-img">🌙</div><div class="comic-ep">4화</div><div class="comic-ttl">도서관의 밤</div></div>
      <div class="comic-thumb"><div class="comic-img">🎨</div><div class="comic-ep">5화</div><div class="comic-ttl">그림책 세상 속으로</div></div>
      <div class="comic-thumb"><div class="comic-img">🚀</div><div class="comic-ep">6화</div><div class="comic-ttl">우주 도서관 탐험</div></div>
    </div>
  </div>

</div><!-- /kiosk -->

<script>
  function pad(n){ return String(n).padStart(2,'0'); }
  function updateClock(){
    const now=new Date();
    document.getElementById('clock').textContent=pad(now.getHours())+':'+pad(now.getMinutes());
    const days=['일','월','화','수','목','금','토'];
    document.getElementById('datestr').textContent=now.getFullYear()+'.'+(now.getMonth()+1)+'.'+now.getDate()+' ('+days[now.getDay()]+')';
  }
  updateClock(); setInterval(updateClock,30000);

  function showScreen(id){
    document.getElementById('mainView').classList.add('hidden');
    document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active'));
    document.getElementById('screen-'+id).classList.add('active');
    window.scrollTo({top:0,behavior:'smooth'});
  }
  function goBack(){
    document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active'));
    document.getElementById('mainView').classList.remove('hidden');
    window.scrollTo({top:0,behavior:'smooth'});
  }

  // 슬라이드
  const TOTAL=5; let cur=0, timer=null;
  const dotsEl=document.getElementById('slideDots');
  for(let i=0;i<TOTAL;i++){
    const d=document.createElement('div');
    d.className='dot'+(i===0?' active':'');
    d.onclick=()=>{ resetTimer(); goSlide(i); };
    dotsEl.appendChild(d);
  }
  function goSlide(n){
    cur=(n+TOTAL)%TOTAL;
    document.getElementById('slideTrack').style.transform='translateX(-'+cur*100+'%)';
    document.querySelectorAll('.dot').forEach((d,i)=>d.classList.toggle('active',i===cur));
  }
  function moveSlide(dir){ resetTimer(); goSlide(cur+dir); }
  function resetTimer(){ clearInterval(timer); timer=setInterval(()=>goSlide(cur+1),4000); }
  resetTimer();

  // 자료검색
  const books=[
    {title:'채식주의자',author:'한강',call:'813.6 한11채',avail:true,icon:'📗'},
    {title:'82년생 김지영',author:'조남주',call:'813.6 조193팔',avail:false,icon:'📘'},
    {title:'아몬드',author:'손원평',call:'813.6 손67아',avail:true,icon:'📕'},
    {title:'불편한 편의점',author:'김호연',call:'813.6 김95불',avail:true,icon:'📙'},
  ];
  function searchBooks(){
    const q=document.getElementById('bookQuery').value.trim();
    const el=document.getElementById('bookResults');
    if(!q){el.innerHTML='<div style="font-size:13px;color:#aaa;padding:8px 0;">검색어를 입력해 주세요.</div>';return;}
    const res=books.filter(b=>b.title.includes(q)||b.author.includes(q));
    if(!res.length){el.innerHTML='<div style="font-size:13px;color:#aaa;padding:8px 0;">검색 결과가 없습니다.</div>';return;}
    el.innerHTML=res.map(b=>`<div class="book-item">
      <div class="book-cover">${b.icon}</div>
      <div>
        <div style="font-size:14px;font-weight:600;color:#1a1a1a;">${b.title}</div>
        <div style="font-size:12px;color:#aaa;margin-top:2px;">${b.author}</div>
        <div style="font-size:12px;color:#aaa;margin-top:4px;"><i class="ti ti-map-pin" style="font-size:12px;vertical-align:-1px;"></i> ${b.call}</div>
        <span class="avail-badge ${b.avail?'avail-y':'avail-n'}">${b.avail?'대출 가능':'대출 중'}</span>
      </div>
    </div>`).join('');
  }
  document.getElementById('bookQuery').addEventListener('keydown',e=>{if(e.key==='Enter')searchBooks();});

  function toggleFaq(el){
    const a=el.nextElementSibling;
    const isOpen=a.classList.contains('open');
    document.querySelectorAll('.faq-a').forEach(x=>x.classList.remove('open'));
    if(!isOpen)a.classList.add('open');
  }
</script>
</body>
</html>
