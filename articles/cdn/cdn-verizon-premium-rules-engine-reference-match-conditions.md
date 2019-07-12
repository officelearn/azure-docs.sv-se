---
title: Azure CDN från Verizon Premium regelmotor – matchningsvillkor | Microsoft Docs
description: Referensdokumentation för Azure Content Delivery Network från Verizon Premium regelmotor – matchningsvillkor.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593206"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN från Verizon Premium regelmotor matchar de villkor

Den här artikeln innehåller detaljerade beskrivningar av tillgängliga matchningsvillkor för Azure Content Delivery Network (CDN) och som har från Verizon Premium [regelmotor](cdn-verizon-premium-rules-engine.md).

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

---

### <a name="always"></a>Alltid

Matchningsvillkor alltid gäller en standarduppsättning med funktioner för alla begäranden.

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Klientens IP-adress

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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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
  - Ett cookievärde kan dra nytta av [jokertecken värden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Om ett jokerteckenvärde har angetts, att endast en exakt matchning uppfylla det här matchningsvillkor. Till exempel matchar anger ”Value” ”Value”, men inte ”Value1” eller ”Value2”.
  - Använd den **Ignorera skiftläge** alternativet att kontrollera om en skiftlägeskänslig jämförelse görs mot den begärda cookie-värdet.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

[Överst på sidan](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Cookie parametern Regex

Matchningsvillkor Cookie parametern Regex definierar en cookie-namn och värde. Du kan använda [reguljära uttryck](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) att definiera det önskade cookievärdet.

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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Country

Du kan ange ett land via dess landskod. 

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

- Land matchning: Ange land matchningsvillkor för den önskade uppsättningen länder.
  - Tillåt: Ange land matchar villkoret **matchar inte** så att endast den angivna länder åtkomsten till innehåll som lagras på den plats som definieras av matchningsvillkor URL-sökväg med jokertecken.
  - Blockera: Ange land matchar villkoret **matchningar** att blockera de angivna länder/regioner från att komma åt innehåll som lagras på den plats som definieras av matchningsvillkor URL-sökväg med jokertecken.

- Neka åtkomst (403)-funktionen: Aktivera den [neka åtkomst (403)-funktionen](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) att replikera den Tillåt eller blockera delen av funktionen Landsfiltrering.

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Kunden

Viktig information:

- Kunden matchar villkoret är uppfyllt, oavsett om innehållet begärs via en CDN-URL eller en kant CNAME-URL som pekar till det valda kunden ursprunget.
- En konfiguration av customer ursprung som refereras av en regel kan inte tas bort från sidan kunden. Kontrollera att följande konfigurationer inte refererar till den innan du försöker ta bort en konfiguration av customer ursprung:
  - Ett matchningsvillkor för kunden
  - En edge CNAME-konfiguration
- Använd inte en instruktion och om för att kombinera vissa matchningsvillkor. Kombinera ett matchningsvillkor för kunden med ett matchningsvillkor för CDN-ursprung skulle till exempel skapa ett matchningsmönster som aldrig kunde matchas. Därför kan inte två kunden matchningsvillkor kombineras via en och om-instruktion.

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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

type     | Beskrivning
---------|------------
Literal  | Välj det här alternativet för att förhindra att de flesta tecken tar på särskild innebörd med hjälp av deras [literalvärde](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Välj det här alternativet för att dra nytta av alla [jokertecken] ([jokertecken värden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Välj det här alternativet att använda [reguljära uttryck](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Reguljära uttryck är användbara för att definiera ett mönster med tecken.

#### <a name="wurfl-capabilities"></a>WURFL funktioner

En funktion som WURFL refererar till en kategori som beskriver mobila enheter. Den valda kapaciteten avgör vilken typ av mobil enhetsbeskrivning som används för att identifiera begäranden.

I följande tabell visas WURFL funktioner och deras variabler för regelmotorn.

> [!NOTE]
> Följande variabler stöds i den **ändra klienten begära huvud** och **ändra klienten svarshuvud** funktioner.

Funktion | Variabel | Beskrivning | Exempelvärden
-----------|----------|-------------|----------------
Varumärke | %{wurfl_cap_brand_name} | En sträng som anger varumärke namnet på enheten. | Samsung
Enhetens OS | %{wurfl_cap_device_os} | En sträng som anger att operativsystemet har installerats på enheten. | IOS
Enhetens operativsystemversion | %{wurfl_cap_device_os_version} | En sträng som anger versionsnumret för operativsystemet installerat på enheten. | 1.0.1
Dubbel orientering | %{wurfl_cap_dual_orientation} | Ett booleskt värde som anger om enheten har stöd för dubbel orientering. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | En sträng som anger den mobilenheter önskade dokumentet typdefinition (DTD) för HTML-innehåll. | Ingen<br/>xhtml_basic<br/>html5
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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Refererande domän

Värdnamnet som är associerade med referent genom vilket innehåll begärdes avgör om refererar domän villkor är uppfyllt.

Den **matchningar**/**matchar inte** anger de villkor som matchar domänen refererar villkor uppfylls:

- **Matchningar**: Kräver refererande värdnamnet som matchar de angivna värdena. 
- **Matchar inte**: Kräver att refererande värdnamnet inte matchar det angivna värdet.

Viktig information:

- Ange flera värdnamn genom att avgränsa dem med ett enda blanksteg.
- Har stöd för den här matchningsvillkor [jokertecken värden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Om det angivna värdet inte innehåller en asterisk, måste det vara en exakt matchning för den referent värdnamn. Till exempel skulle anger ”mydomain.com” inte matchar ”www.mydomain.com”.
- Använd den **Ignorera skiftläge** alternativet att kontrollera om en skiftlägeskänslig jämförelse görs.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Begära huvud Regex

Den **matchningar**/**matchar inte** anger de villkor som matchar Regex begära huvud villkor är uppfyllt.

- **Matchningar**: Kräver begäran som innehåller det angivna huvudet. Värdet måste matcha mönstret som definieras i den angivna [reguljärt uttryck](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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
  - Ett huvudvärde kan dra nytta av [jokertecken värden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Använd den **Ignorera skiftläge** möjlighet att styra jämförelser av rubriken värde skiftläge.
  - Den här matchar villkoret är uppfyllt när ett huvudvärde matchar exakt till minst en av de angivna mönster.
  - Ange flera värden genom att avgränsa dem med ett enda blanksteg.
- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
  - Slutför Cache fyllning
  - Standard interna maxåldern
  - Tvinga interna maxåldern
  - Ignore Origin No-Cache
  - Intern Max-inaktuell

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Begärandemetod

Metod för begäran matchar villkoret är uppfyllt endast när tillgångar begärs via metoden för valda begäran. Begäran om tillgängliga metoderna är:

- HÄMTA
- HEAD
- POST
- ALTERNATIV
- PLACERA
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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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

- En URL-sökvägen kan dra nytta av [jokertecken värden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Använd den **Ignorera skiftläge** alternativet att kontrollera om en skiftlägeskänslig jämförelse.

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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

- Ett namnvärde kan dra nytta av [jokertecken värden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Varje filnamnsmönster kan exempelvis bestå av en eller flera asterisker (*), där varje asterisk matchar en sekvens med ett eller flera tecken.

- Om jokertecken får inte anges, att endast en exakt matchning uppfylla det här matchningsvillkor.

    Till exempel matchar anger ”presentation.ppt” en tillgång med namnet ”presentation.ppt”, men inte en namngiven ”presentation.pptx”.

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>URL-sökväg Regex

Jämför en begäran URL-sökvägen till den angivna [reguljärt uttryck](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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
     - CDN URL: http://wpc.0001.&lt ;domain&gt; /800001/CustomerOrigin/path/asset.htm
     - Edge CNAME-URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Ytterligare information:
    
     - URL-sökväg (i förhållande till rot): /800001/CustomerOrigin/path/asset.htm
    
     - URL-sökväg (i förhållande till ursprunget): /path/asset.htm
    
- Ange flera URL-sökvägar genom att avgränsa dem med ett enda blanksteg.

   Till exempel: /marketing/asset.* /sales/*.htm

- Frågesträngar i URL: en ignoreras.
    
- Använd den **Ignorera skiftläge** alternativet att kontrollera om en skiftlägeskänslig jämförelse.
    
- Ersätt blanksteg i URL-sökvägen med ”% 20”.
    
- Värdet som angetts för en URL-sökväg kan dra nytta av [jokertecken värden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Varje mönster för URL-sökvägen kan innehålla en eller flera asterisker (*), där varje asterisk kan matcha en sekvens av ett eller flera tecken.

#### <a name="sample-scenarios"></a>Exempelscenarier

Exempel-konfigurationer i tabellen nedan förutsätter att den här matchar villkoret är uppfyllt när en begäran matchar det angivna URL-mönstret:

Value                   | I förhållande till    | Resultat 
------------------------|----------------|-------
*/test.html */test.php  | Rot- eller ursprung | Det här mönstret matchas av begäranden för tillgångar med namnet ”test.html” eller ”test.php” i valfri mapp.
/80ABCD/origin/text/*   | rot           | Det här mönstret matchas när den begärda tillgången uppfyller följande kriterier: <br />-Det måste finnas på en kund ursprung som kallas ”ursprung”. <br />-Den relativa sökvägen måste börja med en mapp med namnet ”text”. Den begärda tillgången kan det vill säga antingen finnas i mappen ”text” eller en av dess rekursiv undermappar.
*/CSS/* */js/*          | Rot- eller ursprung | Det här mönstret matchas av alla CDN eller edge CNAME-adresser som innehåller en css- eller js-mapp.
*.jpg *.gif *.png       | Rot- eller ursprung | Det här mönstret matchas av alla CDN eller edge CNAME URL: er slutar med .jpg, .gif, eller .png. Ett annat sätt att ange det här mönstret är med i [URL-sökväg-tillägget matchningsvillkor](#url-path-extension).
/ bilder / * / media / *      | Ursprung         | Det här mönstret matchas av CDN- eller kanttabell CNAME URL: er vars relativ sökväg som börjar med en ”avbildningar” eller ”media”-mapp. <br />-CDN URL: http:\//wpc.0001.&lt; domän&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

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
- Parametern-värden kan innehålla [jokertecken värden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
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
Email | Joe\* | Det här mönstret matchas när frågesträngen för en begärd URL innehåller en parameter för e-post som börjar med ”Johan”.

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>URL: en fråga Regex

Identifierar förfrågningar som innehåller den angivna parametern för frågesträngen. Den här parametern anges till ett värde som matchar en angiven [reguljärt uttryck](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

Den **matchningar**/**matchar inte** anger de villkor som matchar Regex för URL-frågan villkor är uppfyllt.

- **Matchningar**: Kräver begäran som innehåller en URL-frågesträngen som matchar det angivna reguljära uttrycket.
- **Matchar inte**: Kräver begäran som innehåller en URL-frågesträngen som inte matchar det angivna reguljära uttrycket.

Viktig information:

- Endast uppfyller exakta matchningar till det angivna reguljära uttrycket den här matchningsvillkor.
    
- Använd den **Ignorera skiftläge** möjlighet att styra fråga strängjämförelser skiftläge.
    
- För det här alternativet börjar en frågesträng med det första tecknet efter frågetecken (?) avgränsare för frågesträngen.
    
- Vissa tecken kräver URL-kodning. Använd symbolen procent till URL: en koda följande tecken:

   Tecken | URL-kodning | Value
   ----------|--------------|------
   Rymd     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`20
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`25
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Observera att procent symboler måste undantas.

- Särskilda reguljärt uttryck för Double-escape-tecken (till exempel \^$. +) att inkludera ett omvänt snedstreck i det reguljära uttrycket.

   Exempel:

   Value | Tolkas som 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- På grund av det sätt som i vilket cacheminne inställningar spåras, är den här matchningsvillkor inte kompatibel med följande funktioner:
   - Slutför Cache fyllning
   - Standard interna maxåldern
   - Tvinga interna maxåldern
   - Ignore Origin No-Cache
   - Intern Max-inaktuell

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>URL: en fråga med jokertecken

Jämför de angivna värdena mot frågesträngen i begäran.

Den **matchningar**/**matchar inte** anger de villkor som jokertecken för URL-frågan matchar villkoret är uppfyllt.

- **Matchningar**: Kräver begäran som innehåller en URL-frågesträngen som matchar det angivna jokertecken-värdet.
- **Matchar inte**: Kräver begäran som innehåller en URL-frågesträngen som inte matchar det angivna jokertecken-värdet.

Viktig information:

- För det här alternativet börjar en frågesträng med det första tecknet efter frågetecken (?) avgränsare för frågesträngen.
- Parametervärden kan innehålla [jokertecken värden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
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

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Content Delivery Network](cdn-overview.md)
- [Regelmotor – referens](cdn-verizon-premium-rules-engine-reference.md)
- [Regelmotor – villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regelmotor – funktioner](cdn-verizon-premium-rules-engine-reference-features.md)
- [Åsidosätta standardbeteendet för HTTP med regelmotorn](cdn-verizon-premium-rules-engine.md)
