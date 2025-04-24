<!DOCTYPE html>
<html lang="uk">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Карта амброзії у Кропивницькому | Еко-моніторинг</title>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <style>
    :root {
      --primary: #10B981;
      --primary-dark: #059669;
      --danger: #EF4444;
      --warning: #F59E0B;
      --gray: #6B7280;
      --light-gray: #F3F4F6;
      --dark: #111827;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    body {
      font-family: 'Inter', sans-serif;
      color: var(--dark);
      background-color: #f8fafc;
      line-height: 1.6;
    }

    #app {
      display: flex;
      flex-direction: column;
      height: 100vh;
    }

    header {
      background-color: white;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
      padding: 1rem 2rem;
      display: flex;
      justify-content: space-between;
      align-items: center;
      z-index: 1000;
    }

    .logo {
      display: flex;
      align-items: center;
      gap: 0.75rem;
    }

    .logo-icon {
      color: var(--primary);
      font-size: 1.5rem;
    }

    .logo-text {
      font-weight: 600;
      font-size: 1.25rem;
    }

    .user-actions {
      display: flex;
      gap: 1rem;
    }

    .btn {
      padding: 0.5rem 1rem;
      border-radius: 0.375rem;
      font-weight: 500;
      cursor: pointer;
      transition: all 0.2s;
      border: none;
      display: inline-flex;
      align-items: center;
      gap: 0.5rem;
    }

    .btn-primary {
      background-color: var(--primary);
      color: white;
    }

    .btn-primary:hover {
      background-color: var(--primary-dark);
    }

    .btn-outline {
      background-color: transparent;
      border: 1px solid var(--gray);
      color: var(--gray);
    }

    .btn-outline:hover {
      background-color: var(--light-gray);
    }

    .map-container {
      flex: 1;
      display: flex;
      position: relative;
    }

    #map {
      flex: 1;
      height: 100%;
      z-index: 1;
    }

    .sidebar {
      width: 320px;
      background-color: white;
      box-shadow: -2px 0 10px rgba(0,0,0,0.1);
      padding: 1.5rem;
      overflow-y: auto;
      transform: translateX(100%);
      transition: transform 0.3s ease;
      position: absolute;
      right: 0;
      top: 0;
      bottom: 0;
      z-index: 1000;
    }

    .sidebar.open {
      transform: translateX(0);
    }

    .sidebar-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 1.5rem;
    }

    .sidebar-title {
      font-size: 1.25rem;
      font-weight: 600;
    }

    .close-sidebar {
      background: none;
      border: none;
      font-size: 1.25rem;
      cursor: pointer;
      color: var(--gray);
    }

    .legend {
      margin-bottom: 2rem;
    }

    .legend-title {
      font-weight: 500;
      margin-bottom: 0.75rem;
    }

    .legend-item {
      display: flex;
      align-items: center;
      margin-bottom: 0.5rem;
    }

    .legend-color {
      width: 20px;
      height: 20px;
      border-radius: 50%;
      margin-right: 0.75rem;
    }

    .color-danger {
      background-color: var(--danger);
    }

    .color-warning {
      background-color: var(--warning);
    }

    .stats {
      background-color: var(--light-gray);
      border-radius: 0.5rem;
      padding: 1rem;
      margin-bottom: 2rem;
    }

    .stats-title {
      font-weight: 500;
      margin-bottom: 0.75rem;
    }

    .stat-item {
      display: flex;
      justify-content: space-between;
      margin-bottom: 0.5rem;
    }

    .floating-controls {
      position: absolute;
      bottom: 2rem;
      left: 2rem;
      z-index: 1000;
      display: flex;
      flex-direction: column;
      gap: 0.75rem;
    }

    .control-btn {
      width: 48px;
      height: 48px;
      border-radius: 50%;
      background-color: white;
      box-shadow: 0 4px 6px rgba(0,0,0,0.1);
      display: flex;
      align-items: center;
      justify-content: center;
      border: none;
      cursor: pointer;
      color: var(--dark);
      transition: all 0.2s;
    }

    .control-btn:hover {
      background-color: var(--light-gray);
      transform: translateY(-2px);
    }

    .control-btn.active {
      background-color: var(--primary);
      color: white;
    }

    .popup-custom {
      border-radius: 0.5rem;
    }

    .popup-custom .leaflet-popup-content-wrapper {
      border-radius: 0.5rem;
      padding: 0;
      overflow: hidden;
    }

    .popup-custom .leaflet-popup-content {
      margin: 0;
      width: 280px;
    }

    .popup-header {
      padding: 0.75rem 1rem;
      background-color: var(--primary);
      color: white;
      font-weight: 500;
    }

    .popup-body {
      padding: 1rem;
    }

    .popup-coords {
      font-size: 0.875rem;
      color: var(--gray);
      margin: 0.5rem 0;
    }

    .popup-actions {
      display: flex;
      flex-direction: column;
      gap: 0.5rem;
      margin-top: 1rem;
    }

    .popup-btn {
      width: 100%;
      padding: 0.5rem;
      border-radius: 0.375rem;
      font-weight: 500;
      cursor: pointer;
      transition: all 0.2s;
      border: none;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 0.5rem;
    }

    .popup-btn-danger {
      background-color: var(--danger);
      color: white;
    }

    .popup-btn-warning {
      background-color: var(--warning);
      color: white;
    }

    .popup-btn-secondary {
      background-color: var(--light-gray);
      color: var(--gray);
    }

    .notification {
      position: fixed;
      bottom: 1rem;
      left: 50%;
      transform: translateX(-50%);
      background-color: var(--dark);
      color: white;
      padding: 0.75rem 1.5rem;
      border-radius: 0.5rem;
      box-shadow: 0 4px 6px rgba(0,0,0,0.1);
      z-index: 2000;
      display: flex;
      align-items: center;
      gap: 0.75rem;
      opacity: 0;
      transition: opacity 0.3s ease;
    }

    .notification.show {
      opacity: 1;
    }

    .notification-success {
      background-color: var(--primary);
    }

    .notification-error {
      background-color: var(--danger);
    }

    .photo-preview {
      margin-top: 1rem;
      border-radius: 0.5rem;
      overflow: hidden;
      max-height: 150px;
    }

    .photo-preview img {
      width: 100%;
      height: auto;
      object-fit: cover;
    }

    .add-photo-btn {
      background-color: var(--light-gray);
      border: 1px dashed var(--gray);
      padding: 0.75rem;
      text-align: center;
      border-radius: 0.375rem;
      cursor: pointer;
      margin-top: 0.5rem;
    }

    .add-photo-btn:hover {
      background-color: #e5e7eb;
    }

    .map-type-controls {
      position: absolute;
      top: 1rem;
      right: 1rem;
      z-index: 1000;
      display: flex;
      gap: 0.5rem;
      background: white;
      padding: 0.5rem;
      border-radius: 0.5rem;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }

    .map-type-btn {
      padding: 0.5rem;
      border-radius: 0.25rem;
      cursor: pointer;
      font-size: 0.75rem;
      font-weight: 500;
    }

    .map-type-btn.active {
      background-color: var(--primary);
      color: white;
    }

    @media (max-width: 768px) {
      .sidebar {
        width: 280px;
      }

      .floating-controls {
        left: 1rem;
        bottom: 1rem;
      }

      .map-type-controls {
        top: 0.5rem;
        right: 0.5rem;
      }
    }
  </style>
