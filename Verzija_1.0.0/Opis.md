
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
- GeodetskiNacrt:
	- UporabniskiSloji (Sloji, ki jih definira uporabnik):
		- ID (ID Sloja, ki se glede na specifikacijo začne z 901, 902,..90n)
		- Barva [[hexcolor](https://en.wikipedia.org/wiki/Web_colors)] (Barva sloja v HEX zapisu)
		- Opis (Opis sloja)
	- GeoPodatki (Geodetski načrt po specifikaciji formata [GeoJSON](https://geojson.org/)):
		- Element (Točka/Linija/Poligon):
		- TipID [number/null] (ID tipa elementa glede na [Topografski Ključ](http://fgg-web.fgg.uni-lj.si/~/mkuhar/Pouk/DetIzmera/Topografski_kljuc_maj_2006.pdf) ali eTopografskiKljuč)
		- SlojID [[enum number](#Seznam-slojev)] (ID sloja)
- SeznamTock (Informacije o točkah po specifikaciji formata [GeoJSON](https://geojson.org/)):
	- Točka:
		- r1D [[enum number](#Seznam-podatkov)] (referenca na ID podatkovnega vira za višino)
		- r2D [[enum number](#Seznam-podatkov)] (referenca na ID podatkovnega vira za 2D položaj)
		- (Opcijsko) s1D [m] (sigma 1D)
		- (Opcijsko) s2D [m] (sigma 2D)
- ProstorskiPodatki:
	- ID [Number] (ID podatkovnega vira)
	- Opis [text] (Opis podatkovnega vira)
	- Datum [datetime `'YYYYMMDD'` [ISO8601](https://en.wikipedia.org/wiki/ISO_8601)] (Datum podatkovnega vira)
	- Tip [[enum number](#Seznam-podatkov)]:
	- Podatki [text/null] (referenca na datoteko/URL)
	- s1D [m/null] (natančnost 1D oz. null, kadar ni podatka o natančnosti)
	- s2D [m/null] (natančnost 2D oz. null, kadar ni podatka o natančnosti)


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
		0 - Neznano
		1 - Vodovod
		2 - Kanalizacija
		3 - Elektrika
		4 - Javna razsvetljava
		5 - Elektronske komunikacije
	90 - Ostalo:
		1..n - Ostalo (poljubni, uporabniško definirani sloji)
		

### Seznam podatkov:

	1 - Statični podatki:
		1 - DOF GURS (2023)
		2 - Lidar GURS (2015)
		
	9 - Uporabniški podatki:
		1..n (ID Podatkovnega sloja):
	
	Tipi uporabniških podatkov:
		1 - Točke (*.koo / *.txt)
		2 - Oblak Točk (*.las / *.laz / *.e57)
		3 - Ortofoto (*.tif + *.tfw / *.geoTiff)
		4 - DMR (*.tif + *.tfw / *.geoTiff)
		5 - Mesh (*.obj)

## Shematski primer:

Shema prikazuje enostaven primer na podlagi katerega izdelamo geodetski načrt po standardu "eGeodetskiNačrt".

<img src="./Shema.svg" width="800" height="800">

Na shemi vidimo, da imamo:
- Cesto (označena z rumeno barvo)
- Ležeči policaj (označeno z rjavo barvo)
- Vodovod (označeno z modro barvo), ki smo ga prejeli od upravljalca
- Dva vodovodna jaška in en neznan jašek
- Dva objekta
- Živo mejo med objektoma (označeno z zeleno)
- Vozlišča (vertex-e), ki predstavljajo izmerjene točke (označene z rdečo barvo)

Shemo smo dopolnili z besedili, ki nam pomagajo pri razumevanju standarda. Na podlagi sheme smo v datoteko zapisali:
- V razdelek "GeodetskiNacrt -> UporabniskiSloji" smo dodali še uporabniški sloj, ki ga je zahteval naročnik:
	- { "ID": 901, "Barva": "#ff7f00", "Opis": "Ležeči policaj" }

- V razdelek "ProstorskiPodatki" smo dodali štiri lastne podatkovne vire:
	- { "ID": 91, "Opis": "Lasten DOF" , "Tip": 3, "Datum": "20210809", Podatki": "http://spletnastran.si/DOF.tif" , "s1D": null, "s2D": 0.02 }
	- { "ID": 92, "Opis": "Lasten SCAN", "Tip": 2, "Datum": "20210809", Podatki": "http://spletnastran.si/SCAN.las", "s1D": 0.02, "s2D": 0.02 }
	- { "ID": 93, "Opis": "Lasten GPS" , "Tip": 1, "Datum": "20210809", Podatki": "GPS.koo"                        , "s1D": 0.04, "s2D": 0.02 }
	- { "ID": 94, "Opis": "VOKA-SNAGA" , "Tip": 1, "Datum": "20210810", Podatki": "VOKA.koo"                       , "s1D": null, "s2D": null }
	
- V razdelek "SeznamTock" smo dodali vsa vozlišča z naslednjimi atributi:
	- r1D (referenco podatkovnega vira za 1D komponento)
	- r2D (referenco podatkovnega vira za 2D komponento)
	- (opcijsko) s1D (natančnost 1D komponente)
	- (opcijsko) s2D (natančnost 2D komponente)

- V razdelek "GeodetskiNacrt -> GeoPodatki" smo dodali vse geometrijske elemente (Točke/Linije/Poligoni) z naslednjimi atributi:
	- SlojID (referenca na sloj)
	- TipID (referenca na TopografskiKljuč)

- V razdelek "Certifikat" smo vpisali podatke o geodetskem načrtu

Dateko smo shranili kot "PrimerGeodetskegaNacrta.json" in jo zapakirali v "ZIP" datoteko v katero smo vljučili še datoteko "GPS.koo", ki smo jo v razdelku "ProstorskiPodatki" navedli kot uporabljen podatkovni vir. S tem je geodetski načrt izdelan po standardu eGeodetskiNačrt.

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
			"UporabniskiSloji":
			[
				{ "ID": 901, "Barva": "#FF00FF", "Opis": "Frontne Mere" },
				{ "ID": 902, "Barva": "#FF0012", "Opis": "Detajlni posnetek mostu" }
			],
			"GeoPodatki":
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
							"SlojID": 901    -> Pločnik
						}
					},... Drugi elementi
				]
			}
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
						"r1D": 2,
						"r2D": 2
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
						"r1D": 1,
						"r2D": 2,
						"s1D": 0.01,
						"s2D": 0.01
					}
				},... Druge točke
			]
		},
		"ProstorskiPodatki"
		[
			{
				"ID": 91,
				"Opis": "Skeniranje z RTC360",
				"Tip": 3,
				"Datum": "20210809",
				"Podatki": "http://spletnastran.si/oblaktock.las",
				"s1D": 0.02,
				"s2D": 0.02
			},
			{
				"ID": 92,
				"Opis": "Točke posnete z Leica GPS1200",
				"Tip": 1,
				"Datum": "20210105",
				"Podatki": "gnsstocke.koo",
				"s1D": 0.02,
				"s2D": 0.02
			},... Druge podatkovne plasti
		]
	}

