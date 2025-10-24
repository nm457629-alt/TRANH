[index_no_blur_clear3D.html](https://github.com/user-attachments/files/23126330/index_no_blur_clear3D.html)
<script type="text/javascript">
        var gk_isXlsx = false;
        var gk_xlsxFileLookup = {};
        var gk_fileData = {};
        function filledCell(cell) {
          return cell !== '' && cell != null;
        }
        function loadFileData(filename) {
        if (gk_isXlsx && gk_xlsxFileLookup[filename]) {
            try {
                var workbook = XLSX.read(gk_fileData[filename], { type: 'base64' });
                var firstSheetName = workbook.SheetNames[0];
                var worksheet = workbook.Sheets[firstSheetName];

                // Convert sheet to JSON to filter blank rows
                var jsonData = XLSX.utils.sheet_to_json(worksheet, { header: 1, blankrows: false, defval: '' });
                // Filter out blank rows (rows where all cells are empty, null, or undefined)
                var filteredData = jsonData.filter(row => row.some(filledCell));

                // Heuristic to find the header row by ignoring rows with fewer filled cells than the next row
                var headerRowIndex = filteredData.findIndex((row, index) =>
                  row.filter(filledCell).length >= filteredData[index + 1]?.filter(filledCell).length
                );
                // Fallback
                if (headerRowIndex === -1 || headerRowIndex > 25) {
                  headerRowIndex = 0;
                }

                // Convert filtered JSON back to CSV
                var csv = XLSX.utils.aoa_to_sheet(filteredData.slice(headerRowIndex)); // Create a new sheet from filtered array of arrays
                csv = XLSX.utils.sheet_to_csv(csv, { header: 1 });
                return csv;
            } catch (e) {
                console.error(e);
                return "";
            }
        }
        return gk_fileData[filename] || "";
        }
        </script><!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Bộ Sưu Tập Nghệ Thuật Kỹ Thuật Số</title>

  <!-- Updated Spline viewer -->
  <script type="module" src="https://unpkg.com/@splinetool/viewer@1.10.85/build/spline-viewer.js"></script>

  <!-- Fonts -->
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600&family=Playfair+Display:wght@400;600&display=swap" rel="stylesheet">

  <!-- PWA + favicon (cơ bản) -->
  <meta name="theme-color" content="#f7f5f3">
  <link rel="icon" href="data:;base64,iVBORw0KGgo="> <!-- placeholder -->
  <link rel="manifest" href="/manifest.json">

  <style>
    :root{
      --bg:#f7f5f3;
      --card:#ffffff;
      --accent:#c9a14a; /* vàng nhẹ */
      --text:#1f2933;
      --muted:#6b6f74;
      --shadow: 0 8px 24px rgba(31,41,51,0.08);
      --glass: rgba(255,255,255,0.6);
      --radius:14px;
      --transition: 300ms cubic-bezier(.2,.9,.3,1);
      --max-width:1200px;
      --glass-2: rgba(255,255,255,0.65);
    }

    /* dark theme vars - will be toggled by .dark on html */
    html.dark{ --bg:#0b0d10; --card:#0f1316; --accent:#d4b24a; --text:#e6e7e8; --muted:#98a0a6; --shadow: 0 12px 36px rgba(0,0,0,0.6); }

    /* reset */
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0;
      font-family:Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
      background:linear-gradient(180deg, var(--bg), rgba(0,0,0,0));
      color:var(--text);
      -webkit-font-smoothing:antialiased;
      -moz-osx-font-smoothing:grayscale;
      line-height:1.45;
      overflow-x:hidden;
    }

    /* Removed body::before background image */

    /* Spline background - removed all filters */
    spline-viewer{
      position:fixed;
      inset:0;
      width:100%;
      height:100%;
      z-index:-2;
      opacity: 1;
      pointer-events:none;
    }

    /* Top nav */
    header.topbar{
      position:sticky;
      top:0;
      z-index:60;
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap:12px;
      padding:12px 20px;
      background: linear-gradient(180deg, rgba(255,255,255,0.85), rgba(255,255,255,0.65));
      backdrop-filter: none;
      border-bottom:1px solid rgba(31,41,51,0.04);
      box-shadow:0 4px 18px rgba(31,41,51,0.03);
    }

    /* dark sticky header style */
    html.dark header.topbar{ background: linear-gradient(180deg, rgba(20,24,28,0.75), rgba(8,10,12,0.5)); border-bottom:1px solid rgba(255,255,255,0.03); }

    .brand{ display:flex; gap:14px; align-items:center; }
    .logo{ width:48px; height:48px; border-radius:10px; background:linear-gradient(135deg,var(--accent), #e7c96b); display:flex; align-items:center; justify-content:center; color:#fff; font-weight:700; font-family: "Playfair Display", serif; box-shadow: var(--shadow); }
    .brand h1{ margin:0; font-family:"Playfair Display", serif; font-weight:600; font-size:1.05rem; color:var(--text); letter-spacing:0.6px; }
    nav.topnav{ display:flex; gap:12px; align-items:center; margin-left:auto; }
    .topnav a{ text-decoration:none; color:var(--muted); padding:8px 12px; border-radius:10px; font-weight:600; transition: all var(--transition); font-size:0.95rem; }
    .topnav a:hover, .topnav a.active{ color:var(--text); background:linear-gradient(90deg, rgba(201,161,74,0.08), rgba(201,161,74,0.04)); box-shadow: 0 6px 18px rgba(201,161,74,0.06); transform:translateY(-2px); }

    /* theme toggle + controls */
    .controls{ display:flex; gap:10px; align-items:center; }
    .btn-ghost{ background:transparent; border:1px solid rgba(31,41,51,0.06); padding:8px 10px; border-radius:10px; cursor:pointer; font-weight:600 }
    html.dark .btn-ghost{ border:1px solid rgba(255,255,255,0.05) }

    main{ max-width:var(--max-width); margin:28px auto; padding:0 20px 80px; }

    /* Home hero */
    .hero{ display:flex; gap:28px; align-items:center; justify-content:space-between; background: linear-gradient(180deg, rgba(255,255,255,0.9), rgba(255,255,255,0.95)); border-radius:var(--radius); padding:34px; box-shadow: var(--shadow); overflow:hidden; min-height:260px; transition: transform var(--transition), box-shadow var(--transition); }
    html.dark .hero{ background: linear-gradient(180deg, rgba(18,20,22,0.6), rgba(12,14,16,0.6)); }
    .hero:hover{ transform: translateY(-4px) }
    .hero-left{ max-width:62%; }
    .hero .title{ margin:0 0 8px 0; font-family:"Playfair Display", serif; font-size:2.2rem; color:var(--text); letter-spacing:0.6px; }
    .hero .subtitle{ margin:0 0 18px 0; color:var(--muted); font-size:1.05rem; }
    .cta{ display:inline-flex; align-items:center; gap:10px; background:linear-gradient(90deg,var(--accent), #e6c74b); color:white; padding:12px 18px; border-radius:999px; font-weight:700; text-decoration:none; border:none; cursor:pointer; transition: transform var(--transition); box-shadow: 0 8px 26px rgba(201,161,74,0.18); }
    .cta:hover{ transform:translateY(-4px) }

    .hero-right{ width:40%; display:flex; align-items:center; justify-content:center; }
    .hero-preview{ width:160px; height:160px; border-radius:12px; overflow:hidden; box-shadow: 0 10px 30px rgba(31,41,51,0.08); border:8px solid #fff; display:flex; align-items:center; justify-content:center; }
    .hero-preview img{ width:100%; height:100%; object-fit:cover; display:block; }

    /* Gallery layout: sidebar + detail (kept but improved) */
    .gallery-wrap{ margin-top:26px; display:grid; grid-template-columns: 320px 1fr; gap:24px; align-items:start; }

    /* Sidebar list */
    .sidebar{ background:var(--card); border-radius:12px; padding:18px; box-shadow: var(--shadow); max-height:72vh; overflow:auto; }
    .sidebar h2{ margin:0 0 12px 0; font-family:"Playfair Display", serif; font-size:1.05rem; color:var(--text); }

    /* Search box */
    .searchbox{ display:flex; gap:8px; align-items:center; margin-bottom:12px; }
    .searchbox input{ flex:1; padding:10px 12px; border-radius:10px; border:1px solid rgba(31,41,51,0.06); background:transparent; font-size:0.95rem }
    .searchbox button{ padding:10px 12px; border-radius:10px; border:none; cursor:pointer; font-weight:700; background:linear-gradient(90deg,var(--accent), #e6c74b); color:#fff }

    .art-list{ list-style:none; padding:0; margin:0; display:flex; flex-direction:column; gap:10px; }
    .art-list li{ display:flex; gap:12px; align-items:center; padding:8px; border-radius:10px; cursor:pointer; transition: all var(--transition); color:var(--text); border:1px solid rgba(31,41,51,0.03); background:linear-gradient(180deg, rgba(255,255,255,0.98), rgba(250,248,247,0.98)); }
    .art-list li img{ width:54px; height:54px; object-fit:cover; border-radius:8px; border:2px solid rgba(201,161,74,0.12); }
    .art-list li:hover{ transform:translateX(6px); box-shadow: 0 8px 26px rgba(31,41,51,0.06); }
    .art-list li.active{ background: linear-gradient(90deg, rgba(201,161,74,0.08), rgba(255,255,255,0.6)); border:1px solid rgba(201,161,74,0.18); }

    /* Art detail */
    .content{ min-height:60vh; }
    .art-detail{ background:var(--card); border-radius:12px; padding:20px; box-shadow: var(--shadow); transition: opacity var(--transition), transform var(--transition); }
    .art-detail h3{ margin:0 0 6px 0; font-family:"Playfair Display", serif; font-size:1.45rem; }
    .art-detail p{ margin:0 0 10px 0; color:var(--muted) }
    .art-detail .art-img{ width:100%; max-height:520px; overflow:hidden; border-radius:10px; margin-top:16px; border:10px solid #fbfaf8; box-shadow: 0 18px 40px rgba(31,41,51,0.06); cursor:zoom-in; }
    .art-detail .art-img img{ width:100%; height:100%; object-fit:cover; display:block }

    /* All images page: grid */
    .all-images{ margin-top:26px; background:var(--card); border-radius:12px; padding:20px; box-shadow: var(--shadow); }
    .all-images h2{ margin:0 0 18px 0; font-family:"Playfair Display", serif; font-size:1.2rem; }
    .grid{ display:grid; gap:18px; grid-template-columns: repeat(3, 1fr); }
    .grid-item{ background:linear-gradient(180deg, #fff, #fbfaf8); border-radius:12px; overflow:hidden; border:1px solid rgba(31,41,51,0.03); transition: transform var(--transition), box-shadow var(--transition); cursor:zoom-in; }
    .grid-item:hover{ transform:translateY(-8px); box-shadow: 0 20px 40px rgba(31,41,51,0.06); }
    .grid-item .thumb{ width:100%; height:220px; object-fit:cover; display:block; }
    .grid-item .meta{ padding:12px; }
    .grid-item h3{ margin:0 0 6px 0; font-family:"Playfair Display", serif; font-size:1rem; }
    .grid-item p{ margin:0; color:var(--muted); font-size:0.95rem }

    /* footer */
    footer{ margin-top:40px; text-align:center; color:var(--muted); font-size:0.95rem; padding:24px 20px; }

    /* lightbox */
    .lightbox{ position:fixed; inset:0; display:flex; align-items:center; justify-content:center; background: rgba(5,10,10,0.6); z-index:120; opacity:0; pointer-events:none; transition:opacity 240ms ease; }
    .lightbox.open{ opacity:1; pointer-events:auto }
    .lightbox .box{ max-width:92%; max-height:92%; border-radius:12px; overflow:hidden; box-shadow: 0 30px 80px rgba(0,0,0,0.6); background:#fff; }
    .lightbox img{ display:block; width:100%; height:100%; object-fit:contain; background:#111 }

    /* loading overlay */
    .loading-overlay{ position:fixed; inset:0; background:linear-gradient(180deg, rgba(255,255,255,0.6), rgba(255,255,255,0.4)); backdrop-filter: none; display:flex; align-items:center; justify-content:center; z-index:200; transition: opacity 260ms ease; }
    .loading-overlay.hidden{ opacity:0; pointer-events:none; }
    .spinner{ width:68px; height:68px; border-radius:50%; border:8px solid rgba(0,0,0,0.06); border-top-color:var(--accent); animation: spin 900ms linear infinite; }
    @keyframes spin{ to{ transform:rotate(360deg) } }

    /* scroll to top */
    .to-top{ position:fixed; right:18px; bottom:20px; background:var(--card); border-radius:12px; padding:10px; box-shadow:var(--shadow); display:none; z-index:80; cursor:pointer }
    .to-top.show{ display:block }

    /* Responsive */
    @media (max-width:1000px){ .gallery-wrap{ grid-template-columns: 280px 1fr; gap:16px } .hero-left{ max-width:60% } .grid{ grid-template-columns: repeat(2, 1fr) } }
    @media (max-width:720px){ .hero{ flex-direction:column; align-items:flex-start } .hero-right{ width:100%; margin-top:8px; display:none } .gallery-wrap{ grid-template-columns: 1fr; } .sidebar{ max-height:none; order:2 } .content{ order:1 } .grid{ grid-template-columns: 1fr; } }

    /* accessibility focus */
    a:focus, button:focus, li:focus{ outline:3px solid rgba(201,161,74,0.18); outline-offset:4px }
    @media (prefers-reduced-motion: reduce){ *{ transition:none !important; animation:none !important } }
  </style>
</head>
<body>
  <!-- Updated Spline viewer URL -->
  <spline-viewer url="https://prod.spline.design/V2KF4z1lzhNcUaNj/scene.splinecode"></spline-viewer>

  <!-- top navigation -->
  <header class="topbar" role="banner">
    <div class="brand" aria-hidden="false">
      <div class="logo"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/ea/Van_Gogh_-_Starry_Night_-_Google_Art_Project.jpg/1280px-Van_Gogh_-_Starry_Night_-_Google_Art_Project.jpg" alt="Đêm Đầy Sao" style="width:100%; height:100%; object-fit:cover;"></div>
      <div>
        <h1>Bộ Sưu Tập Nghệ Thuật</h1>
        <div style="font-size:12px;color:var(--muted)">Bảo tàng kỹ thuật số • Tối giản &amp; tinh tế</div>
      </div>
    </div>

    <nav class="topnav" role="navigation" aria-label="Main navigation">
      <a href="#" id="nav-home" class="active">Trang chủ</a>
      <a href="#" id="nav-gallery">Bộ Sưu Tập</a>
      <a href="#" id="nav-all">Tất cả hình ảnh</a>
      <a href="https://nm457629-alt.github.io/H-A-S-TH-GI-I/" target="_blank" rel="noopener noreferrer">HỌA SĨ</a>
      <a href="https://artsandculture.google.com/search/asset?project=gigapixels" target="_blank" rel="noopener noreferrer">BẢO TÀNG TRANH</a>
    </nav>

    <div class="controls">
      <button id="themeToggle" aria-label="Chuyển giao diện" class="btn-ghost" title="Chuyển giao diện">🌗</button>
    </div>
  </header>

  <main>
    <!-- HOME -->
    <section id="home" class="page" aria-labelledby="homeTitle">
      <div class="hero" role="region" aria-labelledby="homeTitle">
        <div class="hero-left">
          <h2 id="homeTitle" class="title">NÉT VẼ VÀ TÂM HỒN</h2>
          <p class="subtitle">Bước vào Bảo tàng Kỹ thuật số — nơi các kiệt tác cổ điển gặp thiết kế tối giản hiện đại.</p>
          <button id="open-gallery-btn" class="cta" aria-controls="gallery">Khai mở bộ sưu tập</button>
        </div>
        <div class="hero-right" aria-hidden="true">
          <div class="hero-preview" title="Preview">
            <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/ec/Mona_Lisa%2C_by_Leonardo_da_Vinci%2C_from_C2RMF_retouched.jpg/960px-Mona_Lisa.jpg" alt="Mona Lisa preview">
          </div>
        </div>
      </div>
    </section>

    <!-- GALLERY -->
    <section id="gallery" class="page" style="display:none" aria-labelledby="galleryTitle">
      <div class="gallery-wrap" role="region">
        <aside class="sidebar" aria-label="Danh sách tranh">
          <h2 id="galleryTitle">Danh sách Tranh</h2>

          <!-- Search (mới) -->
          <div class="searchbox" role="search" aria-label="Tìm kiếm tranh">
            <input id="searchInput" type="search" placeholder="Tìm theo tên tác phẩm hoặc tác giả..." aria-label="Tìm kiếm tranh">
            <button id="clearSearch" title="Xoá">X</button>
          </div>

          <ul class="art-list" id="artList">
            <li data-art="mona-lisa" class="active" tabindex="0">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/ec/Mona_Lisa%2C_by_Leonardo_da_Vinci%2C_from_C2RMF_retouched.jpg/960px-Mona_Lisa.jpg" alt="Mona Lisa thumbnail"> Mona Lisa
            </li>
            <li data-art="starry-night" tabindex="0">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/ea/Van_Gogh_-_Starry_Night_-_Google_Art_Project.jpg/1280px-Van_Gogh_-_Starry_Night_-_Google_Art_Project.jpg" alt="Đêm Đầy Sao thumbnail"> Đêm Đầy Sao
            </li>
            <li data-art="the-scream" tabindex="0">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/f/f4/The_Scream.jpg" alt="Tiếng Thét thumbnail"> Tiếng Thét
            </li>
            <li data-art="the-kiss" tabindex="0">
              <img loading="lazy" src="https://product.hstatic.net/1000351483/product/3_e284b058878a42f694cb41f3a758acbc_master.jpg" alt="Nụ Hôn thumbnail"> Nụ Hôn
            </li>
            <li data-art="girl-pearl" tabindex="0">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/d/d7/Meisje_met_de_parel.jpg" alt="Cô Gái Đeo Hoa Tai Ngọc Trai thumbnail"> Cô Gái Đeo Hoa Tai Ngọc Trai
            </li>
            <li data-art="last-supper" tabindex="0">
              <img loading="lazy" src="https://cafebiz.cafebizcdn.vn/2018/12/23/photo-1-15455421686661451721410.png" alt="Bữa Ăn Tối Cuối Cùng thumbnail"> Bữa Ăn Tối Cuối Cùng
            </li>
            <li data-art="birth-venus" tabindex="0">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Sandro_Botticelli_-_La_nascita_di_Venere_-_Google_Art_Project_-_edited.jpg/1920px-Sandro_Botticelli_-_La_nascita_di_Venere_-_Google_Art_Project_-_edited.jpg" alt="Sự Ra Đời của Venus thumbnail"> Sự Ra Đời của Venus
            </li>
            <li data-art="creation-adam" tabindex="0">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/5b/Michelangelo_-_Creation_of_Adam_%28cropped%29.jpg/1920px-Michelangelo_-_Creation_of_Adam_%28cropped%29.jpg" alt="Sự Sáng Tạo của Adam thumbnail"> Sự Sáng Tạo của Adam
            </li>
            <li data-art="school-athens" tabindex="0">
              <img loading="lazy" src="https://vnn-imgs-f.vgcloud.vn/2018/02/05/07/.jpg?width=0&s=nECO_ylFxEEd6RzIS2XEQw" alt="Trường Học Athens thumbnail"> Trường Học Athens
            </li>
            <li data-art="guernica" tabindex="0">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/en/7/74/PicassoGuernica.jpg" alt="Guernica thumbnail"> Guernica
            </li>
            <li data-art="american-gothic" tabindex="0">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/cc/Grant_Wood_-_American_Gothic_-_Google_Art_Project.jpg/800px-Grant_Wood_-_American_Gothic_-_Google_Art_Project.jpg" alt="Gothic Mỹ thumbnail"> Gothic Mỹ
            </li>
            <li data-art="night-watch" tabindex="0">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/3a/La_ronda_de_noche%2C_por_Rembrandt_van_Rijn.jpg/1280px-La_ronda_de_noche%2C_por_Rembrandt_van_Rijn.jpg" alt="Đội Tuần Đêm thumbnail"> Đội Tuần Đêm
            </li>
            <li data-art="sunflowers" tabindex="0">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/4/46/Vincent_Willem_van_Gogh_127.jpg/800px-Vincent_Willem_van_Gogh_127.jpg" alt="Hoa Hướng Dương thumbnail"> Hoa Hướng Dương
            </li>
            <li data-art="persistence-memory" tabindex="0">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/en/d/dd/The_Persistence_of_Memory.jpg" alt="Sự Kiên Trì của Ký Ức thumbnail"> Sự Kiên Trì của Ký Ức
            </li>
            <li data-art="water-lilies" tabindex="0">
              <img loading="lazy" src="https://i1-giaitri.vnecdn.net/2023/11/11/ao-2907-1699686951.jpg?w=0&h=0&q=100&dpr=2&fit=crop&s=BkBI_E2uFxk366dsugruNA" alt="Hoa Súng thumbnail"> Hoa Súng
            </li>
            <li data-art="napoleon-crossing" tabindex="0">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/b/b2/Jacques-Louis_David_-_Napoleon_Crossing_the_Alps_-_Kunsthistorisches_Museum.jpg" alt="Napoleon Crossing thumbnail"> Napoleon Băng Qua Dãy Alps
            </li>
            <li data-art="the-wave" tabindex="0">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a5/Tsunami_by_hokusai_19th_century.jpg/1920px-Tsunami_by_hokusai_19th_century.jpg" alt="Sóng Lớn Kanagawa thumbnail"> Sóng Lớn Ngoài Khơi Kanagawa
            </li>
            <li data-art="whistlers-mother" tabindex="0">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/Whistlers_Mother_high_res.jpg/1024px-Whistlers_Mother_high_res.jpg" alt="Mẹ của Whistler thumbnail"> Mẹ của Whistler
            </li>
          </ul>
        </aside>

        <section class="content" id="detailArea" aria-live="polite">
          <!-- Art details (kept content) -->
          <article id="mona-lisa" class="art-detail" data-title="Mona Lisa">
            <h3>Mona Lisa</h3>
            <p><strong>Tác giả:</strong> Leonardo da Vinci (1503–1506)</p>
            <p>Bức họa nổi tiếng nhất thế giới với nụ cười bí ẩn.</p>
            <div class="art-img" data-full="https://upload.wikimedia.org/wikipedia/commons/e/ec/Mona_Lisa%2C_by_Leonardo_da_Vinci%2C_from_C2RMF_retouched.jpg">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/ec/Mona_Lisa%2C_by_Leonardo_da_Vinci%2C_from_C2RMF_retouched.jpg/1200px-Mona_Lisa.jpg" alt="Mona Lisa">
            </div>
          </article>

          <article id="starry-night" class="art-detail" style="display:none" data-title="Đêm Đầy Sao">
            <h3>Đêm Đầy Sao</h3>
            <p><strong>Tác giả:</strong> Vincent van Gogh, 1889</p>
            <p>Miêu tả bầu trời đêm đầy năng lượng.</p>
            <div class="art-img" data-full="https://upload.wikimedia.org/wikipedia/commons/e/ea/Van_Gogh_-_Starry_Night_-_Google_Art_Project.jpg">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/ea/Van_Gogh_-_Starry_Night_-_Google_Art_Project.jpg/1280px-Van_Gogh_-_Starry_Night_-_Google_Art_Project.jpg" alt="Đêm Đầy Sao">
            </div>
          </article>

          <article id="the-scream" class="art-detail" style="display:none" data-title="Tiếng Thét">
            <h3>Tiếng Thét</h3>
            <p><strong>Tác giả:</strong> Edvard Munch, 1893</p>
            <p>Biểu tượng của sự sợ hãi và nỗi đau nhân loại.</p>
            <div class="art-img" data-full="https://upload.wikimedia.org/wikipedia/commons/f/f4/The_Scream.jpg">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/f/f4/The_Scream.jpg" alt="Tiếng Thét">
            </div>
          </article>

          <article id="the-kiss" class="art-detail" style="display:none" data-title="Nụ Hôn">
            <h3>Nụ Hôn</h3>
            <p><strong>Tác giả:</strong> Gustav Klimt, 1907–1908</p>
            <p>Kiệt tác mang phong cách vàng son, biểu tượng của tình yêu.</p>
            <div class="art-img" data-full="https://product.hstatic.net/1000351483/product/3_e284b058878a42f694cb41f3a758acbc_master.jpg">
              <img loading="lazy" src="https://product.hstatic.net/1000351483/product/3_e284b058878a42f694cb41f3a758acbc_master.jpg" alt="Nụ Hôn">
            </div>
          </article>

          <article id="girl-pearl" class="art-detail" style="display:none" data-title="Cô Gái Đeo Hoa Tai Ngọc Trai">
            <h3>Cô Gái Đeo Hoa Tai Ngọc Trai</h3>
            <p><strong>Tác giả:</strong> Johannes Vermeer, 1665</p>
            <p>Được mệnh danh là "Mona Lisa của phương Bắc".</p>
            <div class="art-img" data-full="https://upload.wikimedia.org/wikipedia/commons/d/d7/Meisje_met_de_parel.jpg">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/d/d7/Meisje_met_de_parel.jpg" alt="Cô Gái Đeo Hoa Tai Ngọc Trai">
            </div>
          </article>

          <article id="last-supper" class="art-detail" style="display:none" data-title="Bữa Ăn Tối Cuối Cùng">
            <h3>Bữa Ăn Tối Cuối Cùng</h3>
            <p><strong>Tác giả:</strong> Leonardo da Vinci, 1498</p>
            <p>Miêu tả khoảnh khắc Chúa Jesus báo hiệu sự phản bội.</p>
            <div class="art-img" data-full="https://cafebiz.cafebizcdn.vn/2018/12/23/photo-1-15455421686661451721410.png">
              <img loading="lazy" src="https://cafebiz.cafebizcdn.vn/2018/12/23/photo-1-15455421686661451721410.png" alt="Bữa Ăn Tối Cuối Cùng">
            </div>
          </article>

          <article id="birth-venus" class="art-detail" style="display:none" data-title="Sự Ra Đời của Venus">
            <h3>Sự Ra Đời của Venus</h3>
            <p><strong>Tác giả:</strong> Sandro Botticelli, 1485</p>
            <p>Thể hiện thần thoại về nữ thần tình yêu bước ra từ biển cả.</p>
            <div class="art-img" data-full="https://upload.wikimedia.org/wikipedia/commons/0/0b/Sandro_Botticelli_-_La_nascita_di_Venere_-_Google_Art_Project_-_edited.jpg">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Sandro_Botticelli_-_La_nascita_di_Venere_-_Google_Art_Project_-_edited.jpg/1200px-Sandro_Botticelli_-_La_nascita_di_Venere_-_Google_Art_Project_-_edited.jpg" alt="Sự Ra Đời của Venus">
            </div>
          </article>

          <article id="creation-adam" class="art-detail" style="display:none" data-title="Sự Sáng Tạo của Adam">
            <h3>Sự Sáng Tạo của Adam</h3>
            <p><strong>Tác giả:</strong> Michelangelo, 1512</p>
            <p>Cảnh tượng nổi tiếng trên trần nhà nguyện Sistine.</p>
            <div class="art-img" data-full="https://upload.wikimedia.org/wikipedia/commons/5/5b/Michelangelo_-_Creation_of_Adam_%28cropped%29.jpg">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/5b/Michelangelo_-_Creation_of_Adam_%28cropped%29.jpg/1200px-Michelangelo_-_Creation_of_Adam_%28cropped%29.jpg" alt="Sự Sáng Tạo của Adam">
            </div>
          </article>

          <article id="school-athens" class="art-detail" style="display:none" data-title="Trường Học Athens">
            <h3>Trường Học Athens</h3>
            <p><strong>Tác giả:</strong> Raphael, 1509–1511</p>
            <p>Tôn vinh triết học cổ điển và những bộ óc vĩ đại.</p>
            <div class="art-img" data-full="https://vnn-imgs-f.vgcloud.vn/2018/02/05/07/.jpg">
              <img loading="lazy" src="https://vnn-imgs-f.vgcloud.vn/2018/02/05/07/.jpg?width=0&s=nECO_ylFxEEd6RzIS2XEQw" alt="Trường Học Athens">
            </div>
          </article>

          <article id="guernica" class="art-detail" style="display:none" data-title="Guernica">
            <h3>Guernica</h3>
            <p><strong>Tác giả:</strong> Pablo Picasso, 1937</p>
            <p>Tố cáo sự tàn bạo của chiến tranh thông qua siêu thực.</p>
            <div class="art-img" data-full="https://upload.wikimedia.org/wikipedia/en/7/74/PicassoGuernica.jpg">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/en/7/74/PicassoGuernica.jpg" alt="Guernica">
            </div>
          </article>

          <article id="american-gothic" class="art-detail" style="display:none" data-title="Gothic Mỹ">
            <h3>Gothic Mỹ</h3>
            <p><strong>Tác giả:</strong> Grant Wood, 1930</p>
            <p>Biểu tượng của tinh thần lao động người Mỹ.</p>
            <div class="art-img" data-full="https://upload.wikimedia.org/wikipedia/commons/c/cc/Grant_Wood_-_American_Gothic_-_Google_Art_Project.jpg">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/cc/Grant_Wood_-_American_Gothic_-_Google_Art_Project.jpg/800px-Grant_Wood_-_American_Gothic_-_Google_Art_Project.jpg" alt="Gothic Mỹ">
            </div>
          </article>

          <article id="night-watch" class="art-detail" style="display:none" data-title="Đội Tuần Đêm">
            <h3>Đội Tuần Đêm</h3>
            <p><strong>Tác giả:</strong> Rembrandt van Rijn, 1642</p>
            <p>Kiệt tác Baroque mô tả đội dân quân Amsterdam.</p>
            <div class="art-img" data-full="https://upload.wikimedia.org/wikipedia/commons/3/3a/La_ronda_de_noche%2C_por_Rembrandt_van_Rijn.jpg">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/3a/La_ronda_de_noche%2C_por_Rembrandt_van_Rijn.jpg/1280px-La_ronda_de_noche%2C_por_Rembrandt_van_Rijn.jpg" alt="Đội Tuần Đêm">
            </div>
          </article>

          <article id="sunflowers" class="art-detail" style="display:none" data-title="Hoa Hướng Dương">
            <h3>Hoa Hướng Dương</h3>
            <p><strong>Tác giả:</strong> Vincent van Gogh, 1888</p>
            <p>Thể hiện ánh sáng, hy vọng và đam mê nghệ thuật.</p>
            <div class="art-img" data-full="https://upload.wikimedia.org/wikipedia/commons/4/46/Vincent_Willem_van_Gogh_127.jpg">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/4/46/Vincent_Willem_van_Gogh_127.jpg/800px-Vincent_Willem_van_Gogh_127.jpg" alt="Hoa Hướng Dương">
            </div>
          </article>

          <article id="persistence-memory" class="art-detail" style="display:none" data-title="Sự Kiên Trì của Ký Ức">
            <h3>Sự Kiên Trì của Ký Ức</h3>
            <p><strong>Tác giả:</strong> Salvador Dalí, 1931</p>
            <p>Biểu tượng siêu thực với những chiếc đồng hồ tan chảy.</p>
            <div class="art-img" data-full="https://upload.wikimedia.org/wikipedia/en/d/dd/The_Persistence_of_Memory.jpg">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/en/d/dd/The_Persistence_of_Memory.jpg" alt="Sự Kiên Trì của Ký Ức">
            </div>
          </article>

          <article id="water-lilies" class="art-detail" style="display:none" data-title="Hoa Súng">
            <h3>Hoa Súng</h3>
            <p><strong>Tác giả:</strong> Claude Monet, 1906</p>
            <p>Một phần trong chuỗi tranh nổi tiếng ở Giverny.</p>
            <div class="art-img" data-full="https://i1-giaitri.vnecdn.net/2023/11/11/ao-2907-1699686951.jpg">
              <img loading="lazy" src="https://i1-giaitri.vnecdn.net/2023/11/11/ao-2907-1699686951.jpg?w=0&h=0&q=100&dpr=2&fit=crop&s=BkBI_E2uFxk366dsugruNA" alt="Hoa Súng">
            </div>
          </article>

          <article id="napoleon-crossing" class="art-detail" style="display:none" data-title="Napoleon Crossing">
            <h3>Napoleon Băng Qua Dãy Alps</h3>
            <p><strong>Tác giả:</strong> Jacques-Louis David, 1801</p>
            <p>Bức chân dung hùng tráng của Napoleon.</p>
            <div class="art-img" data-full="https://upload.wikimedia.org/wikipedia/commons/b/b2/Jacques-Louis_David_-_Napoleon_Crossing_the_Alps_-_Kunsthistorisches_Museum.jpg">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/b/b2/Jacques-Louis_David_-_Napoleon_Crossing_the_Alps_-_Kunsthistorisches_Museum.jpg" alt="Napoleon Crossing">
            </div>
          </article>

          <article id="the-wave" class="art-detail" style="display:none" data-title="Sóng Lớn Kanagawa">
            <h3>Sóng Lớn Ngoài Khơi Kanagawa</h3>
            <p><strong>Tác giả:</strong> Katsushika Hokusai, 1831</p>
            <p>Bức in khắc gỗ nổi tiếng nhất Nhật Bản.</p>
            <div class="art-img" data-full="https://upload.wikimedia.org/wikipedia/commons/a/a5/Tsunami_by_hokusai_19th_century.jpg">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a5/Tsunami_by_hokusai_19th_century.jpg/1200px-Tsunami_by_hokusai_19th_century.jpg" alt="Sóng Lớn Kanagawa">
            </div>
          </article>

          <article id="whistlers-mother" class="art-detail" style="display:none" data-title="Mẹ của Whistler">
            <h3>Mẹ của Whistler</h3>
            <p><strong>Tác giả:</strong> James McNeill Whistler, 1871</p>
            <p>Chân dung người mẹ, biểu tượng sự giản dị.</p>
            <div class="art-img" data-full="https://upload.wikimedia.org/wikipedia/commons/1/1b/Whistlers_Mother_high_res.jpg">
              <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/Whistlers_Mother_high_res.jpg/1024px-Whistlers_Mother_high_res.jpg" alt="Mẹ của Whistler">
            </div>
          </article>
        </section>
      </div>
    </section>

    <!-- ALL IMAGES (grid) -->
    <section id="all" class="page" style="display:none" aria-labelledby="allTitle">
      <div class="all-images">
        <h2 id="allTitle">Kho Tàng Nghệ Thuật</h2>
        <div class="grid" id="allGrid">
          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/commons/thumb/9/99/Las_Meninas_01.jpg/1280px-Las_Meninas_01.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/99/Las_Meninas_01.jpg/1280px-Las_Meninas_01.jpg" alt="Las Meninas">
            <div class="meta"><h3>Las Meninas (Các Thị Nữ)</h3><p><strong>Tác giả:</strong> Diego Velázquez, 1656</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://th.bing.com/th/id/OSK.HERO2wLuR7BnUZJHF6cga2yIrFUXrJ7UoPuzaikz9caos50?o=7&cb=12rm=3&rs=1&pid=ImgDetMain&o=7&rm=3">
            <img loading="lazy" class="thumb" src="https://th.bing.com/th/id/OSK.HERO2wLuR7BnUZJHF6cga2yIrFUXrJ7UoPuzaikz9caos50?o=7&cb=12rm=3&rs=1&pid=ImgDetMain&o=7&rm=3" alt="The Garden of Earthly Delights">
            <div class="meta"><h3>Khu Vườn Lạc Thú Trần Tục</h3><p><strong>Tác giả:</strong> Hieronymus Bosch, 1490–1500</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/commons/5/53/The_Arnolfini_portrait_%281434%29.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/commons/5/53/The_Arnolfini_portrait_%281434%29.jpg" alt="The Arnolfini portrait">
            <div class="meta"><h3>Chân Dung Arnolfini</h3><p><strong>Tác giả:</strong> Jan van Eyck, 1434</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/commons/thumb/0/02/La_Libert%C3%A9_guidant_le_peuple_-_Eug%C3%A8ne_Delacroix_-_Mus%C3%A9e_du_Louvre_Peintures_RF_129_-_apr%C3%A8s_restauration_2024.jpg/330px-La_Libert%C3%A9_guidant_le_peuple_-_Eug%C3%A8ne_Delacroix_-_Mus%C3%A9e_du_Louvre_Peintures_RF_129_-_apr%C3%A8s_restauration_2024.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/02/La_Libert%C3%A9_guidant_le_peuple_-_Eug%C3%A8ne_Delacroix_-_Mus%C3%A9e_du_Louvre_Peintures_RF_129_-_apr%C3%A8s_restauration_2024.jpg/330px-La_Libert%C3%A9_guidant_le_peuple_-_Eug%C3%A8ne_Delacroix_-_Mus%C3%A9e_du_Louvre_Peintures_RF_129_-_apr%C3%A8s_restauration_2024.jpg" alt="Liberty Leading the People">
            <div class="meta"><h3>Tự Do Dẫn Dắt Nhân Dân</h3><p><strong>Tác giả:</strong> Eugène Delacroix, 1830</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://rgb.vn/wp-content/uploads/2022/08/3-768x1024.jpg.webp">
            <img loading="lazy" class="thumb" src="https://rgb.vn/wp-content/uploads/2022/08/3-768x1024.jpg.webp" alt="The Swing">
            <div class="meta"><h3>Chiếc Xích Đu</h3><p><strong>Tác giả:</strong> Jean-Honoré Fragonard, 1767</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c5/Edouard_Manet_-_Olympia_-_Google_Art_ProjectFXD.jpg/500px-Edouard_Manet_-_Olympia_-_Google_Art_ProjectFXD.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c5/Edouard_Manet_-_Olympia_-_Google_Art_ProjectFXD.jpg/500px-Edouard_Manet_-_Olympia_-_Google_Art_ProjectFXD.jpg" alt="Olympia">
            <div class="meta"><h3>Olympia</h3><p><strong>Tác giả:</strong> Édouard Manet, 1863</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/commons/thumb/5/59/Monet_-_Impression%2C_Sunrise.jpg/800px-Monet_-_Impression%2C_Sunrise.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/59/Monet_-_Impression%2C_Sunrise.jpg/800px-Monet_-_Impression%2C_Sunrise.jpg" alt="Impression Sunrise">
            <div class="meta"><h3>Ấn Tượng, Mặt Trời Mọc</h3><p><strong>Tác giả:</strong> Claude Monet, 1872</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/en/e/e5/Magritte_TheSonOfMan.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/en/e/e5/Magritte_TheSonOfMan.jpg" alt="The Son of Man">
            <div class="meta"><h3>Con Trai Của Người Đàn Ông</h3><p><strong>Tác giả:</strong> René Magritte, 1964</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a3/Campbell%27s_Soup_Cans_by_Andy_Warhol.jpg/500px-Campbell%27s_Soup_Cans_by_Andy_Warhol.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a3/Campbell%27s_Soup_Cans_by_Andy_Warhol.jpg/500px-Campbell%27s_Soup_Cans_by_Andy_Warhol.jpg" alt="Campbell's Soup Cans">
            <div class="meta"><h3>Campbell's Soup Cans</h3><p><strong>Tác giả:</strong> Andy Warhol, 1962</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fd/El_Tres_de_Mayo%2C_by_Francisco_de_Goya%2C_from_Prado_thin_black_margin.jpg/1280px-El_Tres_de_Mayo%2C_by_Francisco_de_Goya%2C_from_Prado_thin_black_margin.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fd/El_Tres_de_Mayo%2C_by_Francisco_de_Goya%2C_from_Prado_thin_black_margin.jpg/1280px-El_Tres_de_Mayo%2C_by_Francisco_de_Goya%2C_from_Prado_thin_black_margin.jpg" alt="The Third of May 1808">
            <div class="meta"><h3>Ngày 3 Tháng 5 Năm 1808</h3><p><strong>Tác giả:</strong> Francisco Goya, 1814</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/commons/thumb/7/7d/A_Sunday_on_La_Grande_Jatte%2C_Georges_Seurat%2C_1884.jpg/500px-A_Sunday_on_La_Grande_Jatte%2C_Georges_Seurat%2C_1884.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/commons/thumb/7/7d/A_Sunday_on_La_Grande_Jatte%2C_Georges_Seurat%2C_1884.jpg/500px-A_Sunday_on_La_Grande_Jatte%2C_Georges_Seurat%2C_1884.jpg" alt="A Sunday on La Grande Jatte">
            <div class="meta"><h3>Chiều Chủ Nhật Trên Đảo Grande Jatte</h3><p><strong>Tác giả:</strong> Georges Seurat, 1884–1886</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/Matissedance.jpg/330px-Matissedance.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/Matissedance.jpg/330px-Matissedance.jpg" alt="The Dance">
            <div class="meta"><h3>Vũ Khúc</h3><p><strong>Tác giả:</strong> Henri Matisse, 1910</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/en/4/4c/Les_Demoiselles_d%27Avignon.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/en/4/4c/Les_Demoiselles_d%27Avignon.jpg" alt="Les Demoiselles d'Avignon">
            <div class="meta"><h3>Những Cô Nàng Ở Avignon</h3><p><strong>Tác giả:</strong> Pablo Picasso, 1907</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/commons/thumb/8/8c/David_-_The_Death_of_Socrates.jpg/1280px-David_-_The_Death_of_Socrates.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/commons/thumb/8/8c/David_-_The_Death_of_Socrates.jpg/1280px-David_-_The_Death_of_Socrates.jpg" alt="The Death of Socrates">
            <div class="meta"><h3>Cái Chết Của Socrates</h3><p><strong>Tác giả:</strong> Jacques-Louis David, 1787</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/commons/thumb/1/15/JEAN_LOUIS_TH%C3%89ODORE_G%C3%89RICAULT_-_La_Balsa_de_la_Medusa_%28Museo_del_Louvre%2C_1818-19%29.jpg/330px-JEAN_LOUIS_TH%C3%89ODORE_G%C3%89RICAULT_-_La_Balsa_de_la_Medusa_%28Museo_del_Louvre%2C_1818-19%29.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/commons/thumb/1/15/JEAN_LOUIS_TH%C3%89ODORE_G%C3%89RICAULT_-_La_Balsa_de_la_Medusa_%28Museo_del_Louvre%2C_1818-19%29.jpg/330px-JEAN_LOUIS_TH%C3%89ODORE_G%C3%89RICAULT_-_La_Balsa_de_la_Medusa_%28Museo_del_Louvre%2C_1818-19%29.jpg" alt="The Raft of the Medusa">
            <div class="meta"><h3>Chiếc Bè Của Chiến Thuyền Méduse</h3><p><strong>Tác giả:</strong> Théodore Géricault, 1818–1819</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/commons/thumb/2/20/Johannes_Vermeer_-_Het_melkmeisje_-_Google_Art_Project.jpg/330px-Johannes_Vermeer_-_Het_melkmeisje_-_Google_Art_Project.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/commons/thumb/2/20/Johannes_Vermeer_-_Het_melkmeisje_-_Google_Art_Project.jpg/330px-Johannes_Vermeer_-_Het_melkmeisje_-_Google_Art_Project.jpg" alt="The Milkmaid">
            <div class="meta"><h3>Cô Gái Vắt Sữa</h3><p><strong>Tác giả:</strong> Johannes Vermeer, ca. 1660</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/commons/thumb/5/5e/Vincent_Willem_van_Gogh_076.jpg/330px-Vincent_Willem_van_Gogh_076.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/5e/Vincent_Willem_van_Gogh_076.jpg/330px-Vincent_Willem_van_Gogh_076.jpg" alt="The Night Café">
            <div class="meta"><h3>Quán Café Đêm</h3><p><strong>Tác giả:</strong> Vincent van Gogh, 1888</p></div>
          </div>

          <div class="grid-item" tabindex="0" data-full="https://upload.wikimedia.org/wikipedia/commons/thumb/2/21/Pierre-Auguste_Renoir%2C_Le_Moulin_de_la_Galette.jpg/330px-Pierre-Auguste_Renoir%2C_Le_Moulin_de_la_Galette.jpg">
            <img loading="lazy" class="thumb" src="https://upload.wikimedia.org/wikipedia/commons/thumb/2/21/Pierre-Auguste_Renoir%2C_Le_Moulin_de_la_Galette.jpg/330px-Pierre-Auguste_Renoir%2C_Le_Moulin_de_la_Galette.jpg" alt="Bal du moulin de la Galette">
            <div class="meta"><h3>Buổi Khiêu Vũ Tại Moulin De La Galette</h3><p><strong>Tác giả:</strong> Pierre-Auguste Renoir, 1876</p></div>
          </div>
        </div>
      </div>
    </section>
  </main>

  <footer>
    &copy; 2025 Bảo tàng Nghệ thuật Kỹ thuật số • Thiết kế tối giản
  </footer>

  <!-- lightbox modal -->
  <div id="lightbox" class="lightbox" role="dialog" aria-hidden="true" aria-label="Xem ảnh lớn">
    <div class="box" role="document">
      <img id="lightboxImg" src="" alt="">
    </div>
  </div>

  <!-- loading overlay (mới) -->
  <div id="loading" class="loading-overlay" aria-hidden="false">
    <div>
      <div class="spinner" role="status" aria-hidden="true"></div>
      <div style="text-align:center;margin-top:12px;color:var(--muted)">Đang tải tài nguyên…</div>
    </div>
  </div>

  <!-- scroll to top -->
  <button id="toTop" class="to-top" title="Lên đầu trang">⬆</button>

  <script>
    // --- Existing routing kept, improved with fade animation ---
    const pages = { home: document.getElementById('home'), gallery: document.getElementById('gallery'), all: document.getElementById('all') };
    const navHome = document.getElementById('nav-home');
    const navGallery = document.getElementById('nav-gallery');
    const navAll = document.getElementById('nav-all');

    function setActiveNav(name){ [navHome, navGallery, navAll].forEach(a=>a.classList.remove('active')); if(name==='home') navHome.classList.add('active'); if(name==='gallery') navGallery.classList.add('active'); if(name==='all') navAll.classList.add('active'); }

    function showPage(name){
      // fade out current visible then show new
      Object.keys(pages).forEach(k=>{ if(k===name){ pages[k].style.display=''; pages[k].style.opacity=0; pages[k].animate([{opacity:0},{opacity:1}],{duration:320, fill:'forwards'}); } else { pages[k].style.display='none' } });
      setActiveNav(name);
      window.scrollTo({top:0,behavior:'instant'});
    }

    navHome.addEventListener('click', (e)=>{ e.preventDefault(); showPage('home'); });
    navGallery.addEventListener('click', (e)=>{ e.preventDefault(); showPage('gallery'); });
    navAll.addEventListener('click', (e)=>{ e.preventDefault(); showPage('all'); });
    document.getElementById('open-gallery-btn').addEventListener('click', ()=> showPage('gallery'));

    // Art list interaction (kept existing behavior)
    const artList = document.querySelectorAll('.art-list li');
    const details = document.querySelectorAll('.art-detail');

    function activateArt(id){
      artList.forEach(li => li.classList.toggle('active', li.dataset.art===id));
      details.forEach(d => {
        if(d.id===id){ d.style.display='block'; d.animate([{opacity:0, transform:'translateY(10px)'},{opacity:1, transform:'translateY(0)'}], {duration:300, easing:'ease-out'}); }
        else { d.style.display='none'; }
      });
    }

    artList.forEach(li => { li.addEventListener('click', ()=> { const target = li.dataset.art; activateArt(target); showPage('gallery'); }); li.addEventListener('keydown', (e)=> { if(e.key==='Enter' || e.key===' ') { e.preventDefault(); li.click(); } }); });

    // Lightbox (kept)
    const lightbox = document.getElementById('lightbox');
    const lightboxImg = document.getElementById('lightboxImg');
    function openLightbox(url, alt){ lightboxImg.src = url; lightboxImg.alt = alt || ''; lightbox.classList.add('open'); lightbox.setAttribute('aria-hidden','false'); document.body.style.overflow='hidden'; }
    function closeLightbox(){ lightbox.classList.remove('open'); lightboxImg.src=''; lightbox.setAttribute('aria-hidden','true'); document.body.style.overflow=''; }

    document.querySelectorAll('.art-img').forEach(el => { el.addEventListener('click', ()=> { const full = el.dataset.full || el.querySelector('img').src; const alt = el.querySelector('img')?.alt || ''; openLightbox(full, alt); }); el.addEventListener('keydown', (e)=> { if(e.key==='Enter') el.click(); }); });
    document.querySelectorAll('.grid-item').forEach(item => { item.addEventListener('click', ()=> { const full = item.dataset.full || item.querySelector('img').src; const alt = item.querySelector('img')?.alt || ''; openLightbox(full, alt); }); item.addEventListener('keydown', (e)=> { if(e.key==='Enter') item.click(); }); });
    lightbox.addEventListener('click', (e) => { if(e.target === lightbox) closeLightbox(); });
    document.addEventListener('keydown', (e) => { if(e.key === 'Escape') closeLightbox(); if(e.key === 'ArrowRight'){ const scrollable = document.querySelector('.grid'); if(scrollable) scrollable.scrollBy({left:320, behavior:'smooth'}); } else if(e.key === 'ArrowLeft'){ const scrollable = document.querySelector('.grid'); if(scrollable) scrollable.scrollBy({left:-320, behavior:'smooth'}); } });

    // initial state
    showPage('home');
    document.querySelectorAll('.art-img').forEach(n => n.setAttribute('tabindex', '0'));

    // prefetch images (kept) + loading overlay control
    (function prefetch(){ const imgs = [ 'https://upload.wikimedia.org/wikipedia/commons/e/ec/Mona_Lisa%2C_by_Leonardo_da_Vinci%2C_from_C2RMF_retouched.jpg', 'https://upload.wikimedia.org/wikipedia/commons/e/ea/Van_Gogh_-_Starry_Night_-_Google_Art_Project.jpg' ]; imgs.forEach(u=>{ const l = document.createElement('link'); l.rel='preload'; l.as='image'; l.href=u; document.head.appendChild(l); });
      // hide loading after a short delay or when DOMcontent loaded
      window.addEventListener('load', ()=>{ setTimeout(()=>{ document.getElementById('loading').classList.add('hidden'); document.getElementById('loading').setAttribute('aria-hidden','true'); }, 280); });
    })();

    // Search functionality (mới) - filter both sidebar list and grid
    const searchInput = document.getElementById('searchInput');
    const clearSearch = document.getElementById('clearSearch');
    function normalizeText(s){ return (s||'').toString().toLowerCase(); }
    function filterGallery(q){ const qn = normalizeText(q); document.querySelectorAll('.art-list li').forEach(li=>{ const text = normalizeText(li.textContent + ' ' + (li.getAttribute('data-art')||'')); li.style.display = (!qn || text.includes(qn)) ? '' : 'none'; }); document.querySelectorAll('.grid-item').forEach(g=>{ const meta = normalizeText(g.querySelector('.meta').textContent); g.style.display = (!qn || meta.includes(qn)) ? '' : 'none'; }); }
    searchInput.addEventListener('input', (e)=> filterGallery(e.target.value)); clearSearch.addEventListener('click', ()=>{ searchInput.value=''; filterGallery(''); searchInput.focus(); });

    // Theme toggle (mới) - saved to localStorage
    const themeToggle = document.getElementById('themeToggle');
    function applyTheme(t){ if(t==='dark') document.documentElement.classList.add('dark'); else document.documentElement.classList.remove('dark'); localStorage.setItem('theme', t); }
    themeToggle.addEventListener('click', ()=>{ const isDark = document.documentElement.classList.toggle('dark'); applyTheme(isDark ? 'dark' : 'light'); });
    // init theme
    (function(){ const saved = localStorage.getItem('theme'); if(saved) applyTheme(saved); else { const prefers = window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches; applyTheme(prefers ? 'dark' : 'light'); } })();

    // Scroll to top control
    const toTop = document.getElementById('toTop'); window.addEventListener('scroll', ()=>{ if(window.scrollY>420) toTop.classList.add('show'); else toTop.classList.remove('show'); }); toTop.addEventListener('click', ()=> window.scrollTo({top:0,behavior:'smooth'}));

    // close lightbox on Escape handled above

    // small keyboard hint: pressing / focuses search
    document.addEventListener('keydown', (e)=>{ if(e.key==='/' && document.activeElement.tagName !== 'INPUT' && document.activeElement.tagName !== 'TEXTAREA'){ e.preventDefault(); searchInput.focus(); } });

    // Respect reduced motion - already handled in CSS

    // If URL contains hash for pages, open it (kept with small improvement)
    if(location.hash){ const h = location.hash.replace('#',''); if(['gallery','all'].includes(h)) setTimeout(()=>showPage(h), 60); }

    // tiny accessibility: announce page changes via aria-live region (detailArea used)
  </script>
</body>
</html>
