<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Карта загруженности путей</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        #map { height: 600px; }
        .hidden { display: none; }
    </style>
</head>
<body>
    <div>
        <input type="file" id="excelFile" accept=".xlsx, .xls">
        <button id="shareDataBtn" disabled>Поделиться данными</button>
    </div>
    <div id="map"></div>
    
    <script>
        const map = L.map('map').setView([56.0184, 92.8672], 5);
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
        let mapData = null;
        
        document.getElementById('excelFile').addEventListener('change', function (e) {
            const file = e.target.files[0];
            if (!file) return;
            
            const reader = new FileReader();
            reader.onload = function (e) {
                const data = new Uint8Array(e.target.result);
                const workbook = XLSX.read(data, { type: 'array' });
                const sheet = workbook.Sheets[workbook.SheetNames[0]];
                mapData = XLSX.utils.sheet_to_json(sheet);
                
                if (mapData.length > 0) {
                    document.getElementById('shareDataBtn').disabled = false;
                }
            };
            reader.readAsArrayBuffer(file);
        });
        
        document.getElementById('shareDataBtn').addEventListener('click', () => {
            if (!mapData) return;
            const encodedData = btoa(JSON.stringify(mapData));
            const shareUrl = ${window.location.origin}${window.location.pathname}?data=${encodedData};
            navigator.clipboard.writeText(shareUrl).then(() => {
                alert('Ссылка скопирована в буфер обмена!');
            }).catch(() => {
                alert('Ошибка копирования ссылки.');
            });
        });
    </script>
</body>
</html>