</head>
<body>
  <div id="app">
    <header>
      <div class="logo">
        <i class="fas fa-leaf logo-icon"></i>
        <span class="logo-text">АМБРОЗІЯ-ТВАРИНА</span>
      </div>
      <div class="user-actions">
        <button class="btn btn-outline" id="helpBtn">
          <i class="fas fa-question-circle"></i> Допомога
        </button>
        <button class="btn btn-primary" id="reportBtn">
          <i class="fas fa-plus"></i> Додати звіт
        </button>
      </div>
    </header>

    <div class="map-container">
      <div id="map"></div>

      <div class="sidebar" id="sidebar">
        <div class="sidebar-header">
          <h3 class="sidebar-title">Інформація</h3>
          <button class="close-sidebar" id="closeSidebar">
            <i class="fas fa-times"></i>
          </button>
        </div>

        <div class="legend">
          <h4 class="legend-title">Легенда</h4>
          <div class="legend-item">
            <div class="legend-color color-danger"></div>
            <span>Підтверджена амброзія</span>
          </div>
          <div class="legend-item">
            <div class="legend-color color-warning"></div>
            <span>Підозра на амброзію</span>
          </div>
        </div>

        <div class="stats">
          <h4 class="stats-title">Статистика</h4>
          <div class="stat-item">
            <span>Підтверджених випадків:</span>
            <strong id="confirmedCases">0</strong>
          </div>
          <div class="stat-item">
            <span>Підозр:</span>
            <strong id="suspectedCases">0</strong>
          </div>
        </div>

        <div class="export-section">
          <button class="btn btn-outline" id="exportBtn" style="width: 100%; margin-bottom: 1rem;">
            <i class="fas fa-download"></i> Експортувати дані
          </button>
        </div>

        <div class="info">
          <h4 class="legend-title">Про амброзію</h4>
          <p style="font-size: 0.875rem; margin-bottom: 1rem;">
            Амброзія - небезпечний алерген, який може викликати серйозні проблеми зі здоров'ям. 
            Допоможіть нам відстежувати її поширення у місті.
          </p>
          <button class="btn btn-outline" style="width: 100%;">
            <i class="fas fa-info-circle"></i> Дізнатися більше
          </button>
        </div>
      </div>

      <div class="floating-controls">
        <button class="control-btn" id="locateBtn" title="Моя локація">
          <i class="fas fa-location-arrow"></i>
        </button>
        <button class="control-btn" id="sidebarBtn" title="Інформація">
          <i class="fas fa-info"></i>
        </button>
        <button class="control-btn active" id="addMarkerBtn" title="Додати маркер">
          <i class="fas fa-map-marker-alt"></i>
        </button>
      </div>

      <div class="map-type-controls">
        <button class="map-type-btn active" id="mapTypeStandard">Мапа</button>
        <button class="map-type-btn" id="mapTypeSatellite">Супутник</button>
        <button class="map-type-btn" id="mapTypeHybrid">Гібрид</button>
      </div>
    </div>

    <div class="notification" id="notification">
      <i class="fas fa-check-circle"></i>
      <span id="notificationText">Маркер успішно додано</span>
    </div>
  </div>

  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/js/all.min.js"></script>
  <script>
    // Ініціалізація карти
    const map = L.map('map').setView([48.5132, 32.2597], 13);
    let userMarkers = [];
    let isAddingMarker = true;
    let currentPhoto = null;

    // Статистика
    let stats = {
      confirmed: 0,
      suspected: 0
    };

    // Оновлення статистики
    function updateStats() {
      stats.confirmed = userMarkers.filter(m => m.options.icon === redIcon).length;
      stats.suspected = userMarkers.filter(m => m.options.icon === yellowIcon).length;

      document.getElementById('confirmedCases').textContent = stats.confirmed;
      document.getElementById('suspectedCases').textContent = stats.suspected;
    }

    // Типи карт
    const standardMap = L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      maxZoom: 19,
      attribution: '© <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
    });

    const satelliteMap = L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
      maxZoom: 19,
      attribution: 'Tiles © Esri'
    });

    const hybridMap = L.tileLayer('https://{s}.google.com/vt/lyrs=s,h&x={x}&y={y}&z={z}', {
      maxZoom: 19,
      subdomains: ['mt0', 'mt1', 'mt2', 'mt3'],
      attribution: 'Google Maps'
    });

    standardMap.addTo(map);

    // Кастомні іконки
    const redIcon = L.divIcon({
      className: 'custom-icon',
      html: `<div style="background-color: #EF4444; width: 24px; height: 24px; border-radius: 50%; border: 2px solid white; box-shadow: 0 2px 4px rgba(0,0,0,0.2); display: flex; align-items: center; justify-content: center;">
               <i class="fas fa-exclamation" style="color: white; font-size: 12px;"></i>
             </div>`,
      iconSize: [24, 24],
      iconAnchor: [12, 12]
    });

    const yellowIcon = L.divIcon({
      className: 'custom-icon',
      html: `<div style="background-color: #F59E0B; width: 24px; height: 24px; border-radius: 50%; border: 2px solid white; box-shadow: 0 2px 4px rgba(0,0,0,0.2); display: flex; align-items: center; justify-content: center;">
               <i class="fas fa-question" style="color: white; font-size: 12px;"></i>
             </div>`,
      iconSize: [24, 24],
      iconAnchor: [12, 12]
    });

    // Додавання маркера
    function addMarker(lat, lng, type, photo = null) {
      const icon = type === 'red' ? redIcon : yellowIcon;
      const marker = L.marker([lat, lng], { icon }).addTo(map);
      
      let photoContent = '';
      if (photo) {
        photoContent = `
          <div class="photo-preview">
            <img src="${photo}" alt="Фото доказ">
          </div>
        `;
      }

      const popupContent = `
        <div class="popup-header">
          ${type === 'red' ? 'Підтверджена амброзія' : 'Підозра на амброзію'}
        </div>
        <div class="popup-body">
          <div class="popup-coords">
            <i class="fas fa-map-pin"></i> ${lat.toFixed(6)}, ${lng.toFixed(6)}
          </div>
          ${photoContent}
          <div class="popup-actions">
            <button onclick="removeMarker(${marker._leaflet_id})" class="popup-btn popup-btn-secondary">
              <i class="fas fa-trash"></i> Видалити
            </button>
          </div>
        </div>
      `;

      marker.bindPopup(popupContent, { className: 'popup-custom' });
      marker.photo = photo;
      userMarkers.push(marker);

      updateStats();
      showNotification('Маркер успішно додано', 'success');
      return marker;
    }

    // Видалення маркера
    window.removeMarker = function(markerId) {
      const markerIndex = userMarkers.findIndex(m => m._leaflet_id === markerId);
      if (markerIndex !== -1) {
        map.removeLayer(userMarkers[markerIndex]);
        userMarkers.splice(markerIndex, 1);
        updateStats();
        showNotification('Маркер видалено', 'success');
      }
    }

    // Показати сповіщення
    function showNotification(text, type) {
      const notification = document.getElementById('notification');
      const notificationText = document.getElementById('notificationText');

      notificationText.textContent = text;
      notification.className = `notification ${type === 'success' ? 'notification-success' : 'notification-error'} show`;

      setTimeout(() => {
        notification.classList.remove('show');
      }, 3000);
    }

    // Обробник кліку по карті
    function handleMapClick(e) {
      if (!isAddingMarker) return;

      let photoContent = '';
      if (currentPhoto) {
        photoContent = `
          <div class="photo-preview">
            <img src="${currentPhoto}" alt="Попередній перегляд">
          </div>
          <button onclick="clearPhoto()" class="popup-btn popup-btn-secondary" style="margin-top: 0.5rem;">
            <i class="fas fa-trash"></i> Видалити фото
          </button>
        `;
      }

      const popupContent = `
        <div class="popup-header">
          Додати нову мітку
        </div>
        <div class="popup-body">
          <div class="popup-coords">
            <i class="fas fa-map-pin"></i> ${e.latlng.lat.toFixed(6)}, ${e.latlng.lng.toFixed(6)}
          </div>
          <div class="add-photo-btn" onclick="document.getElementById('photoInput').click()">
            <i class="fas fa-camera"></i> Додати фото (необов'язково)
          </div>
          <input type="file" id="photoInput" accept="image/*" style="display: none;">
          ${photoContent}
          <div class="popup-actions">
            <button onclick="addMarker(${e.latlng.lat}, ${e.latlng.lng}, 'red', currentPhoto || null)" class="popup-btn popup-btn-danger">
              <i class="fas fa-exclamation"></i> Підтверджена амброзія
            </button>
            <button onclick="addMarker(${e.latlng.lat}, ${e.latlng.lng}, 'yellow', currentPhoto || null)" class="popup-btn popup-btn-warning">
              <i class="fas fa-question"></i> Підозра на амброзію
            </button>
          </div>
        </div>
      `;

      L.popup({ className: 'popup-custom' })
        .setLatLng(e.latlng)
        .setContent(popupContent)
        .openOn(map);
    }

    // Очистити фото
    window.clearPhoto = function() {
      currentPhoto = null;
      document.getElementById('photoInput').value = '';
      showNotification('Фото видалено', 'success');
      map.closePopup();
    }

    // Експорт даних
    function exportData() {
      const data = userMarkers.map(marker => {
        return {
          type: marker.options.icon === redIcon ? 'confirmed' : 'suspected',
          lat: marker.getLatLng().lat,
          lng: marker.getLatLng().lng,
          photo: marker.photo || null,
          date: new Date().toISOString()
        };
      });

      const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = `ambrosia-data-${new Date().toISOString().slice(0, 10)}.json`;
      document.body.appendChild(a);
      a.click();
      document.body.removeChild(a);
      URL.revokeObjectURL(url);

      showNotification('Дані експортовано', 'success');
    }

    // Обробник завантаження фото
    document.addEventListener('DOMContentLoaded', function() {
      document.getElementById('photoInput')?.addEventListener('change', function(e) {
        const file = e.target.files[0];
        if (file) {
          const reader = new FileReader();
          reader.onload = function(event) {
            currentPhoto = event.target.result;
            map.closePopup();
            showNotification('Фото завантажено', 'success');
          };
          reader.readAsDataURL(file);
        }
      });
    });

    // Додамо кілька тестових маркерів
    addMarker(48.5100, 32.2600, 'red');
    addMarker(48.5150, 32.2550, 'yellow');
    addMarker(48.5120, 32.2650, 'red');
    addMarker(48.5080, 32.2500, 'yellow');

    // UI Interactions
    document.getElementById('sidebarBtn').addEventListener('click', () => {
      document.getElementById('sidebar').classList.toggle('open');
    });

    document.getElementById('closeSidebar').addEventListener('click', () => {
      document.getElementById('sidebar').classList.remove('open');
    });

    document.getElementById('addMarkerBtn').addEventListener('click', () => {
      isAddingMarker = !isAddingMarker;
      const btn = document.getElementById('addMarkerBtn');
      btn.classList.toggle('active', isAddingMarker);

      if (isAddingMarker) {
        showNotification('Режим додавання маркерів увімкнено', 'success');
      } else {
        showNotification('Режим додавання маркерів вимкнено', 'success');
      }
    });

    document.getElementById('locateBtn').addEventListener('click', () => {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            map.flyTo([position.coords.latitude, position.coords.longitude], 16);
            showNotification('Ваше місцезнаходження визначено', 'success');
          },
          () => {
            showNotification('Не вдалося отримати ваше місцезнаходження', 'error');
          }
        );
      } else {
        showNotification('Геолокація не підтримується вашим браузером', 'error');
      }
    });

    document.getElementById('reportBtn').addEventListener('click', () => {
      isAddingMarker = true;
      document.getElementById('addMarkerBtn').classList.add('active');
      showNotification('Оберіть місце на карті для додавання маркера', 'success');
    });

    document.getElementById('helpBtn').addEventListener('click', () => {
      showNotification('Натисніть на карту, щоб додати маркер амброзії', 'success');
    });

    document.getElementById('exportBtn').addEventListener('click', exportData);

    // Перемикачі типу карти
    document.getElementById('mapTypeStandard').addEventListener('click', () => {
      map.removeLayer(satelliteMap);
      map.removeLayer(hybridMap);
      standardMap.addTo(map);
      updateMapTypeButtons('mapTypeStandard');
    });

    document.getElementById('mapTypeSatellite').addEventListener('click', () => {
      map.removeLayer(standardMap);
      map.removeLayer(hybridMap);
      satelliteMap.addTo(map);
      updateMapTypeButtons('mapTypeSatellite');
    });

    document.getElementById('mapTypeHybrid').addEventListener('click', () => {
      map.removeLayer(standardMap);
      map.removeLayer(satelliteMap);
      hybridMap.addTo(map);
      updateMapTypeButtons('mapTypeHybrid');
    });

    function updateMapTypeButtons(activeButtonId) {
      const buttons = ['mapTypeStandard', 'mapTypeSatellite', 'mapTypeHybrid'];
      buttons.forEach(buttonId => {
        const btn = document.getElementById(buttonId);
        btn.classList.toggle('active', buttonId === activeButtonId);
      });
    }

    // Ініціалізація обробника кліків
    map.on('click', handleMapClick);
  </script>
</body>
</html>
