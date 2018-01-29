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
ms.openlocfilehash: 08845355be0bfb7e7dde52d19949fee4a68ed54b
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
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
[URL-sökväg-katalog](#url-path-directory) | Identifierar begäranden via deras relativa sökvägen.
[URL-sökväg-tillägget](#url-path-extension) | Identifierar begäranden via deras filnamnstillägg.
[URL-sökväg filnamn](#url-path-filename) | Identifierar begäranden av filnamn.
[URL-sökväg Literal](#url-path-literal) | Jämför en begäran relativa sökvägen till det angivna värdet.
[URL-sökväg Regex](#url-path-regex) | Jämför en begäran relativa sökvägen till det angivna reguljära uttrycket.
[URL-sökväg med jokertecken](#url-path-wildcard) | Jämför en begäran relativa sökvägen till det angivna mönstret.
[URL-frågan Literal](#url-query-literal) | Jämför frågesträngen för en begäran till det angivna värdet.
[Frågeparametern för URL](#url-query-parameter) | Identifierar begäranden som innehåller den angivna frågesträngparametern ett värde som matchar ett specifikt mönster.
[URL-frågan Regex](#url-query-regex) | Identifierar begäranden som innehåller angivna frågesträngparametern ett värde som matchar angivna reguljära uttrycket.
[URL: en fråga med jokertecken](#url-query-wildcard) | Jämför det angivna värdet mot frågesträngen i begäran.


## <a name="reference-for-rules-engine-match-conditions"></a>Referens för motorn matchar de villkor som regler

---
### <a name="always"></a>Alltid

Alltid matchar villkoret gäller en standarduppsättning med funktioner för alla begäranden.

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>SOM tal 
Nätverkets AS-numret definieras av dess autonoma systemnummer (ASN). 

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka numret som matchar villkoret är uppfyllt:
- **Matchar**: kräver att klientnätverket ASN matchar ett av de angivna ASN: er. 
- **Har inte matchar**: kräver att ASN klientnätverket inte matchar någon av de angivna ASN: er.

Viktig information:
- Ange flera ASN: er genom att avgränsa dem med ett blanksteg. Exempelvis 64514 64515 matchar begäranden som tas emot från 64514 eller 64515.
- Vissa begäranden kan inte returnera ett giltigt ASN-NUMRET. Ett frågetecken (?) kommer att matcha begäranden som giltigt ASN-NUMRET inte kunde fastställas.
- Ange hela ASN för det önskade nätverket. Partiella värden kommer inte matchas.
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
- Innehåll från CDN lagring begärdes.
- URI-begäran används av innehålls åtkomstpunkt (till exempel /000001) som definieras i det här tillståndet matchar:
  - CDN-URL: URI-begäran måste innehålla valda innehålls åtkomstpunkt.
  - Edge CNAME-URL: Motsvarande kant CNAME konfigurationen måste peka på valda innehålls åtkomstpunkt.
  
Viktig information:
 - Innehålls åtkomstpunkt identifierar den tjänst som ska användas för det begärda innehållet.
 - Använd ett uttryck och om för att kombinera vissa villkor matchar inte. Kombinera ett villkor för matchning av CDN ursprung med en kund ursprung matchar villkoret skulle till exempel skapa ett matchningsmönster som inte kunde matchas. Därför kan inte två CDN ursprung matchar villkor kombineras via ett uttryck och om.

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>IP-adress för klient
Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka klientens IP-adress som matchar villkoret är uppfyllt:
- **Matchar**: kräver att klientens IP-adressen matchar ett av de angivna IP-adresserna. 
- **Har inte matchar**: kräver att klientens IP-adress inte matchar någon av de angivna IP-adresserna. 

Viktig information:
- Använd CIDR-notering.
- Ange flera IP-adresser och/eller IP-Adressblock genom att avgränsa dem med ett blanksteg. Exempel:
  - **IPv4-exempel**: 1.2.3.4 10.20.30.40 matchar alla begäranden som tas emot från adress 1.2.3.4 eller 10.20.30.40.
  - **IPv6-exempel**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matchar alla begäranden som tas emot från adress 1:2:3:4:5:6:7:8 eller 10:20:30:40:50:60:70:80.
- Syntaxen för ett IP-Adressblock är grundläggande IP-adressen följt av ett snedstreck och prefix-storlek. Exempel:
  - **IPv4-exempel**: 5.5.5.64/26 matchar alla begäranden som tas emot från adresser 5.5.5.64 via 5.5.5.127.
  - **IPv6-exempel**: 1:2:3: / 48 matchar alla begäranden som tas emot från adresser 1:2:3:0:0:0:0:0 via 1:2:3:ffff:ffff:ffff:ffff:ffff.
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
Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka Cookie-parametern matchar villkoret är uppfyllt.
- **Matchar**: kräver en förfrågan som innehåller den angivna cookien med ett värde som matchar åtminstone ett av de värden som definieras i det här tillståndet för matchning.
- **Matchar inte**: kräver att begäran uppfyller något av följande kriterier:
  - Den innehåller inte den angivna cookien.
  - Den innehåller den angivna cookien, men dess värde matchar inte någon av de värden som definieras i det här tillståndet för matchning.
  
Viktig information:
- Cookie-namn: 
  - Eftersom jokertecken värden, inklusive asterisker (*) inte stöds när du anger ett cookie-namn, är endast exakt cookie-namn matchar berättigade för jämförelse.
  - Bara ett enda cookie-namn kan anges per instans av det här matchar villkoret.
  - Cookie-namn jämförelser är skiftlägeskänsliga.
- Cookie-värde: 
  - Ange flera cookie-värden genom att avgränsa dem med ett blanksteg.
  - Ett cookievärde kan dra nytta av [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values). 
  - Om ett jokerteckenvärde har angetts, kommer endast en exakt matchning uppfyller felet matchning. Till exempel matchar ange ”värde” ”värde”, men inte ”Value1” eller ”Value2”.
  - Använd den **Ignorera skiftläge** alternativet att styra om gemener görs mot en begäran cookie-värde.
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
Cookie-parametern Regex matchar villkoret definierar ett cookie-namn och värde. Du kan använda [reguljära uttryck](cdn-rules-engine-reference.md#regular-expressions) definiera önskade cookie-värde. 

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka Regex för Cookie-parametern matchar villkoret är uppfyllt.
- **Matchar**: kräver en förfrågan som innehåller den angivna cookien med ett värde som matchar det angivna reguljära uttrycket.
- **Matchar inte**: kräver att begäran uppfyller något av följande kriterier:
  - Den innehåller inte den angivna cookien.
  - Den innehåller den angivna cookien, men dess värde matchar inte det angivna reguljära uttrycket.
  
Viktig information:
- Cookie-namn: 
  - Eftersom reguljära uttryck och jokertecken värden, inklusive asterisker (*) inte stöds när du anger ett cookie-namn, är endast exakt cookie-namn matchar berättigade för jämförelse.
  - Bara ett enda cookie-namn kan anges per instans av det här matchar villkoret.
  - Cookie-namn jämförelser är skiftlägeskänsliga.
- Cookie-värde: 
  - Ett cookievärde kan dra nytta av reguljära uttryck.
  - Använd den **Ignorera skiftläge** alternativet att styra om gemener görs mot en begäran cookie-värde.
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
Du kan ange ett land via dess landskod. 

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka landet matchar villkoret är uppfyllt:
- **Matchar**: kräver begäranden som innehåller de angivna land code-värdena. 
- **Matchar inte**: kräver att begäran inte innehåller de angivna land code-värdena.

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

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementera land filtrering med hjälp av motorn regler
Det här matchar villkoret kan du utföra en mängd olika anpassningar baserat på den plats som en begäran kommer från. Beteendet för funktionen land filtrering kan exempelvis replikeras via följande konfiguration:

- Matchning med jokertecken URL-sökväg: Ange den [URL-sökväg med jokertecken matchar villkoret](#url-path-wildcard) till den katalog som ska skyddas. 
    Lägg till en asterisk i slutet av den relativa sökvägen så att åtkomsten till alla underordnade ska begränsas av den här regeln.

- Land matchar: ange land matchar villkoret önskat antal länder.
   - Tillåt: Ange landet matcha tillståndet **matchar inte** att tillåta angivna länder åtkomst till innehåll som lagras på den plats som angetts i URL-sökväg med jokertecken matchar villkoren.
   - Blockering: Ange landet matcha tillståndet **matchar** att blockera de angivna länderna från att komma åt innehåll som lagras på den plats som angetts i URL-sökväg med jokertecken matchar villkoren.

- Neka åtkomst (403)-funktion: Aktivera den [funktionen neka åtkomst (403)](cdn-rules-engine-reference-features.md#deny-access-403) att replikera den Tillåt eller blockerade delen av funktionen land filtrering.

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>Kunden ursprung

Viktig information: 
- Kunden ursprung matchar villkoret är uppfyllt oavsett om innehållet begärs via en CDN-URL eller en kant CNAME-URL som pekar på det valda kunden ursprunget.
- En konfiguration av customer ursprung som refereras av en regel kan inte tas bort från sidan Customer ursprung. Kontrollera att följande konfigurationer inte refererar till den innan du försöker ta bort en konfiguration av customer ursprung:
  - En kund ursprung matchar villkoret
  - En kant CNAME-konfiguration
- Använd ett uttryck och om för att kombinera vissa villkor matchar inte. Till exempel skapar kombinera en kund ursprung matchar villkoret med ett villkor för matchning av CDN ursprung ett matchningsmönster som inte kunde matchas. Därför kan inte två kunden ursprung matchar villkor kombineras via ett uttryck och om.

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>Enhet

Enheten matchar villkoret identifierar begäranden från en mobil enhet baserat på dess egenskaper. Identifiering av mobila enheter uppnås genom [WURFL](http://wurfl.sourceforge.net/). 

Den **matchar**/**matchar inte** alternativet anger de villkor som enheten matchar villkoret är uppfyllt:
- **Matchar**: kräver den som begär enheten så att den matchar det angivna värdet. 
- **Har inte matchar**: kräver att den som begär enheten inte matchar det angivna värdet.

Viktig information:

- Använd den **Ignorera skiftläge** alternativet för att ange om det angivna värdet är skiftlägeskänsliga.
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

#### <a name="string-type"></a>Strängtyp
En WURFL funktion accepterar vanligtvis en kombination av siffror, bokstäver och symboler. På grund av flexibel hur den här funktionen måste du välja hur värdet som är associerade med det här matchar villkoret tolkas. I följande tabell beskrivs tillgängliga uppsättning alternativ:

Typ     | Beskrivning
---------|------------
Literalen  | Välj det här alternativet för att förhindra att de flesta tecken tar på särskild innebörd med hjälp av sina [teckenvärde](cdn-rules-engine-reference.md#literal-values).
Jokertecken | Välj det här alternativet om du vill dra nytta av alla [jokertecken] ([jokertecken värden](cdn-rules-engine-reference.md#wildcard-values).
Regex    | Välj det här alternativet att använda [reguljära uttryck](cdn-rules-engine-reference.md#regular-expressions). Reguljära uttryck är användbara för att definiera ett mönster med tecken.

#### <a name="wurfl-capabilities"></a>WURFL funktioner
En funktion för WURFL refererar till en kategori som beskriver mobila enheter. Den valda kapaciteten avgör vilken typ av mobil enhetsbeskrivning som används för att identifiera begäranden.

I följande tabell visas WURFL funktioner och deras variabler för regler-motorn.
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
Är Tablet | % {wurfl_cap_is_tablet} | Ett booleskt värde som anger om enheten är en surfplatta. Den här beskrivningen är oberoende av Operativsystemet. | sant
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
- Listan över tillgängliga edge skapa CNAME-poster är begränsad till de edge CNAME-resursposter som har konfigurerats på sidan Edge skapa CNAME-poster för den plattform som motorn regler som konfigureras.
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
Värdnamnet är associerade med referent som begärdes innehåll avgör om den refererande domän är uppfyllt. 

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka hänvisar-domänen matchar villkoret är uppfyllt:
- **Matchar**: kräver refererande värdnamnet som matchar de angivna värdena. 
- **Har inte matchar**: kräver att refererande värdnamnet inte matchar det angivna värdet.

Viktig information:
- Ange flera värden genom att avgränsa dem med ett blanksteg.
- Har stöd för det här tillståndet matchar [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values).
- Om det angivna värdet inte innehåller en asterisk, måste den vara en exakt matchning för den referent värdnamn. Till exempel matchar ange ”mydomain.com” inte ”www.mydomain.com”.
- Använd den **Ignorera skiftläge** alternativet att styra om gemener görs.
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
Den **matchar**/**matchar inte** alternativet anger de villkor som de begär sidhuvud Literal matchar villkoret är uppfyllt.
- **Matchar**: kräver begäranden som innehåller det angivna huvudet. Värdet måste matcha det som definieras i det här tillståndet för matchning.
- **Matchar inte**: kräver att begäran uppfyller något av följande kriterier:
  - Det innehåller inte det angivna huvudet.
  - Det innehåller det angivna huvudet, men dess värde matchar inte det som definieras i det här tillståndet för matchning.
  
Viktig information:
- Huvudet namn jämförelser är alltid skiftlägeskänsliga. Använd den **Ignorera skiftläge** möjlighet att styra skiftlägeskänslighet för huvudet värdet jämförelser.
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
Den **matchar**/**matchar inte** alternativet anger de villkor som begär sidhuvud Regex matchar villkoret är uppfyllt.
- **Matchar**: kräver begäranden som innehåller det angivna huvudet. Värdet måste matcha det mönster som har definierats i den angivna [reguljärt uttryck](cdn-rules-engine-reference.md#regular-expressions).
- **Matchar inte**: kräver att begäran uppfyller något av följande kriterier:
  - Det innehåller inte det angivna huvudet.
  - Det innehåller det angivna huvudet, men dess värde matchar inte det angivna reguljära uttrycket.

Viktig information:
- Huvudnamn: 
  - Huvudet namn jämförelser är skiftlägeskänsliga.
  - Ersätt blanksteg i huvudets namn med ”% 20”. 
- Huvudets värde: 
  - Ett huvudvärde kan dra nytta av reguljära uttryck.
  - Använd den **Ignorera skiftläge** möjlighet att styra skiftlägeskänslighet för huvudet värdet jämförelser.
  - Matchar villkoret är uppfyllt endast när ett huvudvärde exakt matchar minst en av de angivna mönster.
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
Den **matchar**/**matchar inte** alternativet anger de villkor som matchar jokertecknet begära sidhuvud villkoret är uppfyllt.
- **Matchar**: kräver begäranden som innehåller det angivna huvudet. Värdet måste matcha minst en av de värden som definieras i det här tillståndet för matchning.
- **Matchar inte**: kräver att begäran uppfyller något av följande kriterier:
  - Det innehåller inte det angivna huvudet.
  - Det innehåller det angivna huvudet, men dess värde matchar inte någon av de angivna värdena.
  
Viktig information:
- Huvudnamn: 
  - Huvudet namn jämförelser är skiftlägeskänsliga.
  - Blanksteg i huvudnamnet som ska ersättas med ”% 20”. Du kan också använda det här värdet för att ange blanksteg i ett huvudvärde.
- Huvudets värde: 
  - Ett huvudvärde kan dra nytta av [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values).
  - Använd den **Ignorera skiftläge** möjlighet att styra skiftlägeskänslighet för huvudet värdet jämförelser.
  - Den här matchar villkoret är uppfyllt när ett huvudvärde matchar exakt till minst en av de angivna mönster.
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
Metoden matchar villkoret är uppfyllt endast när tillgångar har begärts via metoden begäran som valts. Begäran om tillgängliga metoderna är:
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
Begära schema matchar villkoret är uppfyllt endast när tillgångar har begärts via det valda protokollet. Tillgängliga protokoll är: 
- HTTP
- HTTPS

Viktig information:
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
  - Slutföra Cache Fill
  - Internt Max-åldern som standard
  - Tvinga inre maximal ålder
  - Ignorera ursprung No-Cache
  - Internt Max-inaktuell

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-directory"></a>URL-sökväg-katalog
Identifierar en förfrågan av relativ sökväg, vilket utesluter filnamnet för den begärda tillgången.

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka URL-sökväg Directory matchar villkoret är uppfyllt.
- **Matchar**: kräver begäranden som innehåller en relativ sökväg för URL: en, förutom det filnamn som matchar det angivna URL-mönstret.
- **Har inte matchar**: kräver begäranden som innehåller en relativ sökväg för URL: en, förutom filnamn, som inte matchar det angivna URL-mönstret.

Viktig information:
- Använd den **relativt** alternativet för att ange om URL: en jämförelse startar före eller efter innehålls åtkomstpunkt. Innehålls åtkomstpunkt är del av sökvägen som visas mellan Verizon CDN-värdnamn och den relativa sökvägen till den begärda tillgången (till exempel /800001/CustomerOrigin). Den identifierar en plats med servertyp (till exempel CDN eller kund ursprung) och ditt konto kundnummer.

   Följande värden är tillgängliga för den **relativt** alternativ:
   - **Roten**: Anger att URL: en jämförelse punkten börjar direkt efter CDN-värdnamn. 

     Till exempel: http:\//wpc.0001.&lt; domän&gt;/**MinMapp-800001/myorigin**/index.htm

   - **Ursprung**: Anger att URL: en jämförelse punkten börjar efter innehålls åtkomstpunkt (till exempel /000001 eller/800001/myorigin). Eftersom den \*. azureedge.net CNAME skapas i förhållande till katalogen ursprung på Verizon CDN-värdnamn som standard, Azure CDN-användare bör använda den **ursprung** värde. 

     Till exempel: https:\//&lt;endpoint&gt;.azureedge.net/**MinMapp**/index.htm 

     Den här URL: en pekar till följande Verizon CDN-värdnamn: http:\//wpc.0001.&lt; Domän&gt;/800001/myorigin/**MinMapp**/index.htm

- En kant CNAME URL: en skrivs till en CDN-URL innan du URL: en jämförelse.

    Till exempel båda av följande webbadresser peka på samma tillgång och därför har samma URL-sökväg.
    - CDN-URL: http:\//wpc.0001.&lt; Domän&gt;/800001/CustomerOrigin/path/asset.htm
    
    - Edge CNAME-URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Ytterligare information:
    - Anpassad domän: https:\//my.domain.com/path/asset.htm
    
    - URL-sökväg (i förhållande till rot): / 800001/CustomerOrigin/path /
    
    - URL-sökväg (i förhållande till originalet): /path/

- Del av URL: en som används för URL: en jämförelse avslutas precis innan filnamnet för den begärda tillgången. En avslutande snedstreck är det sista tecknet i den här typen av sökväg.
    
- Ersätt alla blanksteg i URL-sökvägar med ”% 20”.
    
- Varje URL-sökvägar kan innehålla en eller flera asterisker (*), där varje asterisk matchar en sekvens med ett eller flera tecken.
    
- Ange flera URL-sökvägar i mönstret genom att avgränsa dem med ett blanksteg.

    Exempel: * /sales/ * /marketing/

- En URL-sökvägen kan dra nytta av [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values).

- Använd den **Ignorera skiftläge** alternativet att styra om gemener utförs.

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-extension"></a>URL-sökväg-tillägget
Identifierar begäranden av filnamnstillägget för den begärda tillgången.

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka URL-sökvägen tillägget matchar villkoret är uppfyllt.
- **Matchar**: kräver URL för begäran innehåller ett filnamnstillägg som matchar exakt det angivna mönstret.

   Till exempel om du anger ”htm”, matchas ”htm” tillgångar, men inte ”html” tillgångar.  

- **Har inte matchar**: kräver URL-begäran innehåller ett filnamnstillägg som inte matchar det angivna mönstret.

Viktig information:
- Ange de filnamnstillägg som matchar i den **värdet** rutan. Inkludera inte en inledande period. till exempel använda htm i stället för .htm.

- Använd den **Ignorera skiftläge** alternativet att styra om gemener utförs.

- Ange flera filnamnstillägg genom att avgränsa varje tillägg med ett blanksteg. 

    Till exempel: htm html

- Till exempel matchar ange ”htm” ”htm” tillgångar, men inte ”html” tillgångar.


#### <a name="sample-scenario"></a>Exempelscenario

Följande exempelkonfiguration förutsätter att den här matchar villkor när en begäran matchar ett av de angivna tillägg.

Värdet specifikationen: asp aspx php html

Den här matchar villkor när den hittar URL: er som slutar med följande filnamnstillägg:
- ASP
- .aspx
- .php
- .HTML

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-filename"></a>URL-sökväg filnamn
Identifierar begäranden av filnamnet för den begärda tillgången. I syfte att detta matchar ett filnamn som består av namnet på den begärda tillgången, en period och filnamnstillägget (till exempel index.html).

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka URL-sökväg filnamn matchar villkoret är uppfyllt.
- **Matchar**: kräver en begäran om att innehålla ett filnamn i dess URL-sökväg som matchar det angivna mönstret.
- **Har inte matchar**: kräver en begäran om att innehålla ett filnamn i dess URL-sökväg som inte matchar det angivna mönstret.

Viktig information:
- Använd den **Ignorera skiftläge** alternativet att styra om gemener utförs.

- Avgränsa varje tillägg för att ange flera filnamnstillägg, med ett blanksteg.

    Till exempel: index.htm index.html

- Ersätt blanksteg i en namnvärde med ”% 20”.
    
- En namnvärde kan dra nytta av [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values). Varje filnamnsmönster kan exempelvis bestå av en eller flera asterisker (*), där varje asterisk matchar en sekvens med ett eller flera tecken.
    
- Om jokertecken inte har angetts, kommer endast en exakt matchning uppfyller det här matchar villkoret.

    Till exempel matchar ange ”presentation.ppt” en tillgång med namnet ”presentation.ppt”, men inte en namngiven ”presentation.pptx”.

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-literal"></a>URL-sökväg Literal
Jämför en begäran URL-sökväg, inklusive namnet på filen med det angivna värdet.

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka URL-sökväg Literal matchar villkoret är uppfyllt.
- **Matchar**: kräver begäranden som innehåller en URL-sökväg som matchar det angivna mönstret.
- **Har inte matchar**: kräver begäranden som innehåller en URL-sökväg som inte matchar det angivna mönstret.

Viktig information:
- Använd den **relativt** alternativet för att ange om de URL: en jämförelse inleder före eller efter innehålls åtkomstpunkt. 

    Följande värden är tillgängliga för den **relativt** alternativ:
     - **Roten**: Anger att URL: en jämförelse punkten börjar direkt efter CDN-värdnamn.

       Till exempel: http:\//wpc.0001.&lt; Domän&gt;/**800001/myorigin/myfolder/index.htm**

     - **Ursprung**: Anger att URL: en jämförelse punkten börjar efter innehålls åtkomstpunkt (till exempel /000001 eller/800001/myorigin). Eftersom den \*. azureedge.net CNAME skapas i förhållande till katalogen ursprung på Verizon CDN-värdnamn som standard, Azure CDN-användare bör använda den **ursprung** värde. 

       Till exempel: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     Den här URL: en pekar till följande Verizon CDN-värdnamn: http:\//wpc.0001.&lt; Domän&gt;/800001/myorigin/**myfolder/index.htm**

- En kant CNAME URL Om till en CDN-URL innan en URL-jämförelse.

   Till exempel båda av följande webbadresser peka på samma tillgång och därför har samma URL-sökväg:
    - CDN-URL: http:\//wpc.0001.&lt; Domän&gt;/800001/CustomerOrigin/path/asset.htm
    - Edge CNAME-URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

   Ytterligare information:
    
    - URL-sökväg (i förhållande till rot): /800001/CustomerOrigin/path/asset.htm
   
    - URL-sökväg (i förhållande till originalet): /path/asset.htm

- Frågesträngar i Webbadressen ignoreras.
- Använd den **Ignorera skiftläge** alternativet att styra om gemener utförs.
- Det angivna värdet för det här matchar villkoret ska jämföras med den relativa sökvägen för exakt begäran från klienten.

- Om du vill matcha alla begäranden som görs till en viss katalog, använder den [URL-sökväg Directory](#url-path-directory) eller [URL-sökväg med jokertecken](#url-path-wildcard) matchar villkoret.

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-regex"></a>URL-sökväg Regex
Jämför en begäran URL-sökvägen till den angivna [reguljärt uttryck](cdn-rules-engine-reference.md#regular-expressions).

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka URL-sökväg Regex matchar villkoret är uppfyllt.
- **Matchar**: kräver begäranden som innehåller en URL-sökväg som matchar det angivna reguljära uttrycket.
- **Har inte matchar**: kräver begäranden som innehåller en URL-sökväg som inte matchar det angivna reguljära uttrycket.

Viktig information:
- En kant CNAME URL: en skrivs till en CDN-URL innan du URL: en jämförelse. 
 
   Båda URL pekar på samma tillgång och därför har samma URL-sökväg.

     - CDN-URL: http:\//wpc.0001.&lt; Domän&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME-URL: http:\//my.domain.com/path/asset.htm

   Ytterligare information:
    
     - URL-sökväg: /800001/CustomerOrigin/path/asset.htm

- Frågesträngar i Webbadressen ignoreras.
    
- Använd den **Ignorera skiftläge** alternativet att styra om gemener utförs.
    
- Blanksteg i URL-sökvägen ska ersättas med ”% 20”.

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-wildcard"></a>URL-sökväg med jokertecken
Jämför en begäran relativa URL-sökvägen till den angivna jokerteckensmönster.

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka URL-sökväg med jokertecken matchar villkoret är uppfyllt.
- **Matchar**: kräver begäranden som innehåller en URL-sökväg som matchar de angivna jokerteckensmönster.
- **Har inte matchar**: kräver begäranden som innehåller en URL-sökväg som inte matchar den angivna jokerteckensmönster.

Viktig information:
- **Relativt** alternativet: det här alternativet avgör om URL: en jämförelse punkten börjar före eller efter innehålls åtkomstpunkt.

   Det här alternativet kan ha följande värden:
     - **Roten**: Anger att URL: en jämförelse punkten börjar direkt efter CDN-värdnamn.

       Till exempel: http:\//wpc.0001.&lt; Domän&gt;/**800001/myorigin/myfolder/index.htm**

     - **Ursprung**: Anger att URL: en jämförelse punkten börjar efter innehålls åtkomstpunkt (till exempel /000001 eller/800001/myorigin). Eftersom den \*. azureedge.net CNAME skapas i förhållande till katalogen ursprung på Verizon CDN-värdnamn som standard, Azure CDN-användare bör använda den **ursprung** värde. 

       Till exempel: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     Den här URL: en pekar till följande Verizon CDN-värdnamn: http:\//wpc.0001.&lt; Domän&gt;/800001/myorigin/**myfolder/index.htm**

- En kant CNAME URL: en skrivs till en CDN-URL innan du URL: en jämförelse.

   Till exempel båda av följande webbadresser peka på samma tillgång och därför har samma URL-sökväg:
     - CDN-URL: http://wpc.0001. &lt;Domän&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME-URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

   Ytterligare information:
    
     - URL-sökväg (i förhållande till rot): /800001/CustomerOrigin/path/asset.htm
    
     - URL-sökväg (i förhållande till originalet): /path/asset.htm
    
- Ange flera URL-sökvägar genom att avgränsa dem med ett blanksteg.

   Till exempel: /marketing/asset.* /sales/*.htm

- Frågesträngar i Webbadressen ignoreras.
    
- Använd den **Ignorera skiftläge** alternativet att styra om gemener utförs.
    
- Ersätt blanksteg i URL-sökväg med ”% 20”.
    
- Det angivna värdet för URL-sökvägen kan dra nytta av [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values). Varje URL-sökvägar kan innehålla en eller flera asterisker (*), där varje asterisk kan matcha en sekvens med ett eller flera tecken.

#### <a name="sample-scenarios"></a>Exempelscenarier

Exempel konfigurationer i tabellen nedan förutsätter att den här matchar villkor när en begäran matchar det angivna mönstret för URL:

Värde                   | Relativt till    | Resultat 
------------------------|----------------|-------
*/test.HTML */test.php  | Rot- eller ursprung | Det här mönstret matchas av begäranden för tillgångar med namnet ”test.html” eller ”test.php” i en mapp.
/ 80ABCD/ursprung/text / *   | Rot           | Det här mönstret matchas när den begärda tillgången uppfyller följande kriterier: <br />-Det måste finnas på en kund ursprung som kallas ”ursprung”. <br />-Den relativa sökvägen måste börja med en mapp med namnet ”text”. Det vill säga kan den begärda tillgången finnas antingen i mappen ”text” eller en av dess rekursiv undermappar.
*/CSS/* */js/*          | Rot- eller ursprung | Det här mönstret matchas av alla CDN eller edge CNAME-adresser som innehåller en css- eller js mapp.
*.jpg *.gif *.png       | Rot- eller ursprung | Det här mönstret matchas av alla CDN eller edge CNAME URL: er med JPG, GIF eller .png. Ett annat sätt att ange det här mönstret är med i [URL-sökvägen tillägget matchar villkoret](#url-path-extension).
/ bilder / * / media / *      | Ursprung         | Det här mönstret matchas av CDN eller edge CNAME-URL: er vars relativa sökvägar som börjar med en mapp ”bilder” eller ”media”. <br />-CDN URL: http:\//wpc.0001.&lt; Domän&gt;/800001/myorigin/images/sales/event1.png<br />-Exempel edge CNAME-URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-literal"></a>URL-frågan Literal
Jämför frågesträngen för en begäran till det angivna värdet.

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka URL-frågan Literal matchar villkoret är uppfyllt.
- **Matchar**: kräver begäranden som innehåller en URL-frågesträng som matchar den angivna frågesträngen.
- **Har inte matchar**: kräver en begäran om att innehålla en frågesträng för URL: en som inte matchar den angivna frågesträngen.

Viktig information:

- Endast exakt frågan strängen matchar uppfylla det här matchar villkoret.
    
- Använd den **Ignorera skiftläge** möjlighet att styra skiftlägeskänslighet för frågan strängjämförelser.
    
- Inkludera inte en inledande frågetecken (?) i frågetexten sträng värde.
    
- Vissa tecken kräver URL-kodning. Använd procentandel symbolen till URL: en koda följande tecken:

   Tecken | URL-kodning
   ----------|---------
   Blanksteg     | % 20
   &         | % 25

- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
   - Slutföra Cache Fill
   - Internt Max-åldern som standard
   - Tvinga inre maximal ålder
   - Ignorera ursprung No-Cache
   - Internt Max-inaktuell

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-parameter"></a>Frågeparametern för URL
Identifierar begäranden som innehåller den angivna parametern för frågesträngen. Den här parametern anges till ett värde som matchar ett specifikt mönster. Fråga string-parametrar (t.ex, parameter = värde) i begäran URL avgöra om det här villkoret är uppfyllt. Det här matchar villkoret identifierar en frågesträngparametern med namnet och accepterar ett eller flera värden för parametervärdet. 

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka Frågeparametern för URL: en matchar villkoret är uppfyllt.
- **Matchar**: kräver en förfrågan som innehåller den angivna parametern med ett värde som matchar åtminstone ett av de värden som definieras i det här tillståndet för matchning.
- **Matchar inte**: kräver att begäran uppfyller något av följande kriterier:
  - Den innehåller inte den angivna parametern.
  - Den innehåller den angivna parametern, men dess värde matchar inte någon av de värden som definieras i det här tillståndet för matchning.

Det här tillståndet matchar ger ett enkelt sätt att ange parametern namn/värde-kombinationer. För större flexibilitet om du har angett en frågesträngsparameter, bör du använda den [URL-frågan med jokertecken](#url-query-wildcard) matchar villkoret.

Viktig information:
- Endast en enskild URL parametern Frågenamnet kan anges per instans av det här matchar villkoret.
    
- Eftersom jokertecken värden inte stöds när namnet på en parameter har angetts, är endast exakt parametern namn matchar berättigade för jämförelse.
- Värden för parametern kan innehålla [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values).
   - Varje parameter värdet mönster kan bestå av en eller flera asterisker (*), där varje asterisk kan matcha en sekvens med ett eller flera tecken.
   - Vissa tecken kräver URL-kodning. Använd procentandel symbolen till URL: en koda följande tecken:

       Tecken | URL-kodning
       ----------|---------
       Blanksteg     | % 20
       &         | % 25

- Ange parametervärden för flera frågan sträng genom att avgränsa dem med ett blanksteg. Den här matchar villkoret är uppfyllt när en begäran innehåller ett av de angivna namnvärde kombinationerna.

   - Exempel 1:

     - Konfiguration:

       ValueA ValueB

     - Den här konfigurationen matchar följande sträng frågeparametrar:

       Parameter1 = ValueA
    
       Parameter1 = ValueB

   - Exempel 2:

     - Konfiguration: 

        Värdet % 20A värdet % 20B

     - Den här konfigurationen matchar följande sträng frågeparametrar:

       Parameter1 = värde % 20A

       Parameter1 = värde % 20B

- Den här matchar villkoret är uppfyllt när det finns en exakt matchning i minst en av de angivna fråga sträng namn/värde-kombinationerna.

   Till exempel om du använder konfigurationen i föregående exempel parametern namn/värde-kombination ”Parameter1 = ValueAdd” kan inte betraktas som en matchning. Om du anger något av följande värden, kommer den matchar den kombinationen av namn/värde:

   - ValueA ValueB ValueAdd
   - ValueA * ValueB

- Använd den **Ignorera skiftläge** möjlighet att styra skiftlägeskänslighet för frågan strängjämförelser.
    
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
   - Slutföra Cache Fill
   - Internt Max-åldern som standard
   - Tvinga inre maximal ålder
   - Ignorera ursprung No-Cache
   - Internt Max-inaktuell

#### <a name="sample-scenarios"></a>Exempelscenarier
I följande exempel visar hur det här alternativet fungerar i vissa situationer:

Namn      | Värde |  Resultat
----------|-------|--------
Användare      | Joe   | Det här mönstret matchas när frågesträngen för en begärd URL är ”? användare = joe”.
Användare      | *     | Det här mönstret matchas när frågesträngen för en begärd URL innehåller en parameter för användaren.
Joe e-post | *     | Det här mönstret matchas när frågesträngen för en begärd URL innehåller en parameter av e-post som börjar med ”jan”.

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-regex"></a>URL-frågan Regex
Identifierar begäranden som innehåller den angivna parametern för frågesträngen. Den här parametern anges till ett värde som matchar en angiven [reguljärt uttryck](cdn-rules-engine-reference.md#regular-expressions).

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka Regex för URL-frågan matchar villkoret är uppfyllt.
- **Matchar**: kräver en begäran om att innehålla en frågesträng för URL: en som matchar det angivna reguljära uttrycket.
- **Har inte matchar**: kräver en begäran om att innehålla en frågesträng för URL: en som inte matchar det angivna reguljära uttrycket.

Viktig information:
- Endast uppfylla exakt matchar med det angivna reguljära uttrycket det här matchar villkoret.
    
- Använd den **Ignorera skiftläge** möjlighet att styra skiftlägeskänslighet för frågan strängjämförelser.
    
- Vid tillämpningen av det här alternativet startar en frågesträng med det första tecknet efter avgränsaren frågetecken (?) för frågesträngen.
    
- Vissa tecken kräver URL-kodning. Använd procentandel symbolen till URL: en koda följande tecken:

   Tecken | URL-kodning | Värde
   ----------|--------------|------
   Blanksteg     | % 20          | \%20
   &         | % 25          | \%25

   Observera att procentandel symboler måste hoppas.

- Dubbla undantagstecken särskilda reguljära tecken (till exempel \^$. +) med ett omvänt snedstreck i det reguljära uttrycket.

   Exempel:

   Värde | Tolkas som 
   ------|---------------
   \\+    | +
   \\\+   | \\+

- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
   - Slutföra Cache Fill
   - Internt Max-åldern som standard
   - Tvinga inre maximal ålder
   - Ignorera ursprung No-Cache
   - Internt Max-inaktuell


[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-wildcard"></a>URL: en fråga med jokertecken
Jämför de angivna värdena mot frågesträngen i begäran.

Den **matchar**/**matchar inte** alternativet avgör de villkor under vilka jokertecken för URL-frågan matchar villkoret är uppfyllt.
- **Matchar**: kräver begäranden som innehåller en URL-frågesträng som matchar angivna jokertecknet.
- **Har inte matchar**: kräver en begäran om att innehålla en frågesträng för URL: en som inte matchar det angivna jokertecknet.

Viktig information:
- Vid tillämpningen av det här alternativet startar en frågesträng med det första tecknet efter avgränsaren frågetecken (?) för frågesträngen.
- Parametervärden kan innehålla [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values):
   - Varje parameter värdet mönster kan bestå av en eller flera asterisker (*), där varje asterisk kan matcha en sekvens med ett eller flera tecken.
   - Vissa tecken kräver URL-kodning. Använd procentandel symbolen till URL: en koda följande tecken:

     Tecken | URL-kodning
     ----------|---------
     Blanksteg     | % 20
     &         | % 25

- Ange flera värden genom att avgränsa dem med ett blanksteg.

   Till exempel: *Parameter1 = ValueA* *ValueB* *Parameter1 = ValueC & Parameter2 = ValueD*

- Endast uppfylla exakt matchar till minst en av de angivna fråga sträng mönster det här matchar villkoret.
    
- Använd den **Ignorera skiftläge** möjlighet att styra skiftlägeskänslighet för frågan strängjämförelser.
    
- På grund av det sätt som i vilken cache inställningar spåras är det här matchar villkoret inte kompatibel med följande funktioner:
   - Slutföra Cache Fill
   - Internt Max-åldern som standard
   - Tvinga inre maximal ålder
   - Ignorera ursprung No-Cache
   - Internt Max-inaktuell

#### <a name="sample-scenarios"></a>Exempelscenarier
I följande exempel visar hur det här alternativet fungerar i vissa situationer:

 Namn                 | Beskrivning
 ---------------------|------------
användare = joe              | Det här mönstret matchas när frågesträngen för en begärd URL är ”? användare = joe”.
\*användare =\* \*optout =\* | Det här mönstret matchas när CDN URL-frågan innehåller användaren eller optout parametern.

[Överst på sidan](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure Content Delivery Network](cdn-overview.md)
* [Regler modulreferens](cdn-rules-engine-reference.md)
* [Regler motorn villkorsuttryck](cdn-rules-engine-reference-conditional-expressions.md)
* [Regler motorn funktioner](cdn-rules-engine-reference-features.md)
* [Åsidosätta standardbeteendet i HTTP-motorn regler](cdn-rules-engine.md)

