---
title: Azure CDN regelmotor – matchningsvillkor | Microsoft Docs
description: Referensdokumentation för Azure Content Delivery Network regelmotor – matchningsvillkor.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: 75fe965a04bd02a1086551053c28d2072eae6468
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869512"
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Azure CDN-regelmotor matchar de villkor 
Den här artikeln innehåller detaljerade beskrivningar av tillgängliga matchningsvillkor för Azure Content Delivery Network (CDN) [regelmotor](cdn-rules-engine.md).

Den andra delen av en regel är matchningsvillkor. Ett matchningsvillkor identifierar vissa typer av begäranden som en uppsättning funktioner kommer att utföras.

Du kan till exempel använda ett matchningsvillkor till:
- Filtrera begäranden för innehåll på en viss plats.
- Filtrera begäranden som genereras från en viss IP-adress eller land/region.
- Filtrera förfrågningar efter rubrikinformation.

## <a name="always-match-condition"></a>Alltid matchningsvillkor

Matchningsvillkor alltid gäller en standarduppsättning med funktioner för alla begäranden.

Namn | Syfte
-----|--------
[Alltid](#always) | Gäller en standarduppsättning med funktioner för alla begäranden.

## <a name="device-match-condition"></a>Enheten matchningsvillkor

Matchningsvillkor enhet identifierar begäranden som görs från en mobil enhet baserat på dess egenskaper.  

Namn | Syfte
-----|--------
[enheten](#device) | Identifierar begäranden som görs från en mobil enhet baserat på dess egenskaper.

## <a name="location-match-conditions"></a>Plats matchningsvillkor

Matchningsvillkor plats identifiera förfrågningar baserat på förfrågarens plats.

Namn | Syfte
-----|--------
[AS-nummer](#as-number) | Identifierar begäranden som kommer från ett visst nätverk.
[Land/region](#country) | Identifierar förfrågningar som kommer från de angivna länder/regionerna.

## <a name="origin-match-conditions"></a>Ursprung matchningsvillkor

Matchningsvillkor ursprung identifiera begäranden som pekar på Content Delivery Network-lagring eller en kund ursprungsservern.

Namn | Syfte
-----|--------
[CDN-ursprung](#cdn-origin) | Identifierar begäranden för innehåll som lagras i Content Delivery Network-lagring.
[Kunden](#customer-origin) | Identifierar begäranden för innehåll som lagras på en viss kund ursprungsservern.

## <a name="request-match-conditions"></a>Begär matchningsvillkor

Matchningsvillkor begäran identifiera förfrågningar baserat på deras egenskaper.

Namn | Syfte
-----|--------
[Klientens IP-adress](#client-ip-address) | Identifierar förfrågningar som kommer från en viss IP-adress.
[Cookie-Parameter](#cookie-parameter) | Kontrollerar de cookies som är associerade med varje begäran för det angivna värdet.
[Cookie parametern Regex](#cookie-parameter-regex) | Kontrollerar de cookies som är associerade med varje begäran för det angivna reguljära uttrycket.
[Edge Cname](#edge-cname) | Identifierar begäranden som pekar på en specifik edge CNAME.
[Refererande domän](#referring-domain) | Identifierar begäranden som har hänvisats från de angivna värdnamn.
[Begära huvud Literal](#request-header-literal) | Identifierar förfrågningar som innehåller det angivna huvudet inställt på ett angivet värde.
[Begära huvud Regex](#request-header-regex) | Identifierar förfrågningar som innehåller det angivna huvudet inställt på ett värde som matchar det angivna reguljära uttrycket.
[Begära huvud med jokertecken](#request-header-wildcard) | Identifierar förfrågningar som innehåller det angivna huvudet inställt på ett värde som matchar det angivna mönstret.
[Begärandemetod](#request-method) | Identifierar begäranden av sina HTTP-metoden.
[Begäran-schema](#request-scheme) | Identifierar begäranden via sina HTTP-protokollet.

## <a name="url-match-conditions"></a>URL: en matchningsvillkor

URL-matchningsvillkor identifiera förfrågningar baserat på deras webbadresser.

Namn | Syfte
-----|--------
[URL-sökväg-katalog](#url-path-directory) | Identifierar begäranden efter deras relativa sökväg.
[URL-sökväg-tillägget](#url-path-extension) | Identifierar begäranden genom att deras filnamnstillägg.
[URL: en sökväg filnamn](#url-path-filename) | Identifierar begäranden efter deras filnamn.
[URL-sökväg Literal](#url-path-literal) | Jämför en begäran relativa sökvägen till det angivna värdet.
[URL-sökväg Regex](#url-path-regex) | Jämför en begäran relativa sökvägen till det angivna reguljära uttrycket.
[URL-sökväg med jokertecken](#url-path-wildcard) | Jämför en begäran relativa sökvägen till det angivna mönstret.
[URL: en fråga Literal](#url-query-literal) | Jämför frågesträng för en begäran till det angivna värdet.
[URL: en frågeparameter](#url-query-parameter) | Identifierar förfrågningar som innehåller den angivna parametern för frågesträngen ange ett värde som matchar ett specifikt mönster.
[URL: en fråga Regex](#url-query-regex) | Identifierar förfrågningar som innehåller den angivna parametern för frågesträngen ange ett värde som matchar angivna reguljära uttrycket.
[URL: en fråga med jokertecken](#url-query-wildcard) | Jämför det angivna värdet mot frågesträngen i begäran.


## <a name="reference-for-rules-engine-match-conditions"></a>Referens för regelmotor – matchningsvillkor
<a name="main"></a>
---
### <a name="always"></a>Alltid

Matchningsvillkor alltid gäller en standarduppsättning med funktioner för alla begäranden.

[Överst på sidan](#main)

</br>

---
### <a name="as-number"></a>AS-nummer 
AS-nummer nätverk definieras av dess autonomt systemnummer (ASN). 

Den **matchningar**/**matchar inte** anger de villkor som AS-nummer som matchar villkoret är uppfyllt:
- **Matchningar**: Kräver att ASN på klientnätverket matchar en av de angivna ASN: er. 
- **Matchar inte**: Kräver att ASN på klientnätverket inte matchar någon av de angivna ASN: er.

Viktig information:
- Ange flera ASN: er genom att avgränsa dem med ett enda blanksteg. Till exempel 64514 64515 matchar begäranden som tas emot från 64514 eller 64515.
- Vissa begäranden kanske inte returnerar giltigt ASN-NUMRET. Ett frågetecken (?) kommer att matcha begäranden som giltigt ASN-NUMRET inte kunde fastställas.
- Ange hela ASN för det önskade nätverket. Partiella värden matchas inte.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

[Överst på sidan](#main)

</br>

---
### <a name="cdn-origin"></a>CDN-ursprung
CDN-ursprung matchar villkoret är uppfyllt när båda av följande villkor är uppfyllda:
- Innehåll från CDN storage begärdes.
- Begärande-URI använder typ av innehåll åtkomstpunkt (till exempel /000001) som definieras i den här matchningsvillkor:
  - CDN-URL: Begärande-URI måste innehålla den valda innehåll åtkomstpunkten.
  - Edge CNAME-URL: Motsvarande edge CNAME konfigurationen måste peka på den valda innehåll åtkomstpunkten.
  
Viktig information:
 - Innehåll åtkomstpunkt identifierar den tjänst som ska ha det begärda innehållet.
 - Använd inte en instruktion och om för att kombinera vissa matchningsvillkor. Kombinera ett matchningsvillkor för CDN-ursprung med ett matchningsvillkor för kunden skulle till exempel skapa ett matchningsmönster som aldrig kunde matchas. Därför kan inte två CDN-ursprung matchningsvillkor kombineras via en och om-instruktion.

[Överst på sidan](#main)

</br>

---
### <a name="client-ip-address"></a>IP-adress för klient
Den **matchningar**/**matchar inte** anger de villkor som matchar klientens IP-adress villkor uppfylls:
- **Matchningar**: Kräver att klientens IP-adressen matchar en av de angivna IP-adresserna. 
- **Matchar inte**: Kräver att klientens IP-adress inte matchar någon av de angivna IP-adresserna. 

Viktig information:
- Använda CIDR-notation.
- Ange flera IP-adresser och/eller IP-Adressblock genom att avgränsa dem med ett enda blanksteg. Exempel:
  - **IPv4-exempel**: 1.2.3.4 10.20.30.40 matchar alla begäranden som tas emot från adress 1.2.3.4 eller 10.20.30.40.
  - **IPv6-exempel**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matchar alla begäranden som tas emot från adress 1:2:3:4:5:6:7:8 eller 10:20:30:40:50:60:70:80.
- Syntaxen för ett IP-Adressblock är den IP-adress följt av ett snedstreck och prefixstorlek. Exempel:
  - **IPv4-exempel**: 5.5.5.64/26 matchar alla begäranden som tas emot från 5.5.5.64 via 5.5.5.127-adresser.
  - **IPv6-exempel**: 1:2:3: / 48 matchar alla begäranden som tas emot från adresser 1:2:3:0:0:0:0:0 via 1:2:3:ffff:ffff:ffff:ffff:ffff.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

[Överst på sidan](#main)

</br>

---
### <a name="cookie-parameter"></a>Cookie-Parameter
Den **matchningar**/**matchar inte** anger de villkor som matchar parametern Cookie villkor är uppfyllt.
- **Matchningar**: Kräver en begäran som innehåller den angivna cookien med ett värde som matchar minst en av de värden som definieras i den här matchningsvillkor.
- **Matchar inte**: Kräver att begäran uppfyller något av följande kriterier:
  - Den innehåller inte den angivna cookien.
  - Den innehåller den angivna cookien, men dess värde matchar inte någon av de värden som definieras i den här matchningsvillkor.
  
Viktig information:
- Cookie-namn: 
  - Eftersom värden för jokertecken, inklusive asterisker (*), inte stöds när du anger ett cookie-namn, är endast exakta cookie matchar berättigade för jämförelse.
  - Endast en enda cookie-namn kan anges per instans av den här matchningsvillkor.
  - Jämförelser för cookie-namn är skiftlägeskänsliga.
- Cookievärde: 
  - Ange flera cookie-värden genom att avgränsa dem med ett enda blanksteg.
  - Ett cookievärde kan dra nytta av [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values). 
  - Om ett jokerteckenvärde har angetts, att endast en exakt matchning uppfylla det här matchningsvillkor. Till exempel matchar anger ”Value” ”Value”, men inte ”Value1” eller ”Value2”.
  - Använd den **Ignorera skiftläge** alternativet att kontrollera om en skiftlägeskänslig jämförelse görs mot den begärda cookie-värdet.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

[Överst på sidan](#main)

</br>

---
### <a name="cookie-parameter-regex"></a>Cookie parametern Regex
Matchningsvillkor Cookie parametern Regex definierar en cookie-namn och värde. Du kan använda [reguljära uttryck](cdn-rules-engine-reference.md#regular-expressions) att definiera det önskade cookievärdet. 

Den **matchningar**/**matchar inte** anger de villkor som matchar Regex för Cookie-parametern villkor är uppfyllt.
- **Matchningar**: Kräver en begäran som innehåller den angivna cookien med ett värde som matchar det angivna reguljära uttrycket.
- **Matchar inte**: Kräver att begäran uppfyller något av följande kriterier:
  - Den innehåller inte den angivna cookien.
  - Den innehåller den angivna cookien, men dess värde matchar inte det angivna reguljära uttrycket.
  
Viktig information:
- Cookie-namn: 
  - Eftersom reguljära uttryck och jokertecken värden, inklusive asterisker (*), inte stöds när du anger ett cookie-namn, är endast exakta cookie matchar berättigade för jämförelse.
  - Endast en enda cookie-namn kan anges per instans av den här matchningsvillkor.
  - Jämförelser för cookie-namn är skiftlägeskänsliga.
- Cookievärde: 
  - Ett cookievärde kan dra nytta av reguljära uttryck.
  - Använd den **Ignorera skiftläge** alternativet att kontrollera om en skiftlägeskänslig jämförelse görs mot den begärda cookie-värdet.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

[Överst på sidan](#main)

</br>

---
### <a name="country"></a>Land/region
Du kan ange ett land/region via dess landskod. 

Den **matchningar**/**matchar inte** anger de villkor som matchar landet villkor uppfylls:
- **Matchningar**: Kräver begäran som innehåller värdena för angivna land. 
- **Matchar inte**: Kräver att begäran inte innehåller kodvärden angivna land.

Viktig information:
- Ange flera landskoder genom att avgränsa dem med ett enda blanksteg.
- Jokertecken stöds inte när du anger en landskod.
- Landskoder ”Europa” och ”Asien och Stillahavsområdet” inte omfatta alla IP-adresser i dessa regioner.
- Vissa begäranden kanske inte returnerar en giltig landskod. Ett frågetecken (?) kommer att matcha begäranden som en giltig landskod inte kunde fastställas.
- Landskoder är skiftlägeskänsliga.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementera Landsfiltrering med hjälp av regelmotorn
Den här matchningsvillkor kan du utföra en mängd olika anpassningar baserat på den plats som en begäran kommer från. Beteendet för funktionen Landsfiltrering kan exempelvis replikeras via följande konfiguration:

- Matchning med jokertecken URL-sökväg: Ange den [URL-sökväg med jokertecken matchningsvillkor](#url-path-wildcard) till den katalog som ska skyddas. 
    Lägg till en asterisk i slutet av den relativa sökvägen för att säkerställa att åtkomst till alla dess underordnade begränsas av den här regeln.

- Land matchning: Ange land matchningsvillkor för den önskade uppsättningen länder/regioner.
   - Tillåt: Ange land matchar villkoret **matchar inte** så att endast den angivna länder/regioner åtkomsten till innehåll som lagras på den plats som definieras av matchningsvillkor URL-sökväg med jokertecken.
   - Blockera: Ange land matchar villkoret **matchningar** att blockera angivna länder/regioner från att komma åt innehåll som lagras på den plats som definieras av matchningsvillkor URL-sökväg med jokertecken.

- Neka åtkomst (403)-funktionen: Aktivera den [neka åtkomst (403)-funktionen](cdn-rules-engine-reference-features.md#deny-access-403) att replikera den Tillåt eller blockera delen av funktionen Landsfiltrering.

[Överst på sidan](#main)

</br>

---
### <a name="customer-origin"></a>Kunden

Viktig information: 
- Kunden matchar villkoret är uppfyllt, oavsett om innehållet begärs via en CDN-URL eller en kant CNAME-URL som pekar till det valda kunden ursprunget.
- En konfiguration av customer ursprung som refereras av en regel kan inte tas bort från sidan kunden. Kontrollera att följande konfigurationer inte refererar till den innan du försöker ta bort en konfiguration av customer ursprung:
  - Ett matchningsvillkor för kunden
  - En edge CNAME-konfiguration
- Använd inte en instruktion och om för att kombinera vissa matchningsvillkor. Kombinera ett matchningsvillkor för kunden med ett matchningsvillkor för CDN-ursprung skulle till exempel skapa ett matchningsmönster som aldrig kunde matchas. Därför kan inte två kunden matchningsvillkor kombineras via en och om-instruktion.

[Överst på sidan](#main)

</br>

---
### <a name="device"></a>Enhet

Matchningsvillkor enhet identifierar begäranden som görs från en mobil enhet baserat på dess egenskaper. Identifiering av mobila enheter uppnås via [WURFL](http://wurfl.sourceforge.net/). 

Den **matchningar**/**matchar inte** anger de villkor som enheten matchar villkoret är uppfyllt:
- **Matchningar**: Kräver att beställaren enheten så att de matchar det angivna värdet. 
- **Matchar inte**: Kräver att den som begär enheten inte matchar det angivna värdet.

Viktig information:

- Använd den **Ignorera skiftläge** alternativet för att ange om det angivna värdet är skiftlägeskänsligt.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

#### <a name="string-type"></a>Strängtyp
En WURFL funktion accepterar vanligtvis en kombination av siffror, bokstäver och symboler. På grund av flexibla typen av den här funktionen måste du välja hur tolkas det värdet som är associerade med den här matchningsvillkor. I följande tabell beskrivs de tillgängliga uppsättningen alternativ:

Typ     | Beskrivning
---------|------------
Literal  | Välj det här alternativet för att förhindra att de flesta tecken tar på särskild innebörd med hjälp av deras [literalvärde](cdn-rules-engine-reference.md#literal-values).
Wildcard | Välj det här alternativet för att dra nytta av alla [jokertecken] ([jokertecken värden](cdn-rules-engine-reference.md#wildcard-values).
Regex    | Välj det här alternativet att använda [reguljära uttryck](cdn-rules-engine-reference.md#regular-expressions). Reguljära uttryck är användbara för att definiera ett mönster med tecken.

#### <a name="wurfl-capabilities"></a>WURFL funktioner
En funktion som WURFL refererar till en kategori som beskriver mobila enheter. Den valda kapaciteten avgör vilken typ av mobil enhetsbeskrivning som används för att identifiera begäranden.

I följande tabell visas WURFL funktioner och deras variabler för regelmotorn.
<br>
> [!NOTE] 
> Följande variabler stöds i den **ändra klienten begära huvud** och **ändra klienten svarshuvud** funktioner.

Funktion | Variabel | Beskrivning | Exempelvärden
-----------|----------|-------------|----------------
Varumärke | %{wurfl_cap_brand_name} | En sträng som anger varumärke namnet på enheten. | Samsung
Enhetens OS | %{wurfl_cap_device_os} | En sträng som anger att operativsystemet har installerats på enheten. | IOS
Operativsystemsversion för enhet | %{wurfl_cap_device_os_version} | En sträng som anger versionsnumret för operativsystemet installerat på enheten. | 1.0.1
Dubbel orientering | %{wurfl_cap_dual_orientation} | Ett booleskt värde som anger om enheten har stöd för dubbel orientering. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | En sträng som anger den mobilenheter önskade dokumentet typdefinition (DTD) för HTML-innehåll. | inga<br/>xhtml_basic<br/>html5
Bild Inlining | %{wurfl_cap_image_inlining} | Ett booleskt värde som anger om enheten har stöd för Base64-kodad avbildningar. | false
Is Android | %{wurfl_vcap_is_android} | Ett booleskt värde som anger om enheten använder Android OS. | true
Är IOS | %{wurfl_vcap_is_ios} | Ett booleskt värde som anger om enheten använder iOS. | false
Är Smart TV | %{wurfl_cap_is_smarttv} | Ett booleskt värde som anger om enheten är en smart-TV. | false
Är Smartphone | %{wurfl_vcap_is_smartphone} | Ett booleskt värde som anger om enheten är en smartphone. | true
Är surfplatta | % {wurfl_cap_is_tablet} | Ett booleskt värde som anger om enheten är en surfplatta. Den här beskrivningen är oberoende av Operativsystemet. | true
Är trådlösa enhet | %{wurfl_cap_is_wireless_device} | Ett booleskt värde som anger om enheten betraktas som en trådlös enhet. | true
Marknadsföring namn | %{wurfl_cap_marketing_name} | En sträng som anger enhetens marknadsföring namn. | BlackBerry 8100 Pearl
Mobila webbläsare | %{wurfl_cap_mobile_browser} | En sträng som anger i webbläsare som används för att begära innehåll från enheten. | Chrome
Mobila webbläsarversion | %{wurfl_cap_mobile_browser_version} | En sträng som anger versionen av webbläsaren som används för att begära innehåll från enheten. | 31
Modellnamn | %{wurfl_cap_model_name} | En sträng som anger enhetens modellnamn. | s3
Progressiv nedladdning | %{wurfl_cap_progressive_download} | Ett booleskt värde som anger om enheten har stöd för uppspelning av ljud och video medan den fortfarande laddas ned. | true
Utgivningsdatum | %{wurfl_cap_release_date} | En sträng som anger år och månad då enheten lades till WURFL-databasen.<br/><br/>Format: `yyyy_mm` | 2013_december
Lösning höjd | %{wurfl_cap_resolution_height} | Ett heltal som anger enhetens höjd i bildpunkter. | 768
Lösning bredd | %{wurfl_cap_resolution_width} | Ett heltal som anger enhetens bredd i bildpunkter. | 1024

[Överst på sidan](#main)

</br>

---
### <a name="edge-cname"></a>Edge Cname
Viktig information: 
- Listan över tillgängliga edge CNAME-poster är begränsad till dessa edge CNAME-poster som har konfigurerats på sidan Edge CNAME-poster för den plattform som regelmotorn konfigureras.
- Innan du försöker ta bort en edge CNAME-konfiguration, se till att referera den inte av ett matchningsvillkor för Edge Cname. Edge CNAME konfigurationer som har definierats i en regel kan inte tas bort från sidan Edge CNAME-poster. 
- Använd inte en instruktion och om för att kombinera vissa matchningsvillkor. Kombinera en Edge-Cname matchningsvillkor med ett matchningsvillkor för kunden skulle till exempel skapa ett matchningsmönster som aldrig kunde matchas. Därför kan inte två Edge Cname matchningsvillkor kombineras via en och om-instruktion.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

[Överst på sidan](#main)

</br>

---
### <a name="referring-domain"></a>Refererande domän
Värdnamnet som är associerade med referent genom vilket innehåll begärdes avgör om refererar domän villkor är uppfyllt. 

Den **matchningar**/**matchar inte** anger de villkor som matchar domänen refererar villkor uppfylls:
- **Matchningar**: Kräver refererande värdnamnet som matchar de angivna värdena. 
- **Matchar inte**: Kräver att refererande värdnamnet inte matchar det angivna värdet.

Viktig information:
- Ange flera värdnamn genom att avgränsa dem med ett enda blanksteg.
- Har stöd för den här matchningsvillkor [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values).
- Om det angivna värdet inte innehåller en asterisk, måste det vara en exakt matchning för den referent värdnamn. Till exempel skulle anger ”mydomain.com” inte matchar ”www.mydomain.com”.
- Använd den **Ignorera skiftläge** alternativet att kontrollera om en skiftlägeskänslig jämförelse görs.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

[Överst på sidan](#main)

</br>

---
### <a name="request-header-literal"></a>Begära huvud Literal
Den **matchningar**/**matchar inte** anger de villkor som matchar den begära huvud Literal villkor är uppfyllt.
- **Matchningar**: Kräver begäran som innehåller det angivna huvudet. Värdet måste matcha det som definieras i den här matchningsvillkor.
- **Matchar inte**: Kräver att begäran uppfyller något av följande kriterier:
  - Den innehåller inte det angivna huvudet.
  - Den innehåller det angivna huvudet, men dess värde matchar inte det som definieras i den här matchningsvillkor.
  
Viktig information:
- Rubriken namn jämförelser är alltid skiftlägeskänsliga. Använd den **Ignorera skiftläge** möjlighet att styra jämförelser av rubriken värde skiftläge.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

[Överst på sidan](#main)

</br>

---
### <a name="request-header-regex"></a>Begära huvud Regex
Den **matchningar**/**matchar inte** anger de villkor som matchar Regex begära huvud villkor är uppfyllt.
- **Matchningar**: Kräver begäran som innehåller det angivna huvudet. Värdet måste matcha mönstret som definieras i den angivna [reguljärt uttryck](cdn-rules-engine-reference.md#regular-expressions).
- **Matchar inte**: Kräver att begäran uppfyller något av följande kriterier:
  - Den innehåller inte det angivna huvudet.
  - Den innehåller det angivna huvudet, men dess värde matchar inte det angivna reguljära uttrycket.

Viktig information:
- Huvudnamn: 
  - Rubriken namn jämförelser är skiftlägeskänsliga.
  - Ersätt blanksteg i namnet med ”% 20”. 
- Huvudets värde: 
  - Ett huvudvärde kan dra nytta av reguljära uttryck.
  - Använd den **Ignorera skiftläge** möjlighet att styra jämförelser av rubriken värde skiftläge.
  - Matchar villkoret är uppfyllt endast när ett huvudvärde exakt matchar minst en av de angivna mönster.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell 

[Överst på sidan](#main)

</br>

---
### <a name="request-header-wildcard"></a>Begära huvud med jokertecken
Den **matchningar**/**matchar inte** anger de villkor som matchar jokertecknet begära huvud villkor är uppfyllt.
- **Matchningar**: Kräver begäran som innehåller det angivna huvudet. Värdet måste matcha minst en av de värden som definieras i den här matchningsvillkor.
- **Matchar inte**: Kräver att begäran uppfyller något av följande kriterier:
  - Den innehåller inte det angivna huvudet.
  - Den innehåller det angivna huvudet, men dess värde matchar inte någon av de angivna värdena.
  
Viktig information:
- Huvudnamn: 
  - Rubriken namn jämförelser är skiftlägeskänsliga.
  - Blanksteg i namnet ska ersättas med ”% 20”. Du kan också använda det här värdet för att ange blanksteg i en huvudets värde.
- Huvudets värde: 
  - Ett huvudvärde kan dra nytta av [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values).
  - Använd den **Ignorera skiftläge** möjlighet att styra jämförelser av rubriken värde skiftläge.
  - Den här matchar villkoret är uppfyllt när ett huvudvärde matchar exakt till minst en av de angivna mönster.
  - Ange flera värden genom att avgränsa dem med ett enda blanksteg.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

[Överst på sidan](#main)

</br>

---
### <a name="request-method"></a>Begärandemetod
Metod för begäran matchar villkoret är uppfyllt endast när tillgångar begärs via metoden för valda begäran. Begäran om tillgängliga metoderna är:
- HÄMTA
- HEAD 
- POST 
- ALTERNATIV 
- PUT 
- DELETE 
- SPÅRNING 
- ANSLUT 

Viktig information:
- Som standard kan endast GET-begäran-metod generera cachelagrat innehåll i nätverket. Alla övriga metodbegäranden är via proxy via nätverket.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

[Överst på sidan](#main)

</br>

---
### <a name="request-scheme"></a>Begäran-schema
Begär schema matchar villkoret är uppfyllt endast när tillgångar begärs via det valda protokollet. Tillgängliga protokoll är: 
- HTTP
- HTTPS

Viktig information:
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

[Överst på sidan](#main)

</br>

---
### <a name="url-path-directory"></a>URL-sökväg-katalog
Identifierar en förfrågan av relativ sökväg, vilket utesluter filnamnet för den begärda tillgången.

Den **matchningar**/**matchar inte** anger de villkor som katalogen URL-sökvägen matchar villkoret är uppfyllt.
- **Matchningar**: Kräver begäran som innehåller en relativ URL-sökväg, exklusive filnamn som matchar det angivna URL-mönstret.
- **Matchar inte**: Kräver begäran som innehåller en relativ URL-sökväg, exklusive filnamn som inte matchar det angivna URL-mönstret.

Viktig information:
- Använd den **relativt** alternativet om du vill ange att URL: en jämförelse startas före eller efter punkten för åtkomst till innehåll. Åtkomst till innehåll-platsen är del av sökvägen som visas mellan Verizon CDN-värdnamn och den relativa sökvägen till den begärda tillgången (till exempel /800001/CustomerOrigin). Den identifierar en plats med servertyp (till exempel CDN eller kund ursprungliga) och din kund kontonamn.

   Följande värden är tillgängliga för den **relativt** alternativet:
   - **Rot**: Anger att den URL: en jämförelse punkten börjar direkt efter CDN-värdnamn. 

     Till exempel: http:\//wpc.0001.&lt; domän&gt;/**800001/myorigin/MinMapp**/index.htm

   - **Origin**: Anger att den URL: en jämförelse punkten börjar när du har åtkomst till innehåll punkten (till exempel /000001 eller/800001/myorigin). Eftersom den \*. azureedge.net CNAME har skapats i förhållande till den ursprungliga katalogen på Verizon CDN-värdnamn som standard, Azure CDN-användare bör använda den **ursprung** värde. 

     Till exempel: https:\//&lt;endpoint&gt;.azureedge.net/**MinMapp**/index.htm 

     Den här URL: en pekar till följande Verizon CDN-värdnamn: http:\//wpc.0001.&lt; domän&gt;/800001/myorigin/**MinMapp**/index.htm

- En kant CNAME URL: en skrivs om till en CDN-URL innan du URL: en jämförelse.

    Exempelvis kan båda av följande webbadresser peka på samma tillgång och därför har samma URL-sökvägen.
  - CDN-URL: http:\//wpc.0001.&lt; domän&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME-URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Ytterligare information:
  - Anpassad domän: https:\//my.domain.com/path/asset.htm
    
  - URL-sökväg (i förhållande till rot): / 800001/CustomerOrigin/path /
    
  - URL-sökväg (i förhållande till ursprunget): /path/

- Del av URL: en som används för URL: en jämförelse av ends precis före filnamnet för den begärda tillgången. Ett avslutande snedstreck är det sista tecknet i den här typen av sökvägen.
    
- Ersätt utrymmen i mönstret för URL-sökväg med ”% 20”.
    
- Varje mönster för URL-sökvägen kan innehålla en eller flera asterisker (*), där varje asterisk matchar en sekvens med ett eller flera tecken.
    
- Ange flera URL-sökvägar i mönstret genom att avgränsa dem med ett enda blanksteg.

    Till exempel: * /sales/ * /marketing/

- En URL-sökvägen kan dra nytta av [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values).

- Använd den **Ignorera skiftläge** alternativet att kontrollera om en skiftlägeskänslig jämförelse.

[Överst på sidan](#main)

</br>

---
### <a name="url-path-extension"></a>URL-sökväg-tillägget
Identifierar begäranden genom att filnamnstillägget för den begärda tillgången.

Den **matchningar**/**matchar inte** anger de villkor som URL-sökväg-tillägget matchar villkoret är uppfyllt.
- **Matchningar**: Kräver URL för begäran som innehåller ett filnamnstillägg som exakt matchar det angivna mönstret.

   Exempel: Om du anger ”htm”, ”htm” tillgångar matchas, men inte ”html” tillgångar.  

- **Matchar inte**: Kräver URL-begäran som innehåller ett filnamnstillägg som inte matchar det angivna mönstret.

Viktig information:
- Ange de filnamnstillägg som matchar i den **värdet** box. Ta inte med en inledande punkt; till exempel använda htm i stället för .htm.

- Använd den **Ignorera skiftläge** alternativet att kontrollera om en skiftlägeskänslig jämförelse.

- Ange flera filnamnstillägg genom att avgränsa varje tillägg med ett enda blanksteg. 

    Till exempel: htm html

- Till exempel matchar anger ”htm” ”htm” tillgångar, men inte ”html” tillgångar.


#### <a name="sample-scenario"></a>Exempelscenario

Följande exempelkonfiguration förutsätter att den här matchar villkoret är uppfyllt när en begäran matchar ett av de angivna tillägg.

Värdet specifikationen: asp aspx php html

Den här matchar villkoret är uppfyllt när den hittar URL: er som slutar med följande filtillägg:
- .asp
- .aspx
- .php
- .html

[Överst på sidan](#main)

</br>

---
### <a name="url-path-filename"></a>URL: en sökväg filnamn
Identifierar begäranden genom att filnamnet för den begärda tillgången. För den här matchningsvillkor, ett filnamn som består av namnet på den begärda tillgången, en period och filnamnstillägget (till exempel index.html).

Den **matchningar**/**matchar inte** anger de villkor som URL-sökväg filnamn matchar villkoret är uppfyllt.
- **Matchningar**: Kräver begäran som innehåller ett filnamn i dess URL-sökvägen som matchar det angivna mönstret.
- **Matchar inte**: Kräver begäran som innehåller ett filnamn i dess URL-sökvägen som inte matchar det angivna mönstret.

Viktig information:
- Använd den **Ignorera skiftläge** alternativet att kontrollera om en skiftlägeskänslig jämförelse.

- Avgränsa varje tillägg för att ange flera filnamnstillägg, med ett enda blanksteg.

    Till exempel: index.htm index.html

- Ersätt blanksteg i en namnvärde med ”% 20”.
    
- Ett namnvärde kan dra nytta av [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values). Varje filnamnsmönster kan exempelvis bestå av en eller flera asterisker (*), där varje asterisk matchar en sekvens med ett eller flera tecken.
    
- Om jokertecken får inte anges, att endast en exakt matchning uppfylla det här matchningsvillkor.

    Till exempel matchar anger ”presentation.ppt” en tillgång med namnet ”presentation.ppt”, men inte en namngiven ”presentation.pptx”.

[Överst på sidan](#main)

</br>

---
### <a name="url-path-literal"></a>URL-sökväg Literal
Jämför en begäran URL-sökvägen, inklusive filnamnet, med det angivna värdet.

Den **matchningar**/**matchar inte** anger de villkor som matchar URL-sökväg-Literal villkor är uppfyllt.
- **Matchningar**: Kräver begäran som innehåller en URL-sökväg som matchar det angivna mönstret.
- **Matchar inte**: Kräver begäran som innehåller en URL-sökväg som inte matchar det angivna mönstret.

Viktig information:
- Använd den **relativt** alternativet för att ange om den URL: en jämförelse inleder före eller efter punkten för åtkomst till innehåll. 

    Följande värden är tillgängliga för den **relativt** alternativet:
  - **Rot**: Anger att den URL: en jämförelse punkten börjar direkt efter CDN-värdnamn.

    Till exempel: http:\//wpc.0001.&lt; domän&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Anger att den URL: en jämförelse punkten börjar när du har åtkomst till innehåll punkten (till exempel /000001 eller/800001/myorigin). Eftersom den \*. azureedge.net CNAME har skapats i förhållande till den ursprungliga katalogen på Verizon CDN-värdnamn som standard, Azure CDN-användare bör använda den **ursprung** värde. 

    Till exempel: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

    Den här URL: en pekar till följande Verizon CDN-värdnamn: http:\//wpc.0001.&lt; domän&gt;/800001/myorigin/**myfolder/index.htm**

- En kant CNAME URL: en skrivs om till en CDN-URL innan en jämförelse av en URL.

    Exempelvis kan båda av följande webbadresser pekar på samma tillgång och därför har samma URL-sökväg:
  - CDN-URL: http:\//wpc.0001.&lt; domän&gt;/800001/CustomerOrigin/path/asset.htm
  - Edge CNAME-URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Ytterligare information:
    
  - URL-sökväg (i förhållande till rot): /800001/CustomerOrigin/path/asset.htm
   
  - URL-sökväg (i förhållande till ursprunget): /path/asset.htm

- Frågesträngar i URL: en ignoreras.
- Använd den **Ignorera skiftläge** alternativet att kontrollera om en skiftlägeskänslig jämförelse.
- Det angivna värdet för den här matchningsvillkor jämföras med den relativa sökvägen för exakta begäran från klienten.

- Om du vill matcha alla begäranden som görs till en viss katalog, använda den [URL-sökväg-katalog](#url-path-directory) eller [URL-sökväg med jokertecken](#url-path-wildcard) matchningsvillkor.

[Överst på sidan](#main)

</br>

---
### <a name="url-path-regex"></a>URL-sökväg Regex
Jämför en begäran URL-sökvägen till den angivna [reguljärt uttryck](cdn-rules-engine-reference.md#regular-expressions).

Den **matchningar**/**matchar inte** anger de villkor som matchar Regex för URL-sökvägen villkor är uppfyllt.
- **Matchningar**: Kräver begäran som innehåller en URL-sökväg som matchar det angivna reguljära uttrycket.
- **Matchar inte**: Kräver begäran som innehåller en URL-sökväg som inte matchar det angivna reguljära uttrycket.

Viktig information:
- En kant CNAME URL: en skrivs om till en CDN-URL innan du URL: en jämförelse. 
 
    Båda URL: er om du vill peka på samma tillgång och därför har samma URL-sökvägen.

  - CDN-URL: http:\//wpc.0001.&lt; domän&gt;/800001/CustomerOrigin/path/asset.htm

  - Edge CNAME URL: http:\//my.domain.com/path/asset.htm
    
    Ytterligare information:
    
  - URL-sökväg: /800001/CustomerOrigin/path/asset.htm

- Frågesträngar i URL: en ignoreras.
    
- Använd den **Ignorera skiftläge** alternativet att kontrollera om en skiftlägeskänslig jämförelse.
    
- Blanksteg i URL-sökvägen ska ersättas med ”% 20”.

[Överst på sidan](#main)

</br>

---
### <a name="url-path-wildcard"></a>URL-sökväg med jokertecken
Jämför en begäran relativa URL-sökvägen till den angivna jokerteckensmönster.

Den **matchningar**/**matchar inte** anger de villkor som matchar jokertecknet URL-sökvägen villkor är uppfyllt.
- **Matchningar**: Kräver begäran som innehåller en URL-sökväg som matchar de angivna jokerteckensmönster.
- **Matchar inte**: Kräver begäran som innehåller en URL-sökväg som inte matchar de angivna jokerteckensmönster.

Viktig information:
- **Relativt** alternativet: Det här alternativet avgör om den URL: en jämförelse punkten börjar före eller efter punkten för åtkomst till innehåll.

   Det här alternativet kan ha följande värden:
  - **Rot**: Anger att den URL: en jämförelse punkten börjar direkt efter CDN-värdnamn.

    Till exempel: http:\//wpc.0001.&lt; domän&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Anger att den URL: en jämförelse punkten börjar när du har åtkomst till innehåll punkten (till exempel /000001 eller/800001/myorigin). Eftersom den \*. azureedge.net CNAME har skapats i förhållande till den ursprungliga katalogen på Verizon CDN-värdnamn som standard, Azure CDN-användare bör använda den **ursprung** värde. 

    Till exempel: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

    Den här URL: en pekar till följande Verizon CDN-värdnamn: http:\//wpc.0001.&lt; domän&gt;/800001/myorigin/**myfolder/index.htm**

- En kant CNAME URL: en skrivs om till en CDN-URL innan du URL: en jämförelse.

    Exempelvis kan båda av följande webbadresser pekar på samma tillgång och därför har samma URL-sökväg:
  - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
  - Edge CNAME-URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Ytterligare information:
    
  - URL-sökväg (i förhållande till rot): /800001/CustomerOrigin/path/asset.htm
    
  - URL-sökväg (i förhållande till ursprunget): /path/asset.htm
    
- Ange flera URL-sökvägar genom att avgränsa dem med ett enda blanksteg.

   Till exempel: /marketing/asset.* /sales/*.htm

- Frågesträngar i URL: en ignoreras.
    
- Använd den **Ignorera skiftläge** alternativet att kontrollera om en skiftlägeskänslig jämförelse.
    
- Ersätt blanksteg i URL-sökvägen med ”% 20”.
    
- Värdet som angetts för en URL-sökväg kan dra nytta av [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values). Varje mönster för URL-sökvägen kan innehålla en eller flera asterisker (*), där varje asterisk kan matcha en sekvens av ett eller flera tecken.

#### <a name="sample-scenarios"></a>Exempelscenarier

Exempel-konfigurationer i tabellen nedan förutsätter att den här matchar villkoret är uppfyllt när en begäran matchar det angivna URL-mönstret:

Värde                   | I förhållande till    | Resultat 
------------------------|----------------|-------
*/test.html */test.php  | Rot- eller ursprung | Det här mönstret matchas av begäranden för tillgångar med namnet ”test.html” eller ”test.php” i valfri mapp.
/80ABCD/origin/text/*   | Rot           | Det här mönstret matchas när den begärda tillgången uppfyller följande kriterier: <br />-Det måste finnas på en kund ursprung som kallas ”ursprung”. <br />-Den relativa sökvägen måste börja med en mapp med namnet ”text”. Den begärda tillgången kan det vill säga antingen finnas i mappen ”text” eller en av dess rekursiv undermappar.
*/CSS/* */js/*          | Rot- eller ursprung | Det här mönstret matchas av alla CDN eller edge CNAME-adresser som innehåller en css- eller js-mapp.
*.jpg *.gif *.png       | Rot- eller ursprung | Det här mönstret matchas av alla CDN eller edge CNAME URL: er slutar med .jpg, .gif, eller .png. Ett annat sätt att ange det här mönstret är med i [URL-sökväg-tillägget matchningsvillkor](#url-path-extension).
/ bilder / * / media / *      | Ursprung         | Det här mönstret matchas av CDN- eller kanttabell CNAME URL: er vars relativ sökväg som börjar med en ”avbildningar” eller ”media”-mapp. <br />-CDN URL: http:\//wpc.0001.&lt; domän&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Överst på sidan](#main)

</br>

---
### <a name="url-query-literal"></a>URL: en fråga Literal
Jämför frågesträng för en begäran till det angivna värdet.

Den **matchningar**/**matchar inte** anger de villkor som matchar URL: en fråga Literal villkor är uppfyllt.
- **Matchningar**: Kräver begäran som innehåller en URL-frågesträngen som matchar den angivna frågesträngen.
- **Matchar inte**: Kräver begäran som innehåller en URL-frågesträngen som inte matchar den angivna frågesträngen.

Viktig information:

- Endast exakta fråga strängen matchar uppfyller den här matchningsvillkor.
    
- Använd den **Ignorera skiftläge** möjlighet att styra fråga strängjämförelser skiftläge.
    
- Inkludera inte ett ledande frågetecken (?) i frågetexten sträng värde.
    
- Vissa tecken kräver URL-kodning. Använd symbolen procent till URL: en koda följande tecken:

   Tecken | URL-kodning
   ----------|---------
   Rymd     | %20
   &         | %25

- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
   - Slutför Cache fyllning
   - Standard interna maxåldern
   - Tvinga interna maxåldern
   - Ignore Origin No-Cache
   - Intern Max-inaktuell

[Överst på sidan](#main)

</br>

---
### <a name="url-query-parameter"></a>URL: en frågeparameter
Identifierar förfrågningar som innehåller den angivna parametern för frågesträngen. Den här parametern anges till ett värde som matchar ett specifikt mönster. Fråga strängparametrar (till exempel parametern = värde) i URL: en avgöra om det här villkoret är uppfyllt begäran. Den här matchningsvillkor identifierar en frågesträngsparameter efter dess namn och godkänner ett eller flera värden för parametervärdet. 

Den **matchningar**/**matchar inte** anger de villkor som Frågeparametern URL: en matchar villkoret är uppfyllt.
- **Matchningar**: Kräver en begäran som innehåller den angivna parametern med ett värde som matchar minst en av de värden som definieras i den här matchningsvillkor.
- **Matchar inte**: Kräver att begäran uppfyller något av följande kriterier:
  - Den innehåller inte den angivna parametern.
  - Den innehåller den angivna parametern, men dess värde matchar inte någon av de värden som definieras i den här matchningsvillkor.

Den här matchningsvillkor ger ett enkelt sätt att ange parametern namn/värde-kombinationer. För mer flexibilitet om du matchar en frågesträngsparameter, bör du använda den [URL: en fråga med jokertecken](#url-query-wildcard) matchningsvillkor.

Viktig information:
- Endast en enskild URL: en fråga parameternamnet kan anges per instans av den här matchningsvillkor.
    
- Eftersom värden för jokertecken inte stöds när ett parameternamn har angetts, är endast exakta parametern matchar berättigade för jämförelse.
- Parametern-värden kan innehålla [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values).
   - Varje mönster för parametern-värdet kan bestå av en eller flera asterisker (*), där varje asterisk kan matcha en sekvens av ett eller flera tecken.
   - Vissa tecken kräver URL-kodning. Använd symbolen procent till URL: en koda följande tecken:

       Tecken | URL-kodning
       ----------|---------
       Rymd     | %20
       &         | %25

- Ange parametern flera frågesträngsvärden genom att avgränsa dem med ett enda blanksteg. Den här matchar villkoret är uppfyllt när en begäran innehåller ett av de angivna namn/värde-kombinationerna.

   - Exempel 1:

     - Konfiguration:

       ValueA ValueB

     - Den här konfigurationen matchar följande frågesträngparametrarna:

       Parameter1 = Värdea
    
       Parameter1=ValueB

   - Exempel 2:

     - Konfiguration: 

        Värdet % 20A värdet % 20B

     - Den här konfigurationen matchar följande frågesträngparametrarna:

       Parameter1=Value%20A

       Parameter1 = värde % 20B

- Den här matchar villkoret är uppfyllt när det finns en exakt matchning i minst en av de angivna fråga sträng namn/värde-kombinationerna.

   Till exempel om du använder konfigurationen i exemplet ovan, parametern namn/värde kombination ”Parameter1 = ValueAdd” kan inte betraktas som en matchning. Om du anger något av följande värden kan matchar det dock kombinationen av namn/värde:

   - Värdea Värdeb ValueAdd
   - ValueA* ValueB

- Använd den **Ignorera skiftläge** möjlighet att styra fråga strängjämförelser skiftläge.
    
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
   - Slutför Cache fyllning
   - Standard interna maxåldern
   - Tvinga interna maxåldern
   - Ignore Origin No-Cache
   - Intern Max-inaktuell

#### <a name="sample-scenarios"></a>Exempelscenarier
I följande exempel visar hur det här alternativet fungerar i vissa situationer:

Namn  | Värde |  Resultat
------|-------|--------
Användare  | Joe   | Det här mönstret matchas när frågesträngen för en begärd URL är ”? user = joe”.
Användare  | *     | Det här mönstret matchas när frågesträngen för en begärd URL innehåller en parameter för användaren.
E-post | Joe\* | Det här mönstret matchas när frågesträngen för en begärd URL innehåller en parameter för e-post som börjar med ”Johan”.

[Överst på sidan](#main)

</br>

---
### <a name="url-query-regex"></a>URL: en fråga Regex
Identifierar förfrågningar som innehåller den angivna parametern för frågesträngen. Den här parametern anges till ett värde som matchar en angiven [reguljärt uttryck](cdn-rules-engine-reference.md#regular-expressions).

Den **matchningar**/**matchar inte** anger de villkor som matchar Regex för URL-frågan villkor är uppfyllt.
- **Matchningar**: Kräver begäran som innehåller en URL-frågesträngen som matchar det angivna reguljära uttrycket.
- **Matchar inte**: Kräver begäran som innehåller en URL-frågesträngen som inte matchar det angivna reguljära uttrycket.

Viktig information:
- Endast uppfyller exakta matchningar till det angivna reguljära uttrycket den här matchningsvillkor.
    
- Använd den **Ignorera skiftläge** möjlighet att styra fråga strängjämförelser skiftläge.
    
- För det här alternativet börjar en frågesträng med det första tecknet efter frågetecken (?) avgränsare för frågesträngen.
    
- Vissa tecken kräver URL-kodning. Använd symbolen procent till URL: en koda följande tecken:

   Tecken | URL-kodning | Värde
   ----------|--------------|------
   Rymd     | %20          | \%20
   &         | %25          | \%25

   Observera att procent symboler måste undantas.

- Särskilda reguljärt uttryck för Double-escape-tecken (till exempel \^$. +) att inkludera ett omvänt snedstreck i det reguljära uttrycket.

   Exempel:

   Värde | Tolkas som 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
   - Slutför Cache fyllning
   - Standard interna maxåldern
   - Tvinga interna maxåldern
   - Ignore Origin No-Cache
   - Intern Max-inaktuell


[Överst på sidan](#main)

</br>

---
### <a name="url-query-wildcard"></a>URL: en fråga med jokertecken
Jämför de angivna värdena mot frågesträngen i begäran.

Den **matchningar**/**matchar inte** anger de villkor som jokertecken för URL-frågan matchar villkoret är uppfyllt.
- **Matchningar**: Kräver begäran som innehåller en URL-frågesträngen som matchar det angivna jokertecken-värdet.
- **Matchar inte**: Kräver begäran som innehåller en URL-frågesträngen som inte matchar det angivna jokertecken-värdet.

Viktig information:
- För det här alternativet börjar en frågesträng med det första tecknet efter frågetecken (?) avgränsare för frågesträngen.
- Parametervärden kan innehålla [jokertecken värden](cdn-rules-engine-reference.md#wildcard-values):
   - Varje mönster för parametern-värdet kan bestå av en eller flera asterisker (*), där varje asterisk kan matcha en sekvens av ett eller flera tecken.
   - Vissa tecken kräver URL-kodning. Använd symbolen procent till URL: en koda följande tecken:

     Tecken | URL-kodning
     ----------|---------
     Rymd     | %20
     &         | %25

- Ange flera värden genom att avgränsa dem med ett enda blanksteg.

   Exempel: *Parameter1 = Värdea* *Värdeb* *Parameter1 = ValueC & Parameter2 = värdefull*

- Endast uppfyller exakta matchningar till minst en av de angivna sträng-frågemönstren den här matchningsvillkor.
    
- Använd den **Ignorera skiftläge** möjlighet att styra fråga strängjämförelser skiftläge.
    
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
   - Slutför Cache fyllning
   - Standard interna maxåldern
   - Tvinga interna maxåldern
   - Ignore Origin No-Cache
   - Intern Max-inaktuell

#### <a name="sample-scenarios"></a>Exempelscenarier
I följande exempel visar hur det här alternativet fungerar i vissa situationer:

 Namn                 | Beskrivning
 ---------------------|------------
User = joe              | Det här mönstret matchas när frågesträngen för en begärd URL är ”? user = joe”.
\*användare =\* \*utdatanivå =\* | Det här mönstret matchas när CDN-URL-frågan innehåller antingen användar- eller utdatanivå-parametern.

[Överst på sidan](#main)

</br>

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure Content Delivery Network](cdn-overview.md)
* [Regelmotor – referens](cdn-rules-engine-reference.md)
* [Regelmotor – villkorliga uttryck](cdn-rules-engine-reference-conditional-expressions.md)
* [Regelmotor – funktioner](cdn-rules-engine-reference-features.md)
* [Åsidosätta standardbeteendet för HTTP med regelmotorn](cdn-rules-engine.md)

