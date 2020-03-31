---
title: Ordlista för Azure Maps | Microsoft-dokument
description: En ordlista med vanliga termer som är associerade med Azure Maps, Platsbaserade tjänster och GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: c9d5c2efc14dec5beee0eaf81a4f628421ef3d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657052"
---
# <a name="glossary"></a>Ordlista

I följande lista beskrivs vanliga ord som används med Azure Maps-tjänsterna.

## <a name="a"></a>A

<a name="address-validation"></a>**Adressvalidering**: Processen att verifiera att det finns en adress.

<a name="advanced-routing"></a>**Avancerad routning**: En samling tjänster som utför avancerade åtgärder med hjälp av vägroutningsdata. till exempel beräkning av räckviddsbara intervall (isochroner), avståndsmatriser och batchflödesbegäranden.

<a name="aerial-imagery"></a>**Flygbilder:** Se [satellitbilder.](#satellite-imagery) 

<a name="along-a-route-search"></a>**Längs en flödessökning**: En rumslig fråga som söker efter data inom en angiven omvägstid eller avstånd från en vägsökväg.

<a name="altitude"></a>**Höjd**: Höjden eller den vertikala höjden på en punkt ovanför en referensyta. Höjdmätningar baseras på ett givet referensdatum, till exempel genomsnittlig havsnivå. Se även höjd.

<a name="ambiguous"></a>**Tvetydig :** Ett tillstånd av osäkerhet i dataklassificering som finns när ett objekt på lämpligt sätt kan tilldelas två eller flera värden för ett visst attribut. När till exempel geokodning "CA" returneras två tvetydiga resultat: "Kanada" och "Kalifornien". "CA" är ett land och en statlig kod, för "Kanada" och "Kalifornien", respektive. 

<a name="annotation"></a>**Anteckning :** Text eller grafik som visas på kartan för att ge information till användaren. Anteckningen kan identifiera eller beskriva en viss kartentitet, tillhandahålla allmän information om ett område på kartan eller ange information om själva kartan.

<a name="antimeridian"></a>**Antimeridian**: Även känd som den 180:<sup>e</sup> Meridianen. Detta är den punkt där -180 grader och 180 grader av longitud möts. Vilket är motsatsen till den främsta meridianen på jorden.

<a name="application-programming-interface-api"></a>**Api (Application Programming Interface)**: En specifikation som gör det möjligt för utvecklare att skapa program.

<a name="api-key"></a>**API-nyckel:** Se [autentisering med delad nyckel](#shared-key-authentication).

<a name="area-of-interest-aoi"></a>**Intresseområde (AOI)**: Den utsträckning som används för att definiera ett fokusområde för antingen en karta eller en databasproduktion.

<a name="asset-tracking"></a>**Tillgångsspårning**: Processen att spåra platsen för en tillgång, till exempel en person, ett fordon eller något annat objekt.

<a name="asynchronous-request"></a>**Asynkron begäran**: En HTTP-begäran som öppnar en anslutning och gör en begäran till servern som returnerar en identifierare för den asynkrona begäran och stänger sedan anslutningen. Servern fortsätter att bearbeta begäran och användaren kan kontrollera status med hjälp av identifieraren. När begäran är klar med bearbetningen kan användaren sedan hämta svaret. Den här typen av begäran används för tidskrävande processer.

<a name="autocomplete"></a>**Komplettera automatiskt**: En funktion i ett program som förutsäger resten av ett ord som en användare skriver. 

<a name="autosuggest"></a>**Autosuggest**: En funktion i ett program som förutsäger logiska möjligheter för vad användaren skriver.

<a name="azure-location-based-services-lbs"></a>**Azure Location Based Services (LBS)**: Det tidigare namnet på Azure Maps när det var i förhandsversion.

<a name="azure-active-directory"></a>**Azure Active Directory (Azure AD)**: Azure AD är Microsofts molnbaserade identitets- och åtkomsthanteringstjänst. Azure Maps Azure AD-integrering är för närvarande tillgänglig i förhandsversionen för alla Azure Maps API:er. Azure AD stöder rollbaserad åtkomstkontroll (RBAC) för att möjliggöra finkornig åtkomst till Azure Maps-resurser. Mer information om Azure Maps Azure AD-integrering finns i [Azure Maps och Azure AD](azure-maps-authentication.md) och Hantera [autentisering i Azure Maps](how-to-manage-authentication.md).

<a name="azure-maps-key"></a>**Azure Maps-nyckel:** Se [autentisering med delad nyckel](#shared-key-authentication).

## <a name="b"></a>B

<a name="base-map"></a>**Baskarta**: Den del av ett kartprogram som visar bakgrundsreferensinformation som vägar, landmärken och politiska gränser.

<a name="batch-request"></a>**Batch-begäran:** Processen att kombinera flera begäranden till en enda begäran.

<a name="bearing"></a>**Bäring**: En punkts horisontella riktning i förhållande till en annan punkt. Detta uttrycks som en vinkel i förhållande till norr, från 0 grader till 360 grader i medurs riktning. 

<a name="boundary"></a>**Gräns**: En linje eller polygon som skiljer intilliggande politiska enheter, till exempel länder/regioner, distrikt och egenskaper. En gräns är en linje som kan eller inte kan följa fysiska egenskaper, till exempel floder, berg eller väggar.

<a name="bounds"></a>**Gränser:** Se [markeringsram](#bounding-box).

<a name="bounding-box"></a>**Begränsningsram**: En uppsättning koordinater som används för att representera ett rektangulärt område på kartan. 

## <a name="c"></a>C

<a name="cadastre"></a>**Cadastre**: Ett register över registrerad mark och fastigheter. Se även [Paket.](#parcel)

<a name="camera"></a>**Kamera**: I samband med en interaktiv kartkontroll definierar en kamera kartfältet. Visningsområdet för kameran bestäms baserat på flera kartparametrar: centrum, zoomnivå, tonhöjd, lager. 

<a name="centroid"></a>**Centroid**: Den geometriska mitten av en funktion. Centroid av en linje skulle vara mittpunkten medan centroid av en polygon skulle vara dess centrum av området.

<a name="choropleth-map"></a>**Kolrelethkarta**: En tematisk karta där områdena skuggas i proportion till ett mått på en statistisk variabel. Den här statistiska variabeln visas på kartan. Till exempel färglägga gränsen för varje amerikansk stat baserat på dess relativa befolkning till alla andra stater.

<a name="concave-hull"></a>**Konkavt skrov:** En form som representerar en möjlig konkav geometri som omsluter alla former i den angivna datauppsättningen. Den genererade formen liknar att linda data med plastfolie och sedan värma den, vilket orsakar stora spännvidder mellan punkter till grotta in mot andra datapunkter.

<a name="consumption-model"></a>**Förbrukningsmodell**: Information som definierar den hastighet med vilken ett fordon förbrukar bränsle eller elektricitet. Se även dokumentationen för [förbrukningsmodell](consumption-model.md).

<a name="control"></a>**Kontroll**: En fristående eller återanvändbar komponent som består av ett grafiskt användargränssnitt som definierar en uppsättning beteenden för gränssnittet. En kartkontroll är till exempel i allmänhet den del av användargränssnittet som läser in en interaktiv karta.

<a name="convex-hull"></a>**Konvextrov:** Ett konvext skrov är en form som representerar den minsta konvexa geometrin som omsluter alla former i den angivna datauppsättningen. Den genererade formen liknar att radbryta ett elastiskt band runt datauppsättningen.

<a name="coordinate"></a>**Koordinat**: Består av de longitud- och latitudvärden som används för att representera en plats på en karta.

<a name="coordinate-system"></a>**Koordinatsystem**: Ett referensramverk som används för att definiera positionerna för punkter i rymden i två eller tre dimensioner.

<a name="country-code"></a>**Landskod**: En unik identifierare för ett land/en region baserat på ISO-standarden. ISO2 är en tvåteckenskod för ett land (till exempel USA), som ISO3 representerar en kod med tre tecken (till exempel USA).

<a name="country-subdivision"></a>**Landindelning**: En underavdelning på första nivån i ett land/en region, allmänt känd som en stat eller provins.

<a name="country-secondary-subdivision"></a>**Lands sekundär underavdelning:** En andra nivå underavdelning i ett land/en region, allmänt känd som ett län.

<a name="country-tertiary-subdivision"></a>**Land tertiär indelning:** En underavdelning på tredje nivån i ett land/en region, vanligtvis ett namngivet område, till exempel en församling.

<a name="cross-street"></a>**Cross Street**: En punkt där två eller flera gator skär.

<a name="cylindrical-projection"></a>**Cylindrisk projektion**: En projektion som omvandlar punkter från en sfäroid eller sfär till en tangent- eller separatcylinder. Cylindern skivas sedan uppifrån och ned och plattas till ett plan.

## <a name="d"></a>D

<a name="datum"></a>**Datum**: Referensspecifikationerna för ett mätsystem, ett system med koordinatpositioner på en yta (ett horisontellt datum) eller höjder över eller under en yta (ett vertikalt datum).

<a name="dbf-file"></a>**DBF fil:** En databas filformat som används i kombination med Shapefiles (SHP).

<a name="degree-minutes-seconds-dms"></a>**Gradminuter sekunder (DMS)**: Måttenheten för att beskriva latitud och longitud. En examen är 1/360<sup>th</sup> av en cirkel. En examen är ytterligare uppdelad i 60 minuter, och en minut är uppdelad i 60 sekunder.

<a name="delaunay-triangulation"></a>**Delaunay triangulering**: En teknik för att skapa ett nät av sammanhängande, nonoverlapping trianglar från en datauppsättning av punkter. Varje triangelcirklarcirkel innehåller inga punkter från datauppsättningen i sitt inre.

<a name="demographics"></a>**Demografi**: En befolknings befolknings statistiska egenskaper (t.ex. ålder, födelsetal och inkomst).

<a name="destination"></a>**Destination**: En slutpunkt eller plats där någon reser till.

<a name="digital-elevation-model-dem"></a>**Digital höjdmodell (DEM)**: En datauppsättning av höjdvärden relaterade till en yta, som fångas över ett område med jämna mellanrum med ett gemensamt datum. Dems används vanligtvis för att representera terrängavlastning.

<a name="dijkstra's-algorithm"></a>**Dijkstras algoritm**: En algoritm som undersöker anslutningen av ett nätverk för att hitta den kortaste vägen mellan två punkter.

<a name="distance-matrix"></a>**Avståndsmatris:** En matris som innehåller restids- och avståndsinformation mellan en uppsättning ursprung och destinationer. 

## <a name="e"></a>E

<a name="elevation"></a>**Höjd**: Det vertikala avståndet för en punkt eller ett objekt ovanför eller under en referensyta eller ett referensdatum. I allmänhet är referensytan genomsnittlig havsnivå. Höjd hänvisar i allmänhet till den vertikala höjden på mark.

<a name="envelope"></a>**Kuvert**: Se [markeringsram](#bounding-box).

<a name="extended-postal-code"></a>**Utökat postnummer**: Ett postnummer som kan innehålla ytterligare information. I USA har postnummer till exempel fem siffror. Men ett utökat postnummer, kallat zip+4, innehåller ytterligare fyra siffror. Dessa ytterligare siffror används för att identifiera ett geografiskt segment inom det femsiffriga leveransområdet, till exempel ett kvarter, en grupp lägenheter eller en postbox. Att känna till det geografiska segmentet hjälper till med effektiv postsortering och leverans.

<a name="extent"></a>**Omfattning**: Se [begränsningsram](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Federerad autentisering**: En autentiseringsmetod som gör att en enda inloggnings-/autentiseringsmekanism kan användas i flera webb- och mobilappar. 

<a name="feature"></a>**Funktion**: Ett objekt som kombinerar en geometri med ytterligare metadatainformation. 

<a name="feature-collection"></a>**Funktionssamling:** En samling funktionsobjekt.

<a name="find-along-route"></a>**Sök längs vägen**: En rumslig fråga som söker efter data som ligger inom en angiven omvägstid eller ett visst avstånd från en vägsökväg.

<a name="find-nearby"></a>**Hitta i närheten**: En rumslig fråga som söker en fast rak linje avstånd (som kråkan flyger) från en punkt.

<a name="fleet-management"></a>**Vagnparkshantering**: Hantering av nyttofordon som bilar, lastbilar, fartyg och flygplan. Vagnparkshantering kan omfatta en rad funktioner, såsom fordonsfinansiering, underhåll, telematik (spårning och diagnostik) samt förare, hastighet, bränsle samt hälso- och säkerhetshantering. Fleet Management är en process som används av företag som förlitar sig på transporter i sin verksamhet. Företagen vill minimera riskerna och minska sina totala transport- och personalkostnader, samtidigt som de säkerställer att regeringens lagstiftning följs.

<a name="free-flow-speed"></a>**Fri flödeshastighet**: Den fria flödeshastighet som förväntas under idealiska förhållanden. Vanligtvis hastighetsgränsen.

<a name="free-form-address"></a>**Adress i fri form**: En fullständig adress som representeras som en enda textrad.

<a name="fuzzy-search"></a>**Fuzzy search**: En sökning som sker i en fri form sträng av text som kan vara en adress eller intressepunkt. 

## <a name="g"></a>G

<a name="geocode"></a>**Geokod:** En adress eller plats som har konverterats till en koordinat som kan användas för att visa den platsen på en karta. 

<a name="geocoding"></a>**Geokodning**: Även känd som framåt geokodning, är processen att konvertera adress platsdata till koordinater. 

<a name="geodesic-path"></a>**Geodesisk bana**: Den kortaste banan mellan två punkter på en böjd yta. När den återges på Azure Maps visas den här sökvägen som en böjd linje på grund av Mercator-projektionen.

<a name="geofence"></a>**Geofence**: Ett definierat geografiskt område som kan användas för att utlösa händelser när en enhet kommer in i eller finns i regionen.

<a name="geojson"></a>**GeoJSON**: Är ett vanligt JSON-baserat filformat som används för att lagra geografiska vektordata som punkter, linjer och polygoner. **Azure**Maps använder en utökad version av GeoJSON som [dokumenteras här](extend-geojson.md).

<a name="geometry"></a>**Geometri**: Representerar ett rumsligt objekt, till exempel en punkt, linje eller polygon.

<a name="geometrycollection"></a>**GeometryCollection**: En samling geometriobjekt.

<a name="geopol"></a>**GeoPol**: Refererar till geopolitiskt känsliga data, till exempel omtvistade gränser och ortnamn.

<a name="georeference"></a>**Georeference**: Processen att anpassa geografiska data eller bilder till ett känt koordinatsystem. Den här processen kan bestå av att flytta, rotera, skala eller skeva data.

<a name="georss"></a>**GeoRSS**: Ett XML-tillägg för att lägga till rumsliga data i RSS-feeds.

<a name="gis"></a>**GIS**: En akronym för "Geographic Information System". En vanlig term som används för att beskriva kartindustrin.

<a name="gml"></a>**GML**: Även känd som geografi Markup Språk. Ett XML-filtillägg för lagring av rumsliga data.

<a name="gps"></a>**GPS:** Även känd som Global Positioning System, är ett system av satelliter som används för att bestämma en enhet position på jorden. Satelliterna i omloppsbana sänder signaler som gör att en GPS-mottagare var som helst på jorden kan beräkna sin egen plats genom trilateration.

<a name="gpx"></a>**GPX**: Även känd som GPS eXchange-format, är ett XML-filformat som vanligen skapas från GPS-enheter.  

<a name="great-circle-distance"></a>**Great-circle avstånd:** Det kortaste avståndet mellan två punkter på ytan av en sfär.

<a name="greenwich-mean-time-gmt"></a>**Greenwich Mean Time (GMT)**: Tiden på den främsta meridianen, som går genom Royal Observatory i Greenwich, England.

<a name="guid"></a>**GUID**: En globalt unik identifierare. En sträng som används för att unikt identifiera ett gränssnitt, en klass, ett typbibliotek, en komponentkategori eller en post.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Haversine formel**: En vanlig ekvation som används för att beräkna storcirkelavståndet mellan två punkter på en sfär.

<a name="hd-maps"></a>**HD-kartor:** Även känd som High Definition Maps, består av high fidelity vägnät information såsom körfält markeringar, skyltar och riktningsljus som krävs för autonom körning.

<a name="heading"></a>**Rubrik**: Den riktning något pekar eller är vänd mot. Se även [Lager](#heading).

<a name="heatmap"></a>**Heatmap**: En datavisualisering där en färgomfång representerar poängtätheten i ett visst område. Se också Tematiskt kartlägga.

<a name="hybrid-imagery"></a>**Hybridbilder**: Satellit- eller flygbilder som har vägdata och etiketter överlagrad ovanpå den.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: En akronym för Internet Assigned Numbers Authority. En ideell grupp som övervakar global IP-adressallokering.

<a name="isochrone"></a>**Isochrone**: En isochron definierar det område där någon kan resa inom en viss tid för ett transportsätt i någon riktning från en viss plats. Se även [Räckviddsområde](#reachable-range).

<a name="isodistance"></a>**Isodistance**: Med tanke på en plats, definierar en isochrone det område där någon kan resa inom ett visst avstånd för ett transportsätt i någon riktning. Se även [Räckviddsområde](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: Även känt som Nyckelhålsmarkeringsspråk är ett vanligt XML-filformat för lagring av geografiska vektordata som punkter, linjer och polygoner. 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**: Multispektrala, jord-omloppsbana satelliter som utvecklats av NASA som samlar bilder av mark. Detta bildspråk används i många branscher som jordbruk, skogsbruk och kartografi.

<a name="latitude"></a>**Latitud**: Vinkelavståndet mätt i grader från ekvatorn i nord- eller sydriktning.

<a name="level-of-detail"></a>**Detaljnivå:** Se Zoomnivå.

<a name="lidar"></a>**Lidar**: Akronym för ljusdetektering och räckvidd. En fjärranalysteknik som använder lasrar för att mäta avstånd till reflekterande ytor.

<a name="linear-interpolation"></a>**Linjär interpolation:** Uppskattning av ett okänt värde med hjälp av det linjära avståndet mellan kända värden.

<a name="linestring"></a>**Linjesträngning**: En geometri som används för att representera en linje. Även känd som en polylinje. 

<a name="localization"></a>**Lokalisering**: Stöd för olika språk och kulturer.

<a name="logistics"></a>**Logistik**: Processen att flytta människor, fordon, förnödenheter eller tillgångar på ett samordnat sätt.

<a name="longitude"></a>**Longitud**: Vinkelavståndet mätt i grader från den främsta meridianen i öst- eller västlig riktning.

## <a name="m"></a>M

<a name="map-tile"></a>**Kartpanel:** En rektangulär bild som representerar en partition på en kartarbetsyte. Mer information finns i [dokumentationen för zoomnivåer och panelrutnät](zoom-levels-and-tile-grid.md).

<a name="marker"></a>**Markör:** Kallas även en pin eller kartnål, är en ikon som representerar en punktplats på en karta.

<a name="mercator-projection"></a>**Mercator projektion**: En cylindrisk karta projektion som blev standard kartprojektion för nautiska ändamål på grund av dess förmåga att representera linjer av konstant kurs, känd som rhumb linjer, som raka segment som bevara vinklar med meridianer. Alla plana kartprojektioner förvränger kartans former eller storlekar jämfört med den verkliga layouten på jordens yta. Mercator-projektionen överdriver områden långt från ekvatorn, så att mindre områden ser större ut på kartan när du närmar dig polerna. 

<a name="multilinestring"></a>**MultiLineString**: En geometri som representerar en samling LineString-objekt. 

<a name="multipoint"></a>**MultiPoint**: En geometri som representerar en samling Point-objekt.

<a name="multipolygon"></a>**MultiPolygon**: En geometri som representerar en samling Polygonobjekt. Till exempel, för att visa gränsen för Hawaii, skulle varje ö beskrivas med en polygon. Således skulle gränsen för Hawaii alltså vara en MultiPolygon.

<a name="municipality"></a>**Kommun**: En stad eller stad. 

<a name="municipality-subdivision"></a>**Kommunens underavdelning**: En underavdelning till en kommun, till exempel ett grann- eller lokalt namn som "centrum".

## <a name="n"></a>N

<a name="navigation-bar"></a>**Navigeringsfält:** Den uppsättning kontroller på en karta som används för att justera zoomnivå, tonhöjd, rotation och växla baskartlagret.

<a name="nearby-search"></a>**Närliggande sökning**: En rumslig fråga som söker en fast rak linje avstånd (som kråkan flyger) från en punkt.

<a name="neutral-ground-truth"></a>**Neutral ground truth**: En karta som återger etiketter på det officiella språket i den region den representerar och i lokala skript om sådana finns tillgängliga.

## <a name="o"></a>O

<a name="origin"></a>**Ursprung**: En startpunkt eller plats där en användare befinner sig.

## <a name="p"></a>P

<a name="panning"></a>**Panorering:** Processen att flytta kartan i valfri riktning samtidigt som en konstant zoomnivå bibehålls.

<a name="parcel"></a>**Skifte**: En tomt eller egendom gräns.

<a name="pitch"></a>**Tonhöjd:** Mängden lutning kartan har i förhållande till den vertikala där 0 tittar rakt ner på kartan.

<a name="point"></a>**Punkt**: En geometri som representerar en enda position på kartan. 

<a name="points-of-interest-poi"></a>**Intressanta platser (POI)**: Ett företag, landmärke eller gemensam plats av intresse.

<a name="polygon"></a>**Polygon**: En solid geometri som representerar ett område på en karta. 

<a name="polyline"></a>**Polyline**: En geometri som används för att representera en linje. Kallas även LineString. 

<a name="position"></a>**Position**: En punkts longitud, latitud och höjd (x,y,z-koordinater).

<a name="post-code"></a>**Postnummer**: Se [Postnummer](#postal-code).

<a name="postal-code"></a>**Postnummer**: En serie bokstäver eller siffror, eller båda, i ett visst format. Postnumret används av posttjänsten i ett land/en region för att dela upp geografiska områden i zoner för att förenkla postutdelningen.

<a name="primary-key"></a>**Primärnyckel:** Den första av två prenumerationsnycklar som tillhandahålls för azure maps delad nyckelautentisering. Se [Autentisering med delad nyckel](#shared-key-authentication).

<a name="prime-meridian"></a>**Prime meridian**: En linje av longitud som representerar 0 graders longitud. Generellt minskar longitudvärdena när man färdas i västlig riktning fram till 180 grader och ökar när man färdas i östliga riktningar till -180 grader. 

<a name="prj"></a>**PRJ**: En textfil som ofta medföljer en Shapefile-fil som innehåller information om det planerade koordinatsystemet som datauppsättningen befinner sig i.

<a name="projection"></a>**Projektion**: Ett projicerat koordinatsystem baserat på en kartprojektion som tvärgående Mercator, Albers lika område och Robinson. Dessa ger möjlighet att projicera kartor över jordens sfäriska yta på ett tvådimensionellt kartesiskt koordinatplan. Projekterade koordinatsystem kallas ibland kartprognoser.

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**: Ett bas-4-adressindex för en bricka i ett plattsättningssystem för fyrträd. Mer information finns i Dokumentation [om zoomningsnivåer och panelrutnät](zoom-levels-and-tile-grid.md) för mer information.

<a name="quadtree"></a>**Quadtree**: En datastruktur där varje nod har exakt fyra underordnade. Plattsättningssystemet som används i Azure Maps använder en quadtree-struktur som att varje kartpanel delas upp i fyra subtiler när en användare zoomar in på en nivå.  Mer information finns i Dokumentation [om zoomningsnivåer och panelrutnät](zoom-levels-and-tile-grid.md) för mer information.

<a name="queries-per-second-qps"></a>**Frågor per sekund (QPS)**: Antalet frågor eller begäranden som kan göras till en tjänst eller plattform inom en sekund. 

## <a name="r"></a>R

<a name="radial-search"></a>**Radiell sökning**: En rumslig fråga som söker en fast rak linje avstånd (som kråkan flyger) från en punkt. 

<a name="raster-data"></a>**Rasterdata**: En matris med celler (eller pixlar) ordnad i rader och kolumner (eller ett rutnät) där varje cell innehåller ett värde som representerar information, till exempel temperatur. Rasters inkluderar digitala flygfoton, bilder från satelliter, digitala bilder och skannade kartor.

<a name="raster-layer"></a>**Rasterlager**: Ett panellager som består av rasterbilder.

<a name="reachable-range"></a>**Nåbart område**: Ett räckviddsintervall definierar det område där någon kan resa inom en viss tid eller ett visst avstånd, för ett transportsätt att resa, i valfri riktning från en plats. Se även [Isochrone](#isochrone) och [Isodistance.](#isodistance)

<a name="remote-sensing"></a>**Fjärranalys**: Processen att samla in och tolka sensordata på avstånd.

<a name="rest-service"></a>**REST service**: Förkortningen REST står för Representational State Transfer. En REST-tjänst är en URL-baserad webbtjänst som förlitar sig på grundläggande webbteknik för att kommunicera, de vanligaste metoderna är HTTP GET- och POST-begäranden. Dessa typer av tjänster tenderar att jag mycket snabbare och mindre än traditionella SOAP-baserade tjänster.

<a name="reverse-geocode"></a>**Omvänd geokod:** Processen att ta en koordinat och bestämma den adress som representerar på en karta.

<a name="reproject"></a>**Projekt igen**: Se [Omvandling](#transformation).

<a name="rest-service"></a>**REST-tjänst:** Förkortning för representationstillståndsöverföring. En arkitektur för utbyte av information mellan peer-datorer i en decentraliserad, distribuerad miljö. REST gör det möjligt för program på olika datorer att kommunicera oberoende av ett operativsystem eller en plattform. En tjänst kan skicka en HTTP-begäran (Hypertext Transfer Protocol) till en URL (Uniform Resource Locator) och hämta data.

<a name="route"></a>**Rutt**: En sökväg mellan två eller flera platser, som också kan innehålla ytterligare information, till exempel instruktioner för waypoints längs rutten.

<a name="requests-per-second-rps"></a>**Begäranden per sekund (RPS):** Se [frågor per sekund (QPS)](#queries-per-second-qps). 

<a name="rss"></a>**RSS:** Förkortning för Really Simple Syndication, Resource Description Framework (RDF) Site Summary, eller Rich Site Summary, beroende på källan. Ett enkelt, strukturerat XML-format för delning av innehåll mellan olika webbplatser. RSS-dokument innehåller viktiga metadataelement som författare, datum, titel, en kort beskrivning och en hypertextlänk. Den här informationen hjälper en användare (eller en RSS-utgivare) att avgöra vilket material som är värt ytterligare undersökning.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**Satellitbilder:** Bilder som har fångats av flygplan och satelliter som pekar rakt ner.

<a name="secondary-key"></a>**Sekundär nyckel:** Den andra av två prenumerationsnycklar som tillhandahålls för azure maps delad nyckelautentisering. Se [Autentisering med delad nyckel](#shared-key-authentication).

<a name="shapefile-shp"></a>**Shapefile (SHP):** Även känd som en ESRI Shapefile, är en vektor datalagringsformat för lagring av plats, form och attribut för geografiska funktioner. En formfil lagras i en uppsättning relaterade filer.

<a name="shared-key-authentication"></a>**Autentisering med delad nyckel**: Autentisering med delad nyckel är beroende av att skicka azure maps-kontogenererade nycklar för varje begäran till Azure Maps. Dessa nycklar kallas ofta prenumerationsnycklar. Vi rekommenderar att nycklar regelbundet återskapas för säkerhet. Två nycklar tillhandahålls så att du kan underhålla anslutningar med en nyckel när du återskapar den andra. När du återskapar nycklarna måste du uppdatera alla program som använder det här kontot till att använda de nya nycklarna. Mer information om Azure Maps-autentisering finns i [Azure Maps och Azure AD](azure-maps-authentication.md) och Hantera [autentisering i Azure Maps](how-to-manage-authentication.md).

<a name="software-development-kit-sdk"></a>**Software development kit (SDK):** En samling dokumentation, exempelkod och exempelappar som hjälper en utvecklare att använda ett API för att skapa appar.

<a name="spherical-mercator-projection"></a>**Sfärisk mercatorprojektion**: Se [Web Mercator](#web-mercator). 

<a name="spatial-query"></a>**Spatial fråga**: En begäran till en tjänst som utför en rumslig åtgärd. Till exempel en radiell sökning eller längs en ruttsökning.

<a name="spatial-reference"></a>**Rumslig referens**: Ett koordinatbaserat lokalt, regionalt eller globalt system som används för att exakt lokalisera geografiska enheter. Det definierar det koordinatsystem som används för att relatera kartkoordinater till platser i den verkliga världen. Rumsliga referenser säkerställer att rumsliga data från olika lager eller källor kan integreras för korrekt visning eller analys. Azure Maps använder koordinatreferenssystemet [EPSG:3857](https://epsg.io/3857) och WGS 84 för indatageometridata.

<a name="sql-spatial"></a>**SQL spatial**: Refererar till de rumsliga funktioner som är inbyggda i SQL Azure och SQL Server 2008 och senare. Den här rumsliga funktionen är också tillgänglig som ett .NET-bibliotek som kan användas oberoende av SQL Server. Mer information finns i dokumentationen för [Spatial Data (SQL Server)](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) för mer information.

<a name="subscription-key"></a>**Prenumerationsnyckel:** Se [Autentisering med delad nyckel](#shared-key-authentication).

<a name="synchronous-request"></a>**Synkron begäran**: En HTTP-begäran öppnar en anslutning och väntar på ett svar. Webbläsare begränsar antalet samtidiga HTTP-begäranden som kan göras från en sida. Om flera tidskrävande synkrona begäranden görs samtidigt kan den här gränsen nås. Begäranden kommer att försenas tills en av de andra begärandena har slutförts.

## <a name="t"></a>T

<a name="telematics"></a>**Telematik**: Skicka, ta emot och lagra information via telekommunikationsenheter i samband med effektstyrning på fjärrobjekt. 

<a name="temporal-data"></a>**Tidsmässiga data**: Data som specifikt refererar till tider eller datum. Temporala data kan avse diskreta händelser, såsom blixtnedslag; rörliga föremål, t.ex. eller upprepade observationer, till exempel antal från trafiksensorer.

<a name="terrain"></a>**Terräng:** Ett landområde med en särskild egenskap, såsom sandig terräng eller bergig terräng.

<a name="thematic-maps"></a>**Tematiska kartor**: En tematisk karta är en enkel karta gjord för att återspegla ett tema om ett geografiskt område. Ett vanligt scenario för den här typen av karta är att färga administrativa regioner som länder/regioner baserat på vissa mått på data.

<a name="tile-layer"></a>**Kakellager:** Ett lager som visas genom att sätta ihop kartpaneler (rektangulära avsnitt) i ett kontinuerligt lager. Brickorna är antingen raster bild plattor eller vektor plattor. Rasterpanellager återges vanligtvis i förväg och lagras som bilder på en server. Rasterkakellager kan använda ett stort lagringsutrymme. Vektorpanellager återges i nära realtid i klientprogrammet. Lagringskraven på serversidan är därför mindre för vektorpanellager.

<a name="time-zone"></a>**Tidszon**: En region i världen som observerar en enhetlig standardtid för juridiska, kommersiella och sociala ändamål. Tidszoner tenderar att följa gränserna för länder/regioner och deras underavdelningar.

<a name="transaction"></a>**Transaktion**: Azure Maps använder en transaktionslicensmodell där;

- En transaktion skapas för varje 15-karta eller trafikpaneler som begärs.
- En transaktion skapas för varje API-anrop till en av tjänsterna i Azure Maps. Sökning och routning är exempel på Azure Maps-tjänsten.

<a name="transformation"></a>**Omvandling**: Processen att konvertera data mellan olika geografiska koordinatsystem. Du kan till exempel ha vissa data som har hämtats i Storbritannien och baserat på DET geografiska koordinatsystemet OSGB 1936. Azure Maps använder koordinatreferenssystemvarianten [EPSG:3857](https://epsg.io/3857) av WGS84. Som sådan för att visa data korrekt, måste den ha sina koordinater omvandlas från ett system till ett annat.

<a name="traveling-salesmen-problem-tsp"></a>**Traveling Salesmen Problem (TSP)**: En Hamiltonian krets problem där en säljare måste hitta det mest effektiva sättet att besöka en rad stopp, sedan återvända till startplatsen.  

<a name="trilateration"></a>**Trilateration**: Processen att bestämma positionen för en punkt på jordens yta, med avseende på två andra punkter, genom att mäta avstånden mellan alla tre punkterna.

<a name="turn-by-turn-navigation"></a>**Turn-by-turn navigering**: Ett program som ger ruttinstruktioner för varje steg i en rutt som användarna närmar sig nästa manöver.

## <a name="v"></a>V

<a name="vector-data"></a>**Vektordata**: Koordinatbaserade data som representeras som punkter, linjer eller polygoner.

<a name="vector-tile"></a>**Vektorpanel:** En öppen dataspecifikation för lagring av geospatiala vektordata med samma panelsystem som kartkontrollen. Se även [Kakellager](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a>**Vehicle Routing Problem (VRP)**: En klass av problem, där en uppsättning beställda rutter för en fordonsflotta beräknas samtidigt som hänsyn tas till som en uppsättning begränsningar. Dessa begränsningar kan omfatta leveranstidsfönster, flera ruttkapaciteter och begränsningar för restid.

<a name="voronoi-diagram"></a>**Voronoi diagram**: En partition av utrymme i områden, eller celler, som omger en uppsättning geometriska objekt, vanligtvis punkt funktioner. Dessa celler, eller polygoner, måste uppfylla kriterierna för Delaunay trianglar. Alla platser inom ett område ligger närmare objektet som det omger än något annat objekt i uppsättningen. Voronoi diagram används ofta för att avgränsa områden av inflytande kring geografiska egenskaper. 

## <a name="w"></a>W

<a name="waypoint"></a>**Waypoint**: En waypoint är en angiven geografisk plats som definieras av longitud och latitud som används för navigeringsändamål. Används ofta för att representera en punkt där någon navigerar en rutt genom.

<a name="waypoint-optimization"></a>**Waypoint optimering**: Processen att ändra ordning på en uppsättning waypoints för att minimera restid eller avstånd som krävs för att passera genom alla angivna waypoints. Beroende på optimeringens komplexitet kallas den här optimeringen ofta för [problem med resande säljare](#traveling-salesmen-problem-tsp) eller [fordonsroutningsproblem](#vehicle-routing-problem-vrp).

<a name="web-map-service-wms"></a>**WMS (Web Map Service)**: WMS är en OGC-standard (Open Geographic Consortium) som definierar bildbaserade karttjänster. WMS-tjänster tillhandahåller kartbilder för specifika områden inom en karta på begäran. Bilder innehåller förrenderade symbolik och kan återges i något av flera namngivna format om de definieras av tjänsten.

<a name="web-mercator"></a>**Web Mercator**: Även känd som Sfärisk Mercator projektion. Det är en liten variant av Mercator projektion, en som används främst i webbaserade kartprogram. Den använder samma formler som standard Mercator-projektionen som används för småskaliga kartor. Webb mercator använder dock sfäriska formler i alla skalor, men storskaliga Mercator kartor använder normalt ellipsoidal form av projektionen. Diskrepansen är omärklig på global nivå, men det orsakar kartor över lokala områden att avvika något från sanna ellipsoidal Mercator kartor, i samma skala.

<a name="wgs84"></a>**WGS84**: En uppsättning konstanter som används för att relatera rumsliga koordinater till platser på kartans yta. WGS84-datumet är det standardprogram som används av de flesta onlinekartningsleverantörer och GPS-enheter. Azure Maps använder koordinatreferenssystemvarianten [EPSG:3857](https://epsg.io/3857) av WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Z-koordinat:** Se [Höjd](#altitude). 

<a name="zip-code"></a>**Postnummer**: Se [Postnummer](#postal-code).

<a name="Zoom level"></a>**Zoomnivå**: Anger detaljnivå och hur mycket av kartan som är synlig. När den zoomas hela vägen till nivå 0 kommer den fullständiga världskartan ofta att vara synlig. Men kartan visar begränsade detaljer som lands-/regionnamn, gränser och oceannamn. När den zoomas in närmare nivå 17 kommer kartan att visa ett område med några kvarter med detaljerad väginformation. I azure kartor är den högsta zoomnivån 22. Mer information finns i dokumentationen [för zoomnivåer och panelrutnät.](zoom-levels-and-tile-grid.md)

