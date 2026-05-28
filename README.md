<!DOCTYPE html>
<html lang="ko">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>K-METEOR: 대한민국 실시간 기상관측 플랫폼</title>
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link
        href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&family=Outfit:wght@300;400;500;600;700;800&family=Noto+Sans+KR:wght@300;400;500;700;900&display=swap"
        rel="stylesheet">
    <!-- Leaflet.js CSS (지도 라이브러리) -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
        integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin="" />
    <!-- Custom CSS -->
    <link rel="stylesheet" href="style.css">
    <!-- Lucide Icons CDN -->
    <script src="https://unpkg.com/lucide@latest"></script>
</head>

<body>
    <div class="app-container">
        <!-- 1. HEADER (상단 바) -->
        <header class="app-header">
            <div class="header-logo">
                <div class="logo-icon-wrapper">
                    <i data-lucide="cloud-lightning" class="logo-icon"></i>
                    <span class="logo-glow"></span>
                </div>
                <div class="logo-text">
                    <h1>K-METEOR</h1>
                    <p>대한민국 국가 기상 통합 관측 시스템</p>
                </div>
            </div>

            <!-- 실시간 기상 속보 티커 -->
            <div class="ticker-container">
                <div class="ticker-badge">속보</div>
                <div class="ticker-wrapper">
                    <div class="ticker-content" id="weather-ticker">
                        기상 정보를 성공적으로 연결하는 중입니다... 전국 주요 관측소 실시간 가동 중.
                    </div>
                </div>
            </div>

            <div class="header-status">
                <div class="time-display">
                    <i data-lucide="clock" class="icon-small"></i>
                    <span id="current-time">00:00:00</span>
                </div>
                <div class="location-status" id="gps-status">
                    <span class="status-dot pulsing"></span>
                    <span class="status-text">GPS 대기 중</span>
                </div>
            </div>
        </header>

        <!-- 2. MAIN DASHBOARD GRID -->
        <main class="dashboard-grid">

            <!-- LEFT COLUMN: 실시간 현황 및 7일 예측 -->
            <section class="left-panel">

                <!-- 현재 날씨 헤어로 카드 -->
                <div class="card weather-hero-card" id="hero-card">
                    <div class="card-bg-glow"></div>
                    <div class="hero-header">
                        <div class="location-info">
                            <span class="badge" id="data-source-badge">실시간 관측</span>
                            <h2 id="current-location">서울 특별시 기상관측소</h2>
                            <p class="coords" id="current-coords">위도: 37.5665 | 경도: 126.9780</p>
                        </div>
                        <div class="search-box-wrapper">
                            <select id="city-selector" class="city-select">
                                <option value="gps" selected>🛰️ 내 위치 자동 감지</option>
                                <option value="seoul">📍 서울특별시</option>
                                <option value="incheon">📍 인천광역시</option>
                                <option value="chuncheon">📍 강원도 춘천시</option>
                                <option value="gangneung">📍 강원도 강릉시</option>
                                <option value="daejeon">📍 대전광역시</option>
                                <option value="daegu">📍 대구광역시</option>
                                <option value="gwangju">📍 광주광역시</option>
                                <option value="busan">📍 부산광역시</option>
                                <option value="ulsan">📍 울산광역시</option>
                                <option value="jeju">📍 제주특별자치도</option>
                                <option value="dokdo">📍 독도 / 울릉도</option>
                            </select>
                        </div>
                    </div>

                    <div class="hero-body">
                        <div class="weather-primary">
                            <div class="weather-visual">
                                <div class="weather-icon-large" id="main-weather-icon">
                                    <i data-lucide="sun" class="weather-svg icon-sun"></i>
                                </div>
                                <div class="weather-status-desc" id="weather-description">맑음</div>
                            </div>
                            <div class="temp-display">
                                <span class="temp-number" id="current-temp">--</span>
                                <span class="temp-unit">°C</span>
                            </div>
                        </div>
                    </div>

                    <!-- 실시간 세부 기상 지표 -->
                    <div class="hero-details-grid">
                        <div class="detail-item">
                            <div class="item-label">체감 온도</div>
                            <div class="item-value-row">
                                <i data-lucide="thermometer" class="icon-cyan"></i>
                                <span class="item-value" id="feels-like-temp">--°C</span>
                            </div>
                        </div>
                        <div class="detail-item">
                            <div class="item-label">상대 습도</div>
                            <div class="item-value-row">
                                <i data-lucide="droplets" class="icon-blue"></i>
                                <span class="item-value" id="humidity-value">--%</span>
                            </div>
                        </div>
                        <div class="detail-item">
                            <div class="item-label">풍향 및 풍속</div>
                            <div class="item-value-row">
                                <i data-lucide="wind" class="icon-teal" id="wind-icon"></i>
                                <span class="item-value" id="wind-value">-- m/s</span>
                            </div>
                        </div>
                        <div class="detail-item">
                            <div class="item-label">자외선 지수 (UV)</div>
                            <div class="item-value-row">
                                <i data-lucide="sun-dim" class="icon-orange"></i>
                                <span class="item-value" id="uv-value">--</span>
                            </div>
                        </div>
                        <div class="detail-item">
                            <div class="item-label">대기압 (hPa)</div>
                            <div class="item-value-row">
                                <i data-lucide="gauge" class="icon-purple"></i>
                                <span class="item-value" id="pressure-value">--</span>
                            </div>
                        </div>
                        <div class="detail-item">
                            <div class="item-label">강수량 (1시간)</div>
                            <div class="item-value-row">
                                <i data-lucide="cloud-rain" class="icon-indigo"></i>
                                <span class="item-value" id="rain-value">0.0 mm</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- 7일 기상 예보 카드 -->
                <div class="card forecast-card">
                    <div class="card-header">
                        <div class="header-title">
                            <i data-lucide="calendar-range" class="title-icon"></i>
                            <h3>향후 7일간 기상 전망</h3>
                        </div>
                        <span class="forecast-update-time" id="forecast-update-info">KST 기준</span>
                    </div>
                    <div class="forecast-list" id="forecast-container">
                        <!-- JS에서 동적 로드 -->
                        <div class="skeleton-loader-list">
                            <div class="skeleton-row"></div>
                            <div class="skeleton-row"></div>
                            <div class="skeleton-row"></div>
                            <div class="skeleton-row"></div>
                            <div class="skeleton-row"></div>
                        </div>
                    </div>
                </div>
            </section>

            <!-- RIGHT COLUMN: 전국 지도 및 기상 분석 차트 -->
            <section class="right-panel">

                <!-- 기상 지도 카드 -->
                <div class="card map-card">
                    <div class="card-header">
                        <div class="header-title">
                            <i data-lucide="map" class="title-icon"></i>
                            <h3>실시간 전국 종합 기상 레이더 지도</h3>
                        </div>
                        <div class="map-legend">
                            <span class="legend-dot temp"></span> 기온
                        </div>
                    </div>
                    <div class="map-body">
                        <!-- 지도가 들어갈 컨테이너 -->
                        <div id="weather-map"></div>
                    </div>
                </div>

                <!-- 24시간 추이 차트 카드 -->
                <div class="card chart-card">
                    <div class="card-header">
                        <div class="header-title">
                            <i data-lucide="trending-up" class="title-icon"></i>
                            <h3>향후 24시간 온도 및 강수 변동 추이</h3>
                        </div>
                        <div class="chart-toggles">
                            <span class="badge badge-chart-temp">기온 (°C)</span>
                            <span class="badge badge-chart-rain">강수 확률 (%)</span>
                        </div>
                    </div>
                    <div class="chart-body">
                        <div class="canvas-wrapper">
                            <canvas id="hourly-trend-chart"></canvas>
                        </div>
                    </div>
                </div>

            </section>
        </main>

        <!-- FOOTER -->
        <footer class="app-footer">
            <div class="footer-content">
                <p>© 2026 K-METEOR. All data parsed dynamically from Open-Meteo Open-Source Meteorological API.</p>
                <div class="footer-links">
                    <span>데이터 갱신 주기: 15분</span>
                    <span class="divider">|</span>
                    <a href="https://open-meteo.com/" target="_blank" rel="noopener noreferrer">Open-Meteo API
                        Document</a>
                </div>
            </div>
        </footer>
    </div>

    <!-- Leaflet.js (지도 라이브러리) -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
        integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin=""></script>
    <!-- Chart.js (차트 라이브러리) -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Custom Application JS -->
    <script src="app.js"></script>
</body>

</html>
