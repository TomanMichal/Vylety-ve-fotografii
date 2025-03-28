# Vylety-ve-fotografii
<!DOCTYPE html>
<html lang="cs">

<head>
    <meta charset="UTF-8">
    <title>Mapa výletů - OpenStreetMap</title>

    <!-- Leaflet CSS (pro zobrazení mapy) -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        #mapa {
            width: 100%;
            height: 600px;
        }

        form {
            margin: 10px 0;
        }

        label {
            margin-right: 10px;
        }

        button {
            margin-top: 10px;
        }
    </style>
</head>

<body>

    <h1>Moje mapa výletů</h1>

    <!-- Formulář pro přidání bodu -->
    <form id="formBod">
        <label>
            Název:
            <input type="text" id="nazev" required>
        </label>
        <label>
            Šířka (lat):
            <input type="number" id="lat" step="any" required>
        </label>
        <label>
            Délka (lng):
            <input type="number" id="lng" step="any" required>
        </label>
        <label>
            Odkaz na fotky:
            <input type="url" id="url" required>
        </label>
        <label>
            Kategorie:
            <select id="kategorie">
                <option value="mesto">Město</option>
                <option value="hory">Hory</option>
                <option value="jezero">Jezero</option>
            </select>
        </label>
        <button type="submit">Přidat bod</button>
    </form>

    <!-- Ovládací tlačítka pro filtrování kategorií -->
    <button onclick="showCategory('vse')">Vše</button>
    <button onclick="showCategory('mesto')">Města</button>
    <button onclick="showCategory('hory')">Hory</button>
    <button onclick="showCategory('jezero')">Jezera</button>

    <!-- Kontejner pro mapu -->
    <div id="mapa"></div>

    <!-- Leaflet JS (pro práci s OpenStreetMap) -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

    <script>
        // Inicializace mapy (střed: Praha, zoom: 7)
        var mapa = L.map('mapa').setView([50.087, 14.421], 7);

        // Načtení OpenStreetMap dlaždic
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }).addTo(mapa);

        // Ikona fotoaparátu (vlastní ikona)
        var cameraIcon = L.icon({
            iconUrl: 'https://cdn-icons-png.flaticon.com/512/685/685655.png',
            iconSize: [32, 32],
            iconAnchor: [16, 32],
            popupAnchor: [0, -32]
        });

        // Pole pro ukládání markerů
        var markery = [];

        // Funkce pro přidání bodu do mapy
        function pridejBod(misto) {
            var marker = L.marker([misto.lat, misto.lng], { icon: cameraIcon })
                .bindPopup(`<b>${misto.nazev}</b><br><a href="${misto.url}" target="_blank">Fotky z výletu</a>`)
                .addTo(mapa);
            markery.push({ marker, kategorie: misto.kategorie });
        }

        // Funkce pro filtrování bodů podle kategorie
        function showCategory(kategorie) {
            markery.forEach(item => {
                if (kategorie === "vse" || item.kategorie === kategorie) {
                    mapa.addLayer(item.marker);
                } else {
                    mapa.removeLayer(item.marker);
                }
            });
        }

        // Ošetření odeslání formuláře – přidání nového bodu
        document.getElementById('formBod').addEventListener('submit', function (e) {
            e.preventDefault();

            // Získání hodnot z formuláře
            var nazev = document.getElementById('nazev').value;
            var lat = parseFloat(document.getElementById('lat').value);
            var lng = parseFloat(document.getElementById('lng').value);
            var url = document.getElementById('url').value;
            var kategorie = document.getElementById('kategorie').value;

            // Kontrola správnosti souřadnic
            if (isNaN(lat) || isNaN(lng)) {
                alert("Zadej platné souřadnice!");
                return;
            }

            // Vytvoření objektu nového bodu
            var novyBod = { nazev, lat, lng, url, kategorie };

            // Přidání bodu na mapu
            pridejBod(novyBod);

            // Vymazání formuláře
            e.target.reset();
        });

    </script>

</body>

</html>
