
#  eGeodetskiNačrt - Verzija 1.0.0

eGeodetskiNačrt – Specifikacija standardizacije geodetskega načrta

## Podatkovna struktura:

- Verzija: 1.0.0
- Certifikat:
	- Izvajalec [text] (Naziv izvajalca)
	- IzvajalecID [number] (Matična številka podjetja)
	- Naziv [text] (naziv projekta)
	- ProjektID [text] (oznaka projekta)
	- DatumIzdelave [datetime `'YYYYMMDD'` [ISO8601](https://en.wikipedia.org/wiki/ISO_8601)] (Datum izdelave)
	- OdgovorniGeodet [text] ([IZS](https://www.izs.si/imenik/pooblasceni-inzenirji/) številka odgovornega geodeta)
	- KoordinatniSistem1D [number] (po standardu [EPSG](https://epsg.io/) - [Primer](https://www.e-prostor.gov.si/podrocja/drzavni-topografski-sistem/drugo/razno/epsg-kode-za-slovenijo/?acitem=1392-1393))
	- KoordinatniSistem2D [number] (po standardu [EPSG](https://epsg.io/) - [Primer](https://www.e-prostor.gov.si/podrocja/drzavni-topografski-sistem/drugo/razno/epsg-kode-za-slovenijo/?acitem=1392-1393))
- GeodetskiNacrt (Geodetski načrt po specifikaciji formata [GeoJSON](https://geojson.org/)):
	- Element (Točka/Linija/Poligon):
		- TipID [number] (ID tipa elementa glede na [Topografski Ključ](http://fgg-web.fgg.uni-lj.si/~/mkuhar/Pouk/DetIzmera/Topografski_kljuc_maj_2006.pdf) ali eTopografskiKljuč)
		- SlojID [[enum number](#Seznam-slojev)] (ID sloja)
- SeznamTock (Informacije o točkah po specifikaciji formata [GeoJSON](https://geojson.org/)):
	- Točka:
		- ID [number] (referenca na ID podatkovnega vira)
		- (Opcijsko) s1D [m] (sigma 1D)
		- (Opcijsko) s2D [m] (sigma 2D)
- ProstorskiPodatki:
	- ID [Number] (ID podatkovnega vira)
	- Tip [[enum number](#Seznam-podatkov)]:
	- Podatki [text/null] (referenca na datoteko/URL)
	- s1D [m] (sigma 1D)
	- s2D [m] (sigma 2D)


### Seznam slojev:

	1 - Geodetska osnova:
		1 - Geodetske točka
	2 - Infrastruktura:
		1 - Cesta
		2 - Bankina
		3 - Pločnik
		4 - Kolesarska steza
		5 - Dvorišče
		6 - Objekt	
	3 - Narava:
		1 - Gozd
		2 - Zelenica / Travnik
		3 - Njiva
		4 - Voda
	4 - Teren:
		1 - Brežina
		2 - Kote terena
		3 - Plastnice
	5 - Grajeni elementi:
		1 - Oporni zid
		2 - Ograja
	6 - Upravni Režimi:
		1 - Parcelne meje
	7 - Gospodarska javna infrastruktura:
		1 - Vodovod
		2 - Kanalizacija
		3 - Elektrika
		4 - Javna razsvetljava
		5 - Elektronske komunikacije
	9 - Ostalo:
		1 - Ostalo
		

### Seznam podatkov:

	1 - GNSS (*.koo / *.txt)
	2 - Tahimetrija (*.koo / *.txt)
	3 - Oblak Točk (*.las / *.laz / *.e57)
	4 - Ortofoto (*.tif + *.tfw / *.geoTiff)
	5 - DMR (*.tif + *.tfw / *.geoTiff)
	6 - Mesh (*.obj)

## Primer s specifikacijo:

	{
		"Verzija": "1.0.0",
		"Certifikat":
		{
			"Izvajalec": "Geodetski Zavod Slovenije d.o.o.",
			"IzvajalecID": 5162162001,
			"Naziv": "Geodetski načrt za primer specifikacije",
			"ProjektID": "DN-2023-0015",
			"DatumIzdelave": "20230315",
			"OdgovorniGeodet": "Geo152121",
			"KoordinatniSistem1D: "EPSG:8690",
			"KoordinatniSistem2D: "EPSG:3794"
		},
		"GeodetskiNacrt":
		{
			"type": "FeatureCollection",
			"features": [
				{
					"type": "Feature",
					"geometry": {
						"type": "Polygon",
						"coordinates": [
							[
								[
									444930.64,
									112476.81,
									321.03
								],
								[
									444938.97,
									112463.09,
									321.06
								],... dodatne točke poligona
							]
						]
					},	
					"properties": {
						"TipID": 330190, -> Pločnik, raven ali nagnjen, rampa
						"SlojID": 23     -> Pločnik
					}
				},
				{
					"type": "Feature",
					"geometry": {
						"type": "Point",
						"coordinates": [
							439483.08,
							110043.46,
							378.37
						]
					},	
					"properties": {
						"TipID": 330210, -> Semafor
						"SlojID": 91     -> Pločnik
					}
				},... Drugi elementi
			]
		},
		"SeznamTock":
		{
			"type": "FeatureCollection",
			"features": [
				{
					"type": "Feature",
					"geometry": {
						"type": "Point",
						"coordinates": [
							444930.64,
							112476.81,
							321.03
						]
					},	
					"properties": {
						"ID": 1
					}
				},
				{
					"type": "Feature",
					"geometry": {
						"type": "Point",
						"coordinates": [
							444938.97,
							112463.09,
							321.06
						]
					},	
					"properties": {
						"ID": 2
					}
				},
				{
					"type": "Feature",
					"geometry": {
						"type": "Point",
						"coordinates": [
							439483.08,
							110043.46,
							378.37
						]
					},	
					"properties": {
						"ID": 1
						"s1D": 0.01,
						"s2D": 0.01
					}
				},... Druge točke
			]
		},
		"ProstorskiPodatki"
		[
			{
				"ID": 1,
				"Tip": 3,
				"Podatki": "http://spletnastran.si/oblaktock.las",
				"s1D": 0.02,
				"s2D": 0.02
			},
			{
				"ID": 2,
				"Tip": 1,
				"Podatki": "gnsstocke.koo",
				"s1D": 0.02,
				"s2D": 0.02
			},... Druge podatkovne plasti
		]
	}

