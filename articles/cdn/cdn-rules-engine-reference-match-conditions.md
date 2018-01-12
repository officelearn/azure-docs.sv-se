---
title: "Matchar villkoren för Azure CDN regelmotor | Microsoft Docs"
description: "I referensdokumentationen för Azure Content Delivery Network regler motorn matchar villkoren."
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
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: 9986e654b076df099e3912f9da628728723b5c3d
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
# <a name="match-conditions-for-the-azure-cdn-rules-engine"></a>Matchar villkoren för Azure CDN regelmotor
Den här artikeln innehåller detaljerade beskrivningar av tillgängliga matchar villkoren för Azure Content Delivery Network (CDN) [regelmotor](cdn-rules-engine.md).

Den andra delen av en regel är matchar villkoret. En matchar villkoret identifierar vissa typer av begäranden som en uppsättning funktioner kommer att utföras.

Du kan till exempel använda matchar villkoret att:
- Filtrera begäranden för innehåll på en viss plats.
- Filtrera begäranden som genereras från en viss IP-adress eller ett land.
- Filtrera förfrågningar efter huvudinformation.

## <a name="always-match-condition"></a>Matcha villkoret

Alltid matchar villkoret gäller en standarduppsättning med funktioner för alla begäranden.

Namn | Syfte
-----|--------
[Alltid](#always) | Gäller en standarduppsättning med funktioner för alla begäranden.

## <a name="device-match-condition"></a>Enheten matchar villkoret

Enheten matchar villkoret identifierar begäranden från en mobil enhet baserat på dess egenskaper.  

Namn | Syfte
-----|--------
[Enheten](#device) | Identifierar begäranden från en mobil enhet baserat på dess egenskaper.

## <a name="location-match-conditions"></a>Plats matchar villkor

Plats matchar villkoren identifiera förfrågningar baserat på förfrågarens plats.

Namn | Syfte
-----|--------
[SOM tal](#as-number) | Identifierar förfrågningar som kommer från ett visst nätverk.
[Land](#country) | Identifierar förfrågningar som kommer från de angivna länderna.

## <a name="origin-match-conditions"></a>Villkor för matchning av ursprung

Ursprung matchar villkoren identifiera begäranden som pekar på innehållsleveransnätverk lagring eller en kund ursprungsservern.

Namn | Syfte
-----|--------
[CDN ursprung](#cdn-origin) | Identifierar förfrågningar om innehåll som lagras i innehållet Delivery Network storage.
[Kunden ursprung](#customer-origin) | Identifierar förfrågningar om innehåll som lagras på en viss kund ursprungsservern.

## <a name="request-match-conditions"></a>Begäran matchar villkor

Begäran matchar villkoren identifiera förfrågningar baserat på deras egenskaper.

Namn | Syfte
-----|--------
[Klientens IP-adress](#client-ip-address) | Identifierar förfrågningar som kommer från en viss IP-adress.
[Cookie-Parameter](#cookie-parameter) | Kontrollerar cookies som är associerade med varje begäran för det angivna värdet.
[Cookie-parametern Regex](#cookie-parameter-regex) | Kontrollerar cookies som är associerade med varje begäran för det angivna reguljära uttrycket.
[Edge Cname](#edge-cname) | Identifierar begäranden som pekar på en specifik kant CNAME-post.
[Refererande domän](#referring-domain) | Identifierar begäranden som har hänvisats från de angivna värdnamn.
[Begäran sidhuvud Literal](#request-header-literal) | Identifierar begäranden som innehåller det angivna huvudet inställt på ett angivet värde.
[Begäran sidhuvud Regex](#request-header-regex) | Identifierar begäranden som innehåller det angivna huvudet inställt på ett värde som matchar det angivna reguljära uttrycket.
[Begäran huvud med jokertecken](#request-header-wildcard) | Identifierar begäranden som innehåller det angivna huvudet inställt på ett värde som matchar det angivna mönstret.
[Metod för begäran](#request-method) | Identifierar begäranden via sina HTTP-metoden.
[Schemat för begäran](#request-scheme) | Identifierar begäranden via sina HTTP-protokollet.

## <a name="url-match-conditions"></a>URL: en matchar villkor

URL: en matchar villkoren identifiera förfrågningar baserat på deras URL: er.

Namn | Syfte
-----|--------
URL-sökväg-katalog | Identifierar begäranden via deras relativa sökvägen.
URL-sökväg-tillägget | Identifierar begäranden via deras filnamnstillägg.
URL-sökväg filnamn | Identifierar begäranden av filnamn.
URL-sökväg Literal | Jämför en begäran relativa sökvägen till det angivna värdet.
URL-sökväg Regex | Jämför en begäran relativa sökvägen till det angivna reguljära uttrycket.
URL-sökväg med jokertecken | Jämför en begäran relativa sökvägen till det angivna mönstret.
URL-frågan Literal | Jämför frågesträngen för en begäran till det angivna värdet.
Frågeparametern för URL | Identifierar begäranden som innehåller den angivna frågesträngparametern ett värde som matchar ett specifikt mönster.
URL-frågan Regex | Identifierar begäranden som innehåller angivna frågesträngparametern ett värde som matchar angivna reguljära uttrycket.
URL: en fråga med jokertecken | Jämför det angivna värdet mot frågesträngen i begäran.


## <a name="reference-for-rules-engine-match-conditions"></a>Referens för motorn matchar de villkor som regler

---
### <a name="always"></a>Alltid

Alltid matchar villkoret gäller en standarduppsättning med funktioner för alla begäranden.

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>SOM tal 
Nätverkets AS-numret definieras av dess autonoma systemnummer (ASN). Ett alternativ som anger om det här villkoret kommer vara uppfyllda när en klient nätverk ”matchar” eller ”har inte matchar” angivna ASN.

Viktig information:
- Ange flera ASN: er genom att avgränsa dem med ett blanksteg. Exempelvis 64514 64515 matchar begäranden som tas emot från 64514 eller 64515.
- Vissa begäranden kan inte returnera ett giltigt ASN-NUMRET. Ett frågetecken (?) kommer att matcha begäranden som giltigt ASN-NUMRET inte kunde fastställas.
- Du måste ange hela ASN för det önskade nätverket. Partiella värden kommer inte matchas.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cdn-origin"></a>CDN ursprung
CDN ursprung matchar villkoret är uppfyllt när båda av följande villkor är uppfyllda:
- Innehåll från innehållsleveransnätverk lagring begärdes.
- Begäran URI använder innehåll åtkomstpunkt (till exempel /000001) som har definierats i det här tillståndet för matchning.
  - Content Delivery nätverk URL: URI-begäran måste innehålla valda innehålls åtkomstpunkt.
  - Edge CNAME-URL: Motsvarande kant CNAME konfigurationen måste peka på valda innehålls åtkomstpunkt.
  
Viktig information:
 - Innehålls åtkomstpunkt identifierar den tjänst som ska användas för det begärda innehållet.
 - Använd ett uttryck och om för att kombinera vissa villkor matchar inte. Kombinera ett villkor för matchning av CDN ursprung med en kund ursprung matchar villkoret skulle till exempel skapa ett matchningsmönster som inte kunde matchas. Därför kan inte två CDN ursprung matchar villkor kombineras via ett uttryck och om.

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>Klientens IP-adress
Ett alternativ som anger om klientens IP-adress villkor vara uppfyllda när en klients IP-adress ”matchar” eller ”har inte matchar” det angivna IP-adresser.

Viktig information:
- Se till att använda CIDR-notering.
- Ange flera IP-adresser och/eller IP-Adressblock genom att avgränsa dem med ett blanksteg.
  - **IPv4-exempel**: 1.2.3.4 10.20.30.40 matchar alla begäranden som tas emot från 1.2.3.4 eller 10.20.30.40.
  - **IPv6-exempel**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matchar alla begäranden som tas emot från 1:2:3:4:5:6:7:8 eller 10:20:30:40:50:60:70:80.
- Syntaxen för ett IP-Adressblock är grundläggande IP-adressen följt av ett snedstreck och prefix-storlek.
  - **IPv4-exempel**: 5.5.5.64/26 matchar alla begäranden som tas emot från 5.5.5.64 via 5.5.5.127.
  - **IPv6-exempel**: 1:2:3: / 48 matchar alla begäranden som tas emot från 1:2:3:0:0:0:0:0 via 1:2:3:ffff:ffff:ffff:ffff:ffff.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter"></a>Cookie-Parameter
Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka Cookie-parametern matchar villkoret uppfylls.
- **Matchar**: kräver en förfrågan som innehåller den angivna cookien med ett värde som matchar åtminstone ett av de värden som definieras i det här tillståndet för matchning.
- **Matchar inte**: kräver att begäran uppfyller något av följande kriterier:
  - Den innehåller inte den angivna cookien.
  - Den innehåller den angivna cookien, men dess värde matchar inte någon av de värden som definieras i det här tillståndet för matchning.
  
Viktig information:
- Cookie-namn: 
  - Specialtecken, inklusive en asterisk stöds inte när du anger ett cookie-namn. Detta innebär att endast exakta cookie-namn matchar är berättigade för jämförelse.
  - Bara ett enda cookie-namn kan anges per instans av det här matchar villkoret.
  - Cookie-namn jämförelser är skiftlägeskänsliga.
- Cookie-värde: 
  - Ange flera cookie-värden genom att avgränsa dem med ett blanksteg.
  - Ett cookievärde kan dra nytta av specialtecken. 
  - Om ett jokertecken inte har angetts, kommer endast en exakt matchning uppfyller det här matchar villkoret. Till exempel matchar ange ”värde” ”värde”, men inte ”Value1” eller ”Value2”.
  - Den **Ignorera skiftläge** alternativet avgör om gemener görs mot en begäran cookie-värde.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter-regex"></a>Cookie-parametern Regex
Cookie-parametern Regex matchar villkoret definierar ett cookie-namn och värde. Du kan använda reguljära uttryck för att definiera önskade cookie-värde. 

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka detta matchar villkoret uppfylls.
- **Matchar**: kräver en förfrågan som innehåller den angivna cookien med ett värde som matchar det angivna reguljära uttrycket.
- **Matchar inte**: kräver att begäran uppfyller något av följande kriterier:
  - Den innehåller inte den angivna cookien.
  - Den innehåller den angivna cookien, men dess värde matchar inte det angivna reguljära uttrycket.
  
Viktig information:
- Cookie-namn: 
  - Reguljära uttryck och specialtecken, inklusive en asterisk stöds inte när du anger ett cookie-namn. Detta innebär att endast exakta cookie-namn matchar är berättigade för jämförelse.
  - Bara ett enda cookie-namn kan anges per instans av det här matchar villkoret.
  - Cookie-namn jämförelser är skiftlägeskänsliga.
- Cookie-värde: 
  - Ett cookievärde kan dra nytta av reguljära uttryck.
  - Den **Ignorera skiftläge** alternativet avgör om gemener görs mot en begäran cookie-värde.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

--- 
### <a name="country"></a>Land/region
Du kan ange ett land via dess landskod. Ett alternativ som anger om det här villkoret ska vara uppfyllda när det land som en begäran kommer från ”matchar” eller ”har inte matchar” de angivna värdena.

Viktig information:
- Ange flera landskoder genom att avgränsa dem med ett blanksteg.
- Jokertecken stöds inte när du anger en landskod.
- Landskoder ”Europa” och ”Asien” inte omfattar alla IP-adresser i dessa regioner.
- Vissa begäranden kan inte returnera en giltig landskod. Ett frågetecken (?) kommer att matcha begäranden som en giltig landskod inte kunde fastställas.
- Landskoder är skiftlägeskänsliga.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>Kunden ursprung

Viktig information: 
- Kunden ursprung matchar villkoret uppfylls oavsett om innehållet begärs via en Webbadress för leverans av innehåll eller en kant CNAME-URL som pekar på det valda kunden ursprunget.
- En konfiguration av customer ursprung som refereras av en regel kan inte tas bort från sidan Customer ursprung. Kontrollera att följande konfigurationer inte refererar till den innan du försöker ta bort en konfiguration av customer ursprung:
  - En kund ursprung matchar villkoret
  - En kant CNAME-konfiguration
- Använd ett uttryck och om för att kombinera vissa villkor matchar inte. Till exempel skapar kombinera en kund ursprung matchar villkoret med ett villkor för matchning av CDN ursprung ett matchningsmönster som inte kunde matchas. Därför kan inte två kunden ursprung matchar villkor kombineras via ett uttryck och om.

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>Enhet

Enheten matchar villkoret identifierar begäranden från en mobil enhet baserat på dess egenskaper. Identifiering av mobila enheter uppnås genom [WURFL](http://wurfl.sourceforge.net/). I följande tabell visas WURFL funktioner och deras variabler för innehållsleveransnätverk regelmotor.
<br>
> [!NOTE] 
> Följande variabler stöds i den **ändra klienten begär huvud** och **ändra klienten svarshuvud** funktioner.

Funktion | Variabel | Beskrivning | Exempelvärden
-----------|----------|-------------|----------------
Varumärke | % {wurfl_cap_brand_name} | En sträng som anger namnet på enheten. | Samsung
Enhetens OS | % {wurfl_cap_device_os} | En sträng som anger operativsystemet installerat på enheten. | IOS
Operativsystemsversion för enhet | % {wurfl_cap_device_os_version} | En sträng som anger versionsnumret för operativsystemet installerat på enheten. | 1.0.1
Dubbla orientering | % {wurfl_cap_dual_orientation} | Ett booleskt värde som anger om enheten har stöd för dubbel orientering. | sant
HTML önskad DTD | % {wurfl_cap_html_preferred_dtd} | En sträng som anger den mobila enheten prioriterade dokumenttypsdefinition (DTD) för HTML-innehåll. | ingen<br/>xhtml_basic<br/>HTML5
Bild Inlining | % {wurfl_cap_image_inlining} | Ett booleskt värde som anger om enheten stöder Base64-kodade bilder. | falskt
Är Android | % {wurfl_vcap_is_android} | Ett booleskt värde som anger om enheten använder i Android OS. | sant
Är IOS | % {wurfl_vcap_is_ios} | Ett booleskt värde som anger om enheten använder iOS. | falskt
Är Smart TV | % {wurfl_cap_is_smarttv} | Ett booleskt värde som anger om enheten är en smart TV. | falskt
Är Smartphone | % {wurfl_vcap_is_smartphone} | Ett booleskt värde som anger om enheten är en smartphone. | sant
Är Tablet | % {wurfl_cap_is_tablet} | Ett booleskt värde som anger om enheten är en surfplatta. Det här är en oberoende av OS-beskrivning. | sant
Är trådlösa enheter | % {wurfl_cap_is_wireless_device} | Ett booleskt värde som anger om enheten betraktas som en trådlös enhet. | sant
Marknadsföring namn | % {wurfl_cap_marketing_name} | En sträng som anger marknadsföring enhetsnamn. | BlackBerry 8100 Pearl
Mobila webbläsare | % {wurfl_cap_mobile_browser} | En sträng som anger den webbläsare som används för att begära innehåll från enheten. | Chrome
Mobila webbläsarversion | % {wurfl_cap_mobile_browser_version} | En sträng som anger versionen av webbläsaren som används för att begära innehåll från enheten. | 31
Modellnamnet | % {wurfl_cap_model_name} | En sträng som anger enhetens namn. | S3
Progressiv hämtning | % {wurfl_cap_progressive_download} | Ett booleskt värde som anger om enheten har stöd för uppspelning av ljud och video medan den hämtas fortfarande. | sant
Utgivningsdatum | % {wurfl_cap_release_date} | En sträng som anger år och månad då enheten har lagts till WURFL-databasen.<br/><br/>Format:`yyyy_mm` | 2013_december
Lösning höjd | % {wurfl_cap_resolution_height} | Ett heltal som anger enhetens höjd i bildpunkter. | 768
Lösning bredd | % {wurfl_cap_resolution_width} | Ett heltal som anger enhetens bredd i bildpunkter. | 1024

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="edge-cname"></a>Edge Cname
Viktig information: 
- Listan över tillgängliga edge skapa CNAME-poster är begränsade till dem som har konfigurerats på sidan Edge skapa CNAME-poster som motsvarar den plattform som HTTP-regelmotor konfigureras.
- Kontrollera att en kant Cname matchar villkoret inte refererar till den innan du försöker ta bort en kant CNAME-konfiguration. Edge CNAME konfigurationer som har definierats i en regel kan inte tas bort från sidan Edge skapa CNAME-poster. 
- Använd ett uttryck och om för att kombinera vissa villkor matchar inte. Till exempel skapar kombinera ett Edge Cname matchar villkor med ett villkor för matchning av kunden ursprung ett matchningsmönster som inte kunde matchas. Därför kan inte två Edge Cname matchar villkor kombineras via ett uttryck och om.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="referring-domain"></a>Refererande domän
Värdnamnet är associerade med referent som begärdes innehåll avgör om den refererande domän är uppfyllt. Ett alternativ som anger om det här villkoret kommer vara uppfyllda när refererande värdnamnet ”matchar” eller ”har inte matchar” de angivna värdena.

Viktig information:
- Ange flera värden genom att avgränsa dem med ett blanksteg.
- Det här matchar villkoret har stöd för specialtecken.
- Om det angivna värdet inte innehåller en asterisk, måste den vara en exakt matchning för den referent värdnamn. Till exempel matchar ange ”mydomain.com” inte ”www.mydomain.com”.
- Den **Ignorera skiftläge** alternativet avgör om gemener kommer att utföras.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-literal"></a>Begäran sidhuvud Literal
Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka detta matchar villkoret uppfylls.
- **Matchar**: kräver begäranden som innehåller det angivna huvudet. Värdet måste matcha det som definieras i det här tillståndet för matchning.
- **Matchar inte**: kräver att begäran uppfyller något av följande kriterier:
  - Det innehåller inte det angivna huvudet.
  - Det innehåller det angivna huvudet, men dess värde matchar inte det som definieras i det här tillståndet för matchning.
  
Viktig information:
- Huvudet namn jämförelser är alltid skiftlägeskänsliga. Den **Ignorera skiftläge** alternativet avgör skiftlägeskänslighet för huvudet värdet jämförelser.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-regex"></a>Begäran sidhuvud Regex
Den **matchar**/**matchar inte** alternativet anger de villkor som begär sidhuvud Regex matchar villkoret uppfylls.
- **Matchar**: kräver begäranden som innehåller det angivna huvudet. Värdet måste matcha det mönster som har definierats i det angivna reguljära uttrycket.
- **Matchar inte**: kräver att begäran uppfyller något av följande kriterier:
  - Det innehåller inte det angivna huvudet.
  - Det innehåller det angivna huvudet, men dess värde matchar inte det angivna reguljära uttrycket.

Viktig information:
- Huvudnamn: 
  - Huvudet namn jämförelser är skiftlägeskänsliga.
  - Blanksteg i huvudnamnet som ska ersättas med ”% 20”. 
- Huvudets värde: 
  - Ett huvudvärde kan dra nytta av reguljära uttryck.
  - Den **Ignorera skiftläge** alternativet avgör skiftlägeskänslighet för huvudet värdet jämförelser.
  - Endast exakt sidhuvud matchar till minst en av de angivna mönster ska uppfylla det här villkoret.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell 

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-header-wildcard"></a>Begäran huvud med jokertecken
Den **matchar**/**matchar inte** alternativet anger de villkor som matchar jokertecknet begära sidhuvud villkoret uppfylls.
- **Matchar**: kräver begäranden som innehåller det angivna huvudet. Värdet måste matcha minst en av de värden som definieras i det här tillståndet för matchning.
- **Matchar inte**: kräver att begäran uppfyller något av följande kriterier:
  - Det innehåller inte det angivna huvudet.
  - Det innehåller det angivna huvudet, men dess värde matchar inte någon av de angivna värdena.
  
Viktig information:
- Huvudnamn: 
  - Huvudet namn jämförelser är skiftlägeskänsliga.
  - Blanksteg i huvudnamnet som ska ersättas med ”% 20”. Du kan också använda det här värdet för att ange blanksteg i ett huvudvärde.
- Huvudets värde: 
  - Ett huvudvärde kan dra nytta av specialtecken.
  - Den **Ignorera skiftläge** alternativet avgör skiftlägeskänslighet för huvudet värdet jämförelser.
  - Endast exakt sidhuvud matchar till minst en av de angivna mönster ska uppfylla det här villkoret.
  - Ange flera värden genom att avgränsa dem med ett blanksteg.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-method"></a>Metod för begäran
Endast de resurser som har begärts via metoden begäran som valts kommer uppfyller villkoret metod för begäran. Begäran om tillgängliga metoderna är:
- GET
- HUVUDET 
- POST 
- ALTERNATIV 
- PUT 
- DELETE 
- SPÅRNING 
- ANSLUTA 

Viktig information:
- Som standard kan endast GET-begäran-metoden generera cachelagrat innehåll i nätverket. Alla övriga metodbegäranden är via proxy via nätverket.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-scheme"></a>Schemat för begäran
Endast de resurser som har begärts via det valda protokollet ska uppfylla villkoret begära schemat. De tillgängliga protokollen är HTTP och HTTPS.

Viktig information:
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure Content Delivery Network](cdn-overview.md)
* [Regler modulreferens](cdn-rules-engine-reference.md)
* [Regler motorn villkorsuttryck](cdn-rules-engine-reference-conditional-expressions.md)
* [Regler motorn funktioner](cdn-rules-engine-reference-features.md)
* [Åsidosätta standardbeteendet i HTTP-motorn regler](cdn-rules-engine.md)

