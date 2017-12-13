---
title: Azure CDN regler motorn matchar villkoren | Microsoft Docs
description: "I referensdokumentationen för Azure CDN regler motorn matchar villkoren och funktioner."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 0abbcf8508cb95d0fb8a9c8e5243426752efe590
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Azure CDN regelmotor matchar villkoren
Det här avsnittet innehåller detaljerade beskrivningar av tillgängliga matchar villkoren för Azure Content Delivery Network (CDN) [regelmotor](cdn-rules-engine.md).

Den andra delen av en regel är matchar villkoret. En matchar villkoret identifierar vissa typer av begäranden som en uppsättning funktioner kommer att utföras.

Till exempel kan den användas för att filtrera förfrågningar om innehåll på en viss plats begäranden som genereras från en viss IP-adress eller ett land eller av huvudinformation.

## <a name="always"></a>Alltid

Alltid matchar villkoret är avsedd att gäller en standarduppsättning med funktioner för alla begäranden.

## <a name="device"></a>Enhet

Enheten matchar villkoret identifierar begäranden från en mobil enhet baserat på dess egenskaper.  Identifiering av mobila enheter uppnås genom [WURFL](http://wurfl.sourceforge.net/).  WURFL funktioner och deras CDN regelmotor variabler visas nedan.
<br>
> [!NOTE] 
> Variablerna nedan stöds i den **ändra klienten begär huvud** och **ändra klienten svarshuvud** funktioner.

Funktion | Variabel | Beskrivning | Exempel värden
-----------|----------|-------------|----------------
Varumärke | % {wurfl_cap_brand_name} | En sträng som anger namnet på enheten. | Samsung
Enhetens OS | % {wurfl_cap_device_os} | En sträng som anger operativsystemet installerat på enheten. | IOS
Operativsystemsversion för enhet | % {wurfl_cap_device_os_version} | En sträng som anger versionsnumret för Operativsystemet som är installerad på enheten. | 1.0.1
Dubbla orientering | % {wurfl_cap_dual_orientation} | Ett booleskt värde som anger om enheten har stöd för dubbel orientering. | true
HTML önskad DTD | % {wurfl_cap_html_preferred_dtd} | En sträng som anger den mobila enheten prioriterade dokumenttypsdefinition (DTD) för HTML-innehåll. | ingen<br/>xhtml_basic<br/>HTML5
Bild Inlining | % {wurfl_cap_image_inlining} | Ett booleskt värde som anger om enheten stöder Base64-kodade bilder. | false
Är Android | % {wurfl_vcap_is_android} | Ett booleskt värde som anger om enheten använder i Android OS. | true
Är IOS | % {wurfl_vcap_is_ios} | Ett booleskt värde som anger om enheten använder iOS. | false
Är Smart TV | % {wurfl_cap_is_smarttv} | Ett booleskt värde som anger om enheten är en smart TV. | false
Är Smartphone | % {wurfl_vcap_is_smartphone} | Ett booleskt värde som anger om enheten är en smartphone. | true
Är Tablet | % {wurfl_cap_is_tablet} | Ett booleskt värde som anger om enheten är en surfplatta. Det här är en oberoende av OS-beskrivning. | true
Är trådlösa enheter | % {wurfl_cap_is_wireless_device} | Ett booleskt värde som anger om enheten betraktas som en trådlös enhet. | true
Marknadsföring namn | % {wurfl_cap_marketing_name} | En sträng som anger marknadsföring enhetsnamn. | BlackBerry 8100 Pearl
Mobila webbläsare | % {wurfl_cap_mobile_browser} | En sträng som anger den webbläsare som begär innehåll från enheten. | Chrome
Mobila webbläsarversion | % {wurfl_cap_mobile_browser_version} | En sträng som anger versionen av webbläsaren för att begära innehåll från enheten. | 31
Modellnamnet | % {wurfl_cap_model_name} | En sträng som anger enhetens namn. | S3
Progressiv hämtning | % {wurfl_cap_progressive_download} | Ett booleskt värde som anger om enheten har stöd för uppspelning av ljud och video medan den hämtas fortfarande. | true
Utgivningsdatum | % {wurfl_cap_release_date} | En sträng som anger år och månad då enheten har lagts till WURFL-databasen.<br/><br/>Format:`yyyy_mm` | 2013_december
Lösning höjd | % {wurfl_cap_resolution_height} | Ett heltal som anger enhetens höjd i bildpunkter. | 768
Lösning bredd | % {wurfl_cap_resolution_width} | Ett heltal som anger enhetens bredd i bildpunkter. | 1024


## <a name="location"></a>Plats

Dessa villkor är avsedda att identifiera förfrågningar baserat på förfrågarens plats matchning.

Namn | Syfte
-----|--------
SOM tal | Identifierar förfrågningar som kommer från ett visst nätverk.
Land/region | Identifierar förfrågningar som kommer från de angivna länderna.

### <a name="as-number"></a>SOM tal 
Det här nätverket har definierats av dess autonoma numret (ASN: er). Ett alternativ har angetts för att indikera om detta villkor vara uppfyllda när en klient nätverk ”matchar” eller ”har inte matchar” det angivna som tal.

**Viktig Information**
- Ange flera AS nummer genom att avgränsa dem med ett blanksteg. Exempelvis matchar 64514 64515 begäranden som kommer från 64514 eller 64515.
- Vissa begäranden kan inte returnera en giltig som tal. Ett frågetecken (d.v.s.?) kommer att matcha begäranden som en giltig som tal inte kunde fastställas.
- Hela som numret för det önskade nätverket måste anges. Partiella värden kommer inte matchas.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

### <a name="country"></a>Land/region
Ett land kan anges via dess landskod. Ett alternativ som anger om det här villkoret ska vara uppfyllda när det land som en begäran kommer från ”matchar” eller ”har inte matchar” de angivna värdena.


**Viktig Information**
- Ange flera landskoder genom att avgränsa dem med ett blanksteg.
- Jokertecken stöds inte när du anger en landskod.
- Landskoder ”Europa” och ”Asien” inte omfattar alla IP-adresser i dessa regioner.
- Vissa begäranden kan inte returnera en giltig landskod. Ett frågetecken (d.v.s.?) kommer att matcha begäranden som en giltig landskod inte kunde fastställas.
- Landskoder är skiftlägeskänsliga.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

## <a name="origin"></a>Ursprung

Dessa villkor är avsedda att identifiera matcha begäranden som pekar på CDN lagring eller en kund ursprungsservern.

Namn | Syfte
-----|--------
CDN ursprung | Identifierar förfrågningar om innehåll som lagras på CDN lagring.
Kunden ursprung | Identifierar förfrågningar om innehåll som lagras på en viss kund ursprungsservern.

### <a name="cdn-origin"></a>CDN ursprung
Den här matchar villkor när båda av följande villkor är uppfyllda:
- Innehåll från CDN lagring begärdes.
- URI-begäran utnyttjar innehållsåtkomst punkt (t.ex. /000001) som definieras i det här tillståndet för matchning.
  - CDN-URL: URI-begäran måste innehålla valda innehålls åtkomstpunkt.
  - Edge CNAME-URL: Motsvarande kant CNAME konfigurationen måste peka på valda innehålls åtkomstpunkt.
  
*Kommentarer:*
 - Innehålls åtkomstpunkt identifierar den tjänst som ska användas för det begärda innehållet.
 - Ett uttryck och om ska inte användas för att kombinera vissa villkor för matchning. Kombinera ett villkor för matchning av CDN ursprung med en kund ursprung matchar villkoret skulle till exempel skapa ett matchningsmönster som inte kunde matchas. Två CDN ursprung matchar villkor kan inte kombineras via ett uttryck och om samma därför.
 
### <a name="customer-origin"></a>Kunden ursprung

**Viktig Information** 
- Det här matchar villkoret uppfylls oavsett om innehållet begärs använder en CDN- eller en kant CNAME-URL som pekar på det valda kunden ursprunget.
- En kund ursprung konfiguration som refereras av en regel kan inte tas bort från sidan Customer ursprung. Kontrollera att följande konfigurationer inte refererar till den innan du försöker ta bort en kund ursprung konfiguration:
  - Kunden ursprung matchar villkoret
  - En kant CNAME-konfiguration.
- Ett uttryck och om ska inte användas för att kombinera vissa villkor för matchning. Till exempel skapar kombinera en kund ursprung matchar villkoret med ett villkor för matchning av CDN ursprung ett matchningsmönster som inte kunde matchas. Två kunden ursprung matchar villkor kan inte kombineras via ett uttryck och om samma därför.

## <a name="request"></a>Förfrågan

Dessa villkor är avsedda att identifiera förfrågningar baserat på deras egenskaper matchar.

Namn | Syfte
-----|--------
IP-adress för klient | Identifierar förfrågningar som kommer från en viss IP-adress.
Cookie-Parameter | Kontrollerar cookies som är associerade med varje begäran för det angivna värdet.
Cookie-parametern Regex | Kontrollerar cookies som är associerade med varje begäran för det angivna reguljära uttrycket.
Edge Cname | Identifierar begäranden som pekar på en specifik kant CNAME-post.
Refererande domän | Identifierar begäranden som har hänvisats från den angivna hostname(s).
Begäran sidhuvud Literal | Identifierar begäranden som innehåller det angivna huvudet inställt på angivna värden.
Begäran sidhuvud Regex | Identifierar begäranden som innehåller det angivna huvudet inställt på ett värde som matchar det angivna reguljära uttrycket.
Begäran huvud med jokertecken | Identifierar begäranden som innehåller det angivna huvudet inställt på ett värde som matchar det angivna mönstret.
Metod för begäran | Identifierar begäranden via sina HTTP-metoden.
Schemat för begäran | Identifierar begäranden via sina HTTP-protokollet.

### <a name="client-ip-address"></a>IP-adress för klient
Ett alternativ som anger om det här villkoret kommer vara uppfyllda när en klient har IP-adress ”matchar” eller ”har inte matchar” den angivna IP-adress.

**Viktig information:**
- Se till att använda CIDR-notering.
- Ange flera IP-adresser och/eller IP-Adressblock genom att avgränsa dem med ett blanksteg.
  - **IPv4-exempel:** 1.2.3.4 10.20.30.40 matchar alla förfrågningar som kommer från 1.2.3.4 eller 10.20.30.40.
  - **IPv6-exempel:** 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matchar alla förfrågningar som kommer från 1:2:3:4:5:6:7:8 eller 10:20:30:40:50:60:70:80.
- Syntaxen för ett IP-Adressblock är grundläggande IP-adressen följt av ett snedstreck och prefix-storlek.
  - **IPv4-exempel:** 5.5.5.64/26 matchar alla förfrågningar som kommer från 5.5.5.64 via 5.5.5.127.
  - **IPv6-exempel:** 1:2:3: / 48 matchar alla förfrågningar som kommer från 1:2:3:0:0:0:0:0 via 1:2:3:ffff:ffff:ffff:ffff:ffff.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

### <a name="cookie-parameter"></a>Cookie-Parameter
Den **matchar / matchar inte** alternativet avgör de villkor under vilka detta matchar villkoret uppfylls.
- **Matchar:** kräver en förfrågan som innehåller den angivna cookien med ett värde som matchar åtminstone ett av de värden som definieras i det här tillståndet för matchning.
- **Matchar inte:** kräver att begäran uppfyller något av följande kriterier:
  - Den innehåller inte den angivna cookien.
  - Den innehåller den angivna cookien, men dess värde matchar inte någon av de värden som definieras i det här tillståndet för matchning.
  
**Viktig information:**
- **Cookie-namn:** 
  - Specialtecken, inklusive en asterisk stöds inte när du anger ett cookie-namn. Detta innebär att endast exakta cookie-namn matchar är berättigade för jämförelse.
  - Bara ett enda cookie-namn får anges per instans av det här matchar villkoret.
  - Cookie-namn jämförelser är skiftlägeskänsliga.
- **Cookie-värde:** 
  - Ange flera cookie-värden genom att avgränsa dem med ett blanksteg.
  - Ett cookievärde kan dra nytta av specialtecken. 
  - Om ett jokertecken inte har angetts, kommer endast en exakt matchning uppfyller felet matchar. 
   - **Exempel:** anger ”värde” matchar ”värde”, men inte ”Value1” eller ”Value2”.
  - Den **Ignorera skiftläge** alternativet avgör om gemener görs mot en begäran cookie-värde.
  - På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
   - Slutföra Cache Fill
   - Internt Max-åldern som standard
   - Tvinga inre maximal ålder
   - Ignorera ursprung No-Cache
   - Internt Max-inaktuell

### <a name="cookie-parameter-regex"></a>Cookie-parametern Regex
Det här tillståndet matchar definierar ett cookie-namn och värde. Reguljära uttryck kan användas för att definiera önskade cookie-värde. 

Den **matchar / matchar inte** alternativet avgör de villkor under vilka detta matchar villkor vara uppfyllda.
- **Matchar:** kräver en förfrågan som innehåller den angivna cookien med ett värde som matchar det angivna reguljära uttrycket.
- **Matchar inte:** kräver att begäran uppfyller något av följande kriterier:
  - Den innehåller inte den angivna cookien.
  - Den innehåller den angivna cookien, men dess värde matchar inte det angivna reguljära uttrycket.
  
**Viktig information:**
- **Cookie-namn:** 
  - Reguljära uttryck och specialtecken, inklusive en asterisk stöds inte när du anger ett cookie-namn. Detta innebär att endast exakta cookie-namn matchar är berättigade för jämförelse.
  - Bara ett enda cookie-namn får anges per instans av det här matchar villkoret.
  - Cookie-namn jämförelser är skiftlägeskänsliga.
- **Cookie-värde:** 
  - Ett cookievärde kan dra nytta av reguljära uttryck.
  - Den **Ignorera skiftläge** alternativet avgör om gemener görs mot en begäran cookie-värde.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

### <a name="edge-cname"></a>Edge Cname
**Viktig Information** 
- Listan över tillgängliga edge skapa CNAME-poster är begränsade till dem som har konfigurerats på sidan Edge skapa CNAME-poster som motsvarar den plattform som HTTP-regelmotor konfigureras.
- Innan du försöker ta bort en kant CNAME-konfiguration, se till att en kant Cname matchar villkoret refererar inte till den. Edge CNAME konfigurationer som har definierats i en regel kan inte tas bort från sidan Edge skapa CNAME-poster. 
- Ett uttryck och om ska inte användas för att kombinera vissa villkor för matchning. Till exempel skapar kombinera ett Edge Cname matchar villkor med ett villkor för matchning av kunden ursprung ett matchningsmönster som inte kunde matchas. Två Edge Cname matchar villkor kan inte kombineras via ett uttryck och om samma därför.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

### <a name="referring-domain"></a>Refererande domän
Värdnamnet är associerade med referent som begärdes innehåll avgör om det här villkoret är uppfyllt. Ett alternativ som anger om det här villkoret kommer vara uppfyllda när refererande värdnamnet ”matchar” eller ”matchar inte” de angivna värdena.
**Viktig information:**
- Ange flera värdnamn genom att avgränsa dem med ett blanksteg.
- Det här matchar villkoret har stöd för specialtecken.
- Om det angivna värdet inte innehåller en asterisk måste den vara en exakt matchning för den referent värdnamn. Till exempel matchar ange ”mydomain.com” inte ”www.mydomain.com”.
- Alternativet Ignorera skiftläge anger om gemener kommer att utföras.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell
  
 ### <a name="request-header-literal"></a>Begäran sidhuvud Literal
Den **matchar / matchar inte** alternativet avgör de villkor under vilka detta matchar villkoret uppfylls.
- **Matchar:** kräver begäranden som innehåller det angivna huvudet och dess värde måste matcha den som angetts i det här tillståndet för matchning.
- **Matchar inte:** kräver att begäran uppfyller något av följande kriterier:
  - Det innehåller inte det angivna huvudet.
  - Det innehåller det angivna huvudet, men dess värde matchar inte den som angetts i det här tillståndet för matchning.
  
**Viktig information:**
- Huvudet namn jämförelser är alltid skiftlägeskänsliga. Skiftlägeskänslighet för huvudet värdet jämförelser bestäms av alternativet Ignorera skiftläge.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell
  
### <a name="request-header-regex"></a>Begäran sidhuvud Regex
**Obs:** den här funktionen kräver regelmotor - avancerade regler som måste köpas separat. Kontakta kontoansvarig CDN för att aktivera den. 

Den **matchar / matchar inte** alternativet avgör de villkor under vilka detta matchar villkoret uppfylls.
- **Matchar:** kräver begäranden som innehåller det angivna huvudet och dess värde måste matcha det mönster som definierats i det angivna reguljära uttrycket.
- **Matchar inte:** kräver att begäran uppfyller något av följande kriterier:
  - Det innehåller inte det angivna huvudet.
  - Det innehåller det angivna huvudet, men dess värde matchar inte det angivna reguljära uttrycket.

**Viktig information:**
- Huvudnamn: 
  - Huvudet namn jämförelser är skiftlägeskänsliga.
  - Blanksteg i huvudnamnet som ska ersättas med ”% 20”. 
- Huvudets värde: 
  - Ett huvudvärde kan dra nytta av reguljära uttryck.
  - Skiftlägeskänslighet för huvudet värdet jämförelser bestäms av alternativet Ignorera skiftläge.
  - Endast exakt sidhuvud matchar till minst en av de angivna mönster ska uppfylla det här villkoret.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell 

### <a name="request-header-wildcard"></a>Begäran huvud med jokertecken
Den **matchar / matchar inte** alternativet avgör de villkor under vilka detta matchar villkoret uppfylls.
- **Matchar:** kräver begäranden som innehåller det angivna huvudet och dess värde måste matcha minst en av de värden som definieras i det här tillståndet för matchning.
- **Matchar inte:** kräver att begäran uppfyller något av följande kriterier:
  - Det innehåller inte det angivna huvudet.
  - Det innehåller det angivna huvudet, men dess värde matchar inte någon av de angivna värdena.
  
**Viktig information:**
- Huvudnamn: 
  - Huvudet namn jämförelser är skiftlägeskänsliga.
  - Blanksteg i huvudnamnet som ska ersättas med ”% 20”. Det här värdet kan även användas för att ange blanksteg i ett huvudvärde.
- Huvudets värde: 
  - Ett huvudvärde kan dra nytta av specialtecken.
  - Skiftlägeskänslighet för huvudet värdet jämförelser bestäms av alternativet Ignorera skiftläge.
  - Endast exakt sidhuvud matchar till minst en av de angivna mönster ska uppfylla det här villkoret.
  - Ange flera värden genom att avgränsa dem med ett blanksteg.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

### <a name="request-method"></a>Metod för begäran
Endast tillgångar som har begärts med metoden begäran som valts kommer att uppfylla det här villkoret. Begäran om tillgängliga metoderna är:
- HÄMTA
- HUVUDET 
- POST 
- ALTERNATIV 
- PLACERA 
- TA BORT 
- SPÅRNING 
- ANSLUTA 

**Viktig information:**
- Som standard kan endast GET-begäran-metoden generera cachelagrat innehåll i vårt nätverk. Alla övriga metodbegäranden är helt enkelt via proxy via nätverket.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

### <a name="request-scheme"></a>Schemat för begäran
Endast tillgångar som har begärts med det valda protokollet ska uppfylla det här villkoret. De tillgängliga protokollen är HTTP och HTTPS.

**Viktig information:**
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

## <a name="url"></a>Webbadress

Dessa villkor är avsedda att identifiera förfrågningar baserat på deras webbadresser matchning.

Namn | Syfte
-----|--------
URL-sökväg-katalog | Identifierar begäranden via deras relativa sökvägen.
URL-sökväg-tillägget | Identifierar begäranden efter deras filnamnstillägg.
URL-sökväg filnamn | Identifierar begäranden av filnamnet.
URL-sökväg Literal | Jämför en begäran relativa sökvägen till det angivna värdet.
URL-sökväg Regex | Jämför en begäran relativa sökvägen till det angivna reguljära uttrycket.
URL-sökväg med jokertecken | Jämför en begäran relativa sökvägen till det angivna mönstret.
URL-frågan Literal | Jämför frågesträngen för en begäran till det angivna värdet.
Frågeparametern för URL | Identifierar begäranden som innehåller den angivna frågesträngparametern ett värde som matchar ett specifikt mönster.
URL-frågan Regex | Identifierar begäranden som innehåller angivna frågesträngparametern ett värde som matchar angivna reguljära uttrycket.
URL: en fråga med jokertecken | Jämför de angivna värdena mot frågesträngen i begäran.


## <a name="next-steps"></a>Nästa steg
* [Azure CDN-översikt](cdn-overview.md)
* [Regler modulreferens](cdn-rules-engine-reference.md)
* [Regler motorn villkorsuttryck](cdn-rules-engine-reference-conditional-expressions.md)
* [Regler motorn funktioner](cdn-rules-engine-reference-features.md)
* [Åsidosätta standardbeteendet i HTTP-motorn regler](cdn-rules-engine.md)

