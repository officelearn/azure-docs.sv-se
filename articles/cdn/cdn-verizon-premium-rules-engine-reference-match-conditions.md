---
title: Azure CDN från Verizon Premium-regel motor matchnings villkor | Microsoft Docs
description: Referens dokumentation för Azure Content Delivery Network från Verizon Premium Rules-regler för motor matchnings villkor.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 3bc439e3244be63bff1c54d3230eda17dfb9d88d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745597"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN från Verizon Premium-regel motor matchnings villkor

Den här artikeln innehåller detaljerade beskrivningar av tillgängliga matchnings villkor för Azure-Content Delivery Network (CDN) från Verizon Premium [Rules Engine](cdn-verizon-premium-rules-engine.md).

Den andra delen av en regel är matchnings villkoret. Ett matchnings villkor identifierar vissa typer av begär Anden för vilka en uppsättning funktioner ska utföras.

Du kan till exempel använda ett matchnings villkor för att:

- Filtrera begär ande innehåll på en viss plats.
- Filtrera förfrågningar som genereras från en viss IP-adress eller land/region.
- Filtrera förfrågningar efter rubrik information.

## <a name="always-match-condition"></a>Matcha alltid villkor

Villkoret Always match använder en standard uppsättning funktioner för alla begär Anden.

Name | Syfte
-----|--------
[Alltid](#always) | Använder en standard uppsättning funktioner för alla begär Anden.

## <a name="device-match-condition"></a>Villkor för enhets matchning

Villkoret enhets matchning identifierar begär Anden som görs från en mobil enhet baserat på dess egenskaper.  

Name | Syfte
-----|--------
[Enhet](#device) | Identifierar förfrågningar som gjorts från en mobil enhet baserat på dess egenskaper.

## <a name="location-match-conditions"></a>Villkor för plats matchning

Plats matchnings villkoren identifierar begär Anden baserat på beställarens plats.

Name | Syfte
-----|--------
[SOM nummer](#as-number) | Identifierar begär Anden som kommer från ett visst nätverk.
[Land](#country) | Identifierar begär Anden som kommer från de angivna länderna/regionerna.

## <a name="origin-match-conditions"></a>Ursprungliga matchnings villkor

Villkoren för ursprunglig matchning identifierar begär Anden som pekar på Content Delivery Network lagring eller kund ursprungs Server.

Name | Syfte
-----|--------
[CDN-ursprung](#cdn-origin) | Identifierar begär Anden om innehåll som lagras i Content Delivery Network lagring.
[Kund ursprung](#customer-origin) | Identifierar begär Anden om innehåll som lagras på en specifik kund ursprungs Server.

## <a name="request-match-conditions"></a>Matchnings villkor för begäran

Matchnings villkoren för begäran identifierar förfrågningar baserat på deras egenskaper.

Name | Syfte
-----|--------
[Klientens IP-adress](#client-ip-address) | Identifierar begär Anden som kommer från en viss IP-adress.
[Cookie-parameter](#cookie-parameter) | Kontrollerar de cookies som är associerade med varje begäran för det angivna värdet.
[Cookie-parameter regex](#cookie-parameter-regex) | Kontrollerar de cookies som är kopplade till varje begäran för det angivna reguljära uttrycket.
[Edge CNAME](#edge-cname) | Identifierar begär Anden som pekar på en viss Edge CNAME.
[Refererande domän](#referring-domain) | Identifierar begär Anden som refereras från de angivna värd namnen.
[Tecken för begär ande rubrik](#request-header-literal) | Identifierar begär Anden som innehåller den angivna huvud uppsättningen till ett angivet värde.
[Huvudbegäran-huvud-regex](#request-header-regex) | Identifierar begär Anden som innehåller den angivna huvud uppsättningen till ett värde som matchar det angivna reguljära uttrycket.
[Jokertecken för begär ande rubrik](#request-header-wildcard) | Identifierar begär Anden som innehåller den angivna huvud uppsättningen till ett värde som matchar det angivna mönstret.
[Metod för begäran](#request-method) | Identifierar förfrågningar per HTTP-metod.
[Begär ande schema](#request-scheme) | Identifierar begär Anden via HTTP-protokollet.

## <a name="url-match-conditions"></a>Villkor för URL-matchning

Villkoren för URL-matchning identifierar begär Anden baserat på deras URL: er.

Name | Syfte
-----|--------
[Katalog för URL-sökväg](#url-path-directory) | Identifierar förfrågningar efter deras relativa sökväg.
[URL Path-tillägg](#url-path-extension) | Identifierar förfrågningar efter fil namns tillägg.
[URL-sökväg filename](#url-path-filename) | Identifierar begär Anden efter deras fil namn.
[URL-sökväg literal](#url-path-literal) | Jämför en begär ande relativa sökväg med det angivna värdet.
[URL-sökväg regex](#url-path-regex) | Jämför en begär ande relativa sökväg med det angivna reguljära uttrycket.
[Jokertecken för URL-sökväg](#url-path-wildcard) | Jämför en begär ande relativa sökväg med det angivna mönstret.
[URL-frågans litteral](#url-query-literal) | Jämför en förfrågans frågesträng med det angivna värdet.
[Parameter för URL-fråga](#url-query-parameter) | Identifierar begär Anden som innehåller den angivna frågesträngen inställd på ett värde som matchar ett angivet mönster.
[URL-fråga regex](#url-query-regex) | Identifierar begär Anden som innehåller den angivna frågesträngen inställd på ett värde som matchar ett angivet reguljärt uttryck.
[Jokertecken för URL-fråga](#url-query-wildcard) | Jämför det angivna värdet med frågans frågesträng.

## <a name="reference-for-rules-engine-match-conditions"></a>Referens för regel motorns matchnings villkor

---

### <a name="always"></a>Alltid

Villkoret Always match använder en standard uppsättning funktioner för alla begär Anden.

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>SOM nummer

AS Number-nätverket definieras av sitt autonoma system nummer (ASN). 

**Matchningarna** / **stämmer inte överens** med det villkor som anger vilka villkor för as-tal matchningen som är uppfyllda:

- **Matchningar**: kräver att ASN för klient nätverket matchar ett av de angivna ASN: er. 
- **Matchar inte**: kräver att ASN för klient nätverket inte matchar någon av de angivna ASN: er.

Viktig information:

- Ange flera ASN: er genom att avgränsa var och en med ett enda blank steg. 64514 64515 matchar till exempel begär Anden som kommer från antingen 64514 eller 64515.
- Vissa begär Anden kanske inte returnerar ett giltigt ASN. Ett frågetecken (?) matchar begär Anden som det inte gick att fastställa ett giltigt ASN för.
- Ange hela ASN för det önskade nätverket. Partiella värden kommer inte att matchas.
- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
  - Slutför cache-fyllning
  - Standard intern max ålder
  - Framtvinga intern max ålder
  - Ignorera ursprung no-cache
  - Intern Max-inaktuell

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>CDN-ursprung

Matchnings villkoret för CDN-ursprunget uppfylls när båda följande villkor uppfylls:

- Innehåll från CDN-lagring begärdes.
- Begärd URI använder typen av innehålls åtkomst punkt (till exempel/000001) som har definierats i detta matchnings villkor:
  - CDN-URL: begär ande-URI måste innehålla den valda innehålls åtkomst punkten.
  - Edge CNAME-URL: motsvarande kant-CNAME-konfiguration måste peka på den valda innehålls åtkomst punkten.
  
Viktig information:

- Innehålls åtkomst punkten identifierar den tjänst som ska hantera det begärda innehållet.
- Använd inte instruktionen AND IF för att kombinera vissa matchnings villkor. Om du till exempel kombinerar ett matchnings villkor för CDN-ursprung med ett matchnings villkor för kund ursprung skapas ett matchnings mönster som aldrig kunde matchas. Av den anledningen kan två matchnings villkor för CDN-ursprung inte kombineras via en AND IF-instruktion.

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Klientens IP-adress

**Matchningen** / **matchar inte** alternativet anger under vilka villkor som klientens IP-adress matchnings villkor är uppfyllt:

- **Matchningar**: kräver att KLIENTens IP-adress matchar en av de angivna IP-adresserna. 
- **Matchar inte**: kräver att KLIENTens IP-adress inte matchar någon av de angivna IP-adresserna. 

Viktig information:

- Använd CIDR-notering.
- Ange flera IP-adresser och/eller IP-adressblock genom att begränsa var och en av dem med ett enda blank steg. Ett exempel:
  - **IPv4-exempel**: 1.2.3.4 10.20.30.40 matchar alla begär Anden som kommer från adressen 1.2.3.4 eller 10.20.30.40.
  - **IPv6-exempel**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matchar alla begär Anden som kommer från adress 1:2:3:4:5:6:7:8 eller 10:20:30:40:50:60:70:80.
- Syntaxen för ett IP-adressblock är bas-IP-adressen följt av ett snedstreck och prefixets storlek. Ett exempel:
  - **IPv4-exempel**: 5.5.5.64/26 matchar alla begär Anden som kommer från adresser 5.5.5.64 via 5.5.5.127.
  - **IPv6-exempel**: 1:2:3:/48 matchar alla begär Anden som kommer från adresser 1:2:3:0:0:0:0:0 till och med 1:2: 3: FFFF: FFFF: FFFF: FFFF: FFFF.
- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
  - Slutför cache-fyllning
  - Standard intern max ålder
  - Framtvinga intern max ålder
  - Ignorera ursprung no-cache
  - Intern Max-inaktuell

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Cookie-parameter

**Matchningarna** / **stämmer inte överens** med alternativet anger under vilka villkor som matchnings villkoret för cookie-parametern är uppfyllt.

- **Matchningar**: kräver en begäran att innehålla angiven cookie med ett värde som matchar minst ett av de värden som har definierats i matchnings villkoret.
- **Matchar inte**: kräver att begäran uppfyller något av följande villkor:
  - Den innehåller inte den angivna cookien.
  - Den innehåller angiven cookie, men dess värde matchar inte något av de värden som har definierats i det här matchnings villkoret.
  
Viktig information:

- Cookie-namn:
  - Eftersom jokertecken, inklusive asterisker (*), inte stöds när du anger ett cookie-namn, är endast exakta cookie-namn giltiga för jämförelse.
  - Det går bara att ange ett enda cookie-namn per instans av matchnings villkoret.
  - Cookie-namn jämförelser är inte Skift läges känsliga.
- Cookie-värde:
  - Ange flera cookie-värden genom att avgränsa var och en med ett enda blank steg.
  - Ett cookie-värde kan dra nytta av [jokertecken](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Om inget jokertecken anges uppfyller det bara matchnings villkoret. Om du till exempel anger "värde" matchar "värde", men inte "värde1" eller "värde2".
  - Använd alternativet **Ignorera fall** för att kontrol lera om en Skift läges känslig jämförelse görs mot begärans cookie-värde.
- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
  - Slutför cache-fyllning
  - Standard intern max ålder
  - Framtvinga intern max ålder
  - Ignorera ursprung no-cache
  - Intern Max-inaktuell

[Tillbaka till början](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Cookie-parameter regex

Cookie-parametern regex match villkor definierar ett cookie-namn och-värde. Du kan använda [reguljära uttryck](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) för att definiera det önskade cookie-värdet.

**Matchningarna** / **stämmer inte överens** med det villkor som anger under vilken cookie-parameter regex matchnings villkoret är uppfyllt.

- **Matchningar**: kräver en begäran att innehålla angiven cookie med ett värde som matchar det angivna reguljära uttrycket.
- **Matchar inte**: kräver att begäran uppfyller något av följande villkor:
  - Den innehåller inte den angivna cookien.
  - Den innehåller angiven cookie, men dess värde matchar inte det angivna reguljära uttrycket.
  
Viktig information:

- Cookie-namn:
  - Eftersom reguljära uttryck och jokertecken, inklusive asterisker (*), inte stöds när du anger ett cookie-namn, är bara exakta cookie-namn giltiga för jämförelse.
  - Det går bara att ange ett enda cookie-namn per instans av matchnings villkoret.
  - Cookie-namn jämförelser är inte Skift läges känsliga.
- Cookie-värde:
  - Ett cookie-värde kan dra nytta av reguljära uttryck.
  - Använd alternativet **Ignorera fall** för att kontrol lera om en Skift läges känslig jämförelse görs mot begärans cookie-värde.
- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
  - Slutför cache-fyllning
  - Standard intern max ålder
  - Framtvinga intern max ålder
  - Ignorera ursprung no-cache
  - Intern Max-inaktuell

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Land

Du kan ange ett land genom lands koden. 

**Matchningarna** / **stämmer inte överens** med det villkor som uppfyller villkoret för land matchning:

- **Matchningar**: kräver att begäran innehåller de angivna lands kod värdena. 
- **Matchar inte**: kräver att begäran inte innehåller de angivna lands kod värdena.

Viktig information:

- Ange flera lands koder genom att begränsa var och en med ett enda blank steg.
- Jokertecken stöds inte när du anger en landskod.
- Lands koderna "EU" och "AP" omfattar inte alla IP-adresser i dessa regioner.
- Vissa begär Anden kanske inte returnerar en giltig landskod. Ett frågetecken (?) matchar begär Anden som det inte gick att fastställa en giltig landskod för.
- Lands koder är Skift läges känsliga.
- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
  - Slutför cache-fyllning
  - Standard intern max ålder
  - Framtvinga intern max ålder
  - Ignorera ursprung no-cache
  - Intern Max-inaktuell

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementera lands filtrering med hjälp av regel motorn

Med detta matchnings villkor kan du utföra en mängd anpassningar baserat på den plats som en begäran kommer från. Funktionen för funktionen land filtrering kan till exempel replikeras genom följande konfiguration:

- Matchning av jokertecken för URL-sökväg: ange [URL-sökväg jokertecken matchnings villkor](#url-path-wildcard) till den katalog som ska skyddas. 
    Lägg till en asterisk i slutet av den relativa sökvägen för att säkerställa att åtkomsten till alla dess underordnade objekt begränsas av den här regeln.

- Lands matchning: ange land matchnings villkoret till önskad uppsättning länder/regioner.
  - Tillåt: ange lands matchnings villkoret till **överensstämmer inte** för att endast tillåta att de angivna länderna/regionerna har åtkomst till innehåll som lagras på den plats som definierats av URL-sökvägen matchnings villkor för jokertecken.
  - Blockera: ange lands matchnings villkoret som **matchar** för att blockera de angivna länderna/regionerna från åtkomst till innehåll som lagras på den plats som definieras av matchnings villkoret för URL-sökvägen.

- Funktionen neka åtkomst (403): aktivera [funktionen neka åtkomst (403)](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) för att replikera funktionen för att tillåta eller blockera del av funktionen land filtrering.

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Kund ursprung

Viktig information:

- Matchnings villkoret för kund ursprung är uppfyllt oavsett om innehåll begärs via en CDN-URL eller en uppstarts-URL som pekar på det valda kund ursprunget.
- En kund ursprungs konfiguration som refereras till av en regel kan inte tas bort från sidan kund ursprung. Innan du försöker ta bort en konfiguration för kund ursprung kontrollerar du att följande konfigurationer inte hänvisar till den:
  - Ett matchnings villkor för kund ursprung
  - En Edge CNAME-konfiguration
- Använd inte instruktionen AND IF för att kombinera vissa matchnings villkor. Du kan till exempel skapa ett matchnings mönster som aldrig kunde matchas om du kombinerar ett matchnings villkor för kund ursprung med ett krav för CDN-ursprung. Av den anledningen kan två matchnings villkor för kund ursprung inte kombineras via en AND-sats.

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Enhet

Villkoret enhets matchning identifierar begär Anden som görs från en mobil enhet baserat på dess egenskaper. Identifiering av mobila enheter uppnås via [WURFL](http://wurfl.sourceforge.net/). 

**Matchningarna** / **stämmer inte överens** med de villkor som enhetens matchnings villkor är uppfyllt under:

- **Matchningar**: kräver att beställarens enhet matchar det angivna värdet. 
- **Matchar inte**: kräver att beställarens enhet inte matchar det angivna värdet.

Viktig information:

- Använd alternativet **Ignorera fall** för att ange om det angivna värdet är Skift läges känsligt.
- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
  - Slutför cache-fyllning
  - Standard intern max ålder
  - Framtvinga intern max ålder
  - Ignorera ursprung no-cache
  - Intern Max-inaktuell

#### <a name="string-type"></a>Sträng typ

En WURFL-funktion accepterar vanligt vis valfri kombination av siffror, bokstäver och symboler. På grund av den flexibla typen av funktion måste du välja hur värdet som associeras med matchnings villkoret ska tolkas. I följande tabell beskrivs tillgängliga alternativ uppsättningar:

Typ     | Beskrivning
---------|------------
Strängen  | Välj det här alternativet om du vill förhindra att de flesta tecken tas med särskilda innebörd genom att använda deras [litterala värde](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Användning | Välj det här alternativet om du vill dra nytta av alla [jokertecken] ([jokertecken](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Verifiering    | Välj det här alternativet om du vill använda [reguljära uttryck](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Reguljära uttryck är användbara för att definiera ett mönster med tecken.

#### <a name="wurfl-capabilities"></a>WURFL-funktioner

En WURFL-funktion avser en kategori som beskriver mobila enheter. Den valda funktionen bestämmer vilken typ av mobil enhets beskrivning som används för att identifiera begär Anden.

I följande tabell visas WURFL-funktioner och deras variabler för regel motorn.

> [!NOTE]
> Följande variabler stöds i avsnittet **ändra huvud för klient begär ande huvud** och ändra funktioner för **klient svars huvud** .

Funktion | Variabel | Beskrivning | Exempel värden
-----------|----------|-------------|----------------
Märkes namn | % {wurfl_cap_brand_name} | En sträng som anger enhetens märkes namn. | Samsung
Enhetens operativ system | % {wurfl_cap_device_os} | En sträng som anger vilket operativ system som är installerat på enheten. | iOS
Enhetens operativsystemversion | % {wurfl_cap_device_os_version} | En sträng som anger versions numret för det operativ system som är installerat på enheten. | 1.0.1
Dubbel orientering | % {wurfl_cap_dual_orientation} | Ett booleskt värde som anger om enheten stöder dubbel orientering. | true
HTML-föredragen DTD | % {wurfl_cap_html_preferred_dtd} | En sträng som anger den mobila enhetens primära dokument typ definition (DTD) för HTML-innehåll. | inget<br/>xhtml_basic<br/>HTML5
Bild inlägg | % {wurfl_cap_image_inlining} | Ett booleskt värde som anger om enheten stöder base64-kodade avbildningar. | falskt
Är Android | % {wurfl_vcap_is_android} | Ett booleskt värde som anger om enheten använder Android OS. | true
Är IOS | % {wurfl_vcap_is_ios} | Ett booleskt värde som anger om enheten använder iOS. | falskt
Är smart TV | % {wurfl_cap_is_smarttv} | Ett booleskt värde som anger om enheten är en smart TV. | falskt
Är smartphone | % {wurfl_vcap_is_smartphone} | Ett booleskt värde som anger om enheten är en smartphone. | true
Är surfplatta | % {wurfl_cap_is_tablet} | Ett booleskt värde som anger om enheten är en surfplatta. Den här beskrivningen är OS-oberoende. | true
Är trådlös enhet | % {wurfl_cap_is_wireless_device} | Ett booleskt värde som anger om enheten betraktas som en trådlös enhet. | true
Marknadsförings namn | % {wurfl_cap_marketing_name} | En sträng som anger enhetens marknadsförings namn. | Black Berry 8100 Pearl
Mobil webbläsare | % {wurfl_cap_mobile_browser} | En sträng som anger vilken webbläsare som används för att begära innehåll från enheten. | Chrome
Mobil webbläsarens version | % {wurfl_cap_mobile_browser_version} | En sträng som anger vilken version av webbläsaren som används för att begära innehåll från enheten. | 31
Modell namn | % {wurfl_cap_model_name} | En sträng som anger enhetens modell namn. | S3
Progressiv nedladdning | % {wurfl_cap_progressive_download} | Ett booleskt värde som anger om enheten stöder uppspelning av ljud och video medan den fortfarande laddas ned. | true
Lanserings datum | % {wurfl_cap_release_date} | En sträng som anger året och månaden då enheten lades till i WURFL-databasen.<br/><br/>Formatering`yyyy_mm` | 2013_december
Lösnings höjd | % {wurfl_cap_resolution_height} | Ett heltal som anger enhetens höjd i bild punkter. | 768
Lösnings bredd | % {wurfl_cap_resolution_width} | Ett heltal som anger enhetens bredd i bild punkter. | 1024

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Edge CNAME

Viktig information:

- Listan över tillgängliga Edge CNAME är begränsad till de CNAME-CNAME som har kon figurer ATS på sidan Edge CNAME för den plattform där regel motorn konfigureras.
- Innan du försöker ta bort en Edge CNAME-konfiguration måste du kontrol lera att det inte finns någon gräns för en gräns för CNAME-matchning. Det går inte att ta bort kant-CNAME-konfigurationer som har definierats i en regel från sidan Edge CNAME.
- Använd inte instruktionen AND IF för att kombinera vissa matchnings villkor. Du kan till exempel skapa ett matchnings mönster som aldrig kunde matchas med ett villkor för att skapa en gräns för CNAME-matchning med ett matchnings villkor för kund ursprung. Av den anledningen kan inte två gräns för CNAME-matchning kombineras med en AND IF-instruktion.
- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
  - Slutför cache-fyllning
  - Standard intern max ålder
  - Framtvinga intern max ålder
  - Ignorera ursprung no-cache
  - Intern Max-inaktuell

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Refererande domän

Värd namnet som är associerat med den referent genom vilken innehåll begärdes avgör om det refererande domän villkoret är uppfyllt.

**Matchningen** / **matchar inte** alternativet anger under vilka villkor som det refererande domän matchnings villkoret är uppfyllt:

- **Matchningar**: kräver det refererande värd namnet för att matcha de angivna värdena. 
- **Matchar inte**: kräver att det refererande värd namnet inte matchar det angivna värdet.

Viktig information:

- Ange flera värdnamn genom att begränsa var och en med ett enda blank steg.
- Matchnings villkoret har stöd för [jokertecken](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Om det angivna värdet inte innehåller någon asterisk måste det vara en exakt matchning för referentens värdnamn. Om du till exempel anger "mydomain.com" matchar inte "www.mydomain.com".
- Använd alternativet **Ignorera fall** för att kontrol lera om en Skift läges känslig jämförelse görs.
- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
  - Slutför cache-fyllning
  - Standard intern max ålder
  - Framtvinga intern max ålder
  - Ignorera ursprung no-cache
  - Intern Max-inaktuell

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Tecken för begär ande rubrik

**Matchningarna** / **stämmer inte överens** med alternativet anger under vilka villkor som villkoret för begärans huvudets litterala matchning är uppfyllt.

- **Matchningar**: kräver att begäran innehåller det angivna huvudet. Värdet måste matcha det som har definierats i matchnings villkoret.
- **Matchar inte**: kräver att begäran uppfyller något av följande villkor:
  - Det innehåller inte det angivna huvudet.
  - Det innehåller det angivna huvudet men dess värde matchar inte det som har definierats i det här matchnings villkoret.
  
Viktig information:

- Rubrik namns jämförelser är alltid Skift läges känsliga. Använd alternativet **Ignorera fall** för att kontrol lera Skift läges känsligheten för jämförelse av rubrik värden.
- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
  - Slutför cache-fyllning
  - Standard intern max ålder
  - Framtvinga intern max ålder
  - Ignorera ursprung no-cache
  - Intern Max-inaktuell

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Huvudbegäran-huvud-regex

**Matchningarna** / **stämmer inte överens** med det här alternativet anger under vilka villkor som begär ande huvudet regex matchnings villkor är uppfyllt.

- **Matchningar**: kräver att begäran innehåller det angivna huvudet. Dess värde måste matcha det mönster som har definierats i det angivna [reguljära uttrycket](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Matchar inte**: kräver att begäran uppfyller något av följande villkor:
  - Det innehåller inte det angivna huvudet.
  - Det innehåller det angivna huvudet men dess värde matchar inte det angivna reguljära uttrycket.

Viktig information:

- Huvud namn:
  - Rubrik namns jämförelser är inte Skift läges känsliga.
  - Ersätt blank steg i rubrik namnet med "%20".
- Huvud värde:
  - Ett huvud värde kan dra nytta av reguljära uttryck.
  - Använd alternativet **Ignorera fall** för att kontrol lera Skift läges känsligheten för jämförelse av rubrik värden.
  - Matchnings villkoret uppfylls bara när ett huvud värde exakt matchar minst ett av de angivna mönstren.
- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
  - Slutför cache-fyllning
  - Standard intern max ålder
  - Framtvinga intern max ålder
  - Ignorera ursprung no-cache
  - Intern Max-inaktuell

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Jokertecken för begär ande rubrik

**Matchningarna** / **stämmer inte överens** med det villkor som anger under vilket villkor för matchning av begär ande rubrik är uppfyllt.

- **Matchningar**: kräver att begäran innehåller det angivna huvudet. Dess värde måste matcha minst ett av de värden som har definierats i matchnings villkoret.
- **Matchar inte**: kräver att begäran uppfyller något av följande villkor:
  - Det innehåller inte det angivna huvudet.
  - Det innehåller det angivna huvudet men dess värde matchar inte något av de angivna värdena.
  
Viktig information:

- Huvud namn:
  - Rubrik namns jämförelser är inte Skift läges känsliga.
  - Blank steg i rubrik namnet måste ersättas med "%20". Du kan också använda det här värdet för att ange blank steg i ett huvud värde.
- Huvud värde:
  - Ett huvud värde kan dra nytta av [jokertecken](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Använd alternativet **Ignorera fall** för att kontrol lera Skift läges känsligheten för jämförelse av rubrik värden.
  - Matchnings villkoret uppfylls när ett huvud värde exakt matchar minst ett av de angivna mönstren.
  - Ange flera värden genom att begränsa var och en med ett enda blank steg.
- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
  - Slutför cache-fyllning
  - Standard intern max ålder
  - Framtvinga intern max ålder
  - Ignorera ursprung no-cache
  - Intern Max-inaktuell

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Metod för begäran

Matchnings villkoret för metoden för begäran uppfylls bara när till gångar begärs via den valda metoden för begäran. De tillgängliga metoderna för begäran är:

- HÄMTA
- HEAD
- POST
- SÄTT
- PUT
- DELETE
- Rita
- GÅ

Viktig information:

- Som standard kan endast metoden GET Request generera cachelagrat innehåll i nätverket. Alla andra metoder för begäran är proxy via nätverket.
- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
  - Slutför cache-fyllning
  - Standard intern max ålder
  - Framtvinga intern max ålder
  - Ignorera ursprung no-cache
  - Intern Max-inaktuell

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Begär ande schema

Villkoret för matchning av begär ande schema uppfylls endast när till gångar begärs via det valda protokollet. Tillgängliga protokoll är:

- HTTP
- HTTPS

Viktig information:

- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
  - Slutför cache-fyllning
  - Standard intern max ålder
  - Framtvinga intern max ålder
  - Ignorera ursprung no-cache
  - Intern Max-inaktuell

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>Katalog för URL-sökväg

Identifierar en begäran via dess relativa sökväg, vilket utesluter fil namnet för den begärda till gången.

**Matchningarna** / **stämmer inte överens** med det villkor som URL-sökvägarnas katalog matchnings villkor är uppfyllt under.

- **Matchningar**: kräver att begäran innehåller en relativ URL-sökväg, förutom fil namnet, som matchar det angivna URL-mönstret.
- **Matchar inte**: kräver att begäran innehåller en relativ URL-sökväg, exklusive fil namn, som inte matchar angivet URL-mönster.

Viktig information:

- Använd alternativet **relativt** till för att ange om URL-jämförelsen börjar före eller efter innehålls åtkomst punkten. Innehålls åtkomst punkten är den del av sökvägen som visas mellan Verizon CDN-värdnamnet och den relativa sökvägen till den begärda till gången (till exempel/800001/CustomerOrigin). Den identifierar en plats efter server typ (till exempel CDN eller kund ursprung) och ditt kund konto nummer.

   Följande värden är tillgängliga för alternativet **relativt till** :
  - **Rot**: anger att URL-jämförelsen börjar direkt efter CDN-värdnamnet. 

  Till exempel: http: \/ /WPC.0001. &lt; 800001 för domän &gt; / **/källa/min mapp**/index.htm

  - **Ursprung**: anger att URL-jämförelsen börjar efter innehålls åtkomst punkten (till exempel/000001 eller/800001/myorigin). Eftersom \* . AZUREEDGE.net CNAME skapas i förhållande till ursprungs katalogen på Verizon CDN-värdnamnet som standard, ska Azure CDN användare använda **ursprung** svärdet. 

  Till exempel: https: \/ / &lt; Endpoint &gt; . azureedge.net/**myfolder**/index.htm 

  URL: en pekar på följande Verizon CDN-värdnamn: http: \/ /WPC.0001. &lt; &gt;/index.htm för**myfolder**domän/800001/myorigin/

- En kant-CNAME-URL skrivs om till en CDN-URL före URL-jämförelsen.

    Till exempel kan båda följande URL: er peka till samma till gång och har därför samma URL-sökväg.
  - CDN-URL: http: \/ /WPC.0001. &lt; domän &gt; -800001/CustomerOrigin/Path/Asset.htm
    
  - Edge CNAME-URL: http: \/ / &lt; Endpoint &gt; . azureedge.net/Path/Asset.htm
    
    Ytterligare information:
  - Anpassad domän: https: \/ /My.domain.com/Path/Asset.htm
    
    - URL-sökväg (relativ till rot):/800001/CustomerOrigin/path/
    
    - URL-sökväg (relativ till ursprung):/Path/

- Den del av URL: en som används för URL-jämförelsen slutar precis före fil namnet för den begärda till gången. Ett avslutande snedstreck är det sista tecknet i den här typen av sökväg.

- Ersätt eventuella blank steg i URL-sökvägen med "%20".

- Varje URL Path-mönster kan innehålla en eller flera asterisker (*), där varje asterisk matchar en sekvens med ett eller flera tecken.

- Ange flera URL-sökvägar i mönstret genom att avgränsa var och en med ett enda blank steg.

    Till exempel: */Sales/*/Marketing/

- En Sök vägs specifikation för URL kan dra nytta av [jokertecken](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Använd alternativet **Ignorera fall** för att kontrol lera om en Skift läges känslig jämförelse utförs.

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>URL Path-tillägg

Identifierar begär anden av fil namns tillägget för den begärda till gången.

**Matchningarna** / **stämmer inte överens** med de villkor som URL-tilläggets matchnings villkor är uppfyllt under.

- **Matchningar**: kräver URL: en för begäran att innehålla ett fil namns tillägg som exakt matchar det angivna mönstret.

   Om du till exempel anger "htm", matchas "htm" till gångar, men inte "HTML"-till gångar.  

- **Matchar inte**: URL-begäran måste innehålla ett fil namns tillägg som inte matchar det angivna mönstret.

Viktig information:

- Ange de fil namns tillägg som ska matchas i rutan **värde** . Ta inte med en inledande period; Använd till exempel htm i stället för. htm.

- Använd alternativet **Ignorera fall** för att kontrol lera om en Skift läges känslig jämförelse utförs.

- Ange flera fil namns tillägg genom att begränsa varje tillägg med ett enda blank steg. 

    Till exempel: htm html

- Om du till exempel anger "htm" matchar "htm" till gångar, men inte "HTML"-till gångar.

#### <a name="sample-scenario"></a>Exempel scenario

Följande exempel konfiguration förutsätter att matchnings villkoret är uppfyllt när en begäran matchar ett av de angivna tilläggen.

Värde specifikation: ASP aspx php html

Matchnings villkoret är uppfyllt när det hittar URL: er som slutar med följande fil namns tillägg:

- . asp
- . aspx
- . php
- .html

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>URL-sökväg filename

Identifierar begär Anden med fil namnet för den begärda till gången. I detta matchnings villkor består ett fil namn av namnet på den begärda till gången, en punkt och fil namns tillägget (till exempel index. html).

**Matchningarna** / **stämmer inte överens** med de villkor under vilka URL-sökvägens fil namns matchnings villkor är uppfyllt.

- **Matchningar**: kräver att begäran innehåller ett fil namn i URL-sökvägen som matchar det angivna mönstret.
- **Matchar inte**: kräver att begäran innehåller ett fil namn i URL-sökvägen som inte matchar det angivna mönstret.

Viktig information:

- Använd alternativet **Ignorera fall** för att kontrol lera om en Skift läges känslig jämförelse utförs.

- Om du vill ange flera fil namns tillägg avgränsar du varje fil tillägg med ett enda blank steg.

    Exempel: index. htm index. html

- Ersätt blank steg i ett fil namns värde med "%20".

- Ett fil namns värde kan dra nytta av [jokertecken](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Varje fil namns mönster kan till exempel bestå av en eller flera asterisker (*), där varje asterisk matchar en sekvens med ett eller flera tecken.

- Om jokertecken inte anges uppfyller bara en exakt matchning detta matchnings villkor.

    Om du till exempel anger "presentation. ppt" matchar du en till gång med namnet "presentation. ppt", men inte en med namnet "presentation. pptx".

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>URL-sökväg literal

Jämför en begär ande URL-sökväg, inklusive fil namn, till det angivna värdet.

**Matchningarna** / **stämmer inte överens** med det villkor som URL-sökvägens litterala matchnings villkor är uppfyllt under.

- **Matchningar**: kräver att begäran innehåller en URL-sökväg som matchar det angivna mönstret.
- **Matchar inte**: kräver att begäran innehåller en URL-sökväg som inte matchar det angivna mönstret.

Viktig information:

- Använd alternativet **relativt** för att ange om URL-jämförelsen börjar före eller efter innehålls åtkomst punkten. 

    Följande värden är tillgängliga för alternativet **relativt till** :
  - **Rot**: anger att URL-jämförelsen börjar direkt efter CDN-värdnamnet.

    Till exempel: http: \/ /WPC.0001. &lt; domän &gt; / **800001/myorigin/MyFolder/index.htm**

  - **Ursprung**: anger att URL-jämförelsen börjar efter innehålls åtkomst punkten (till exempel/000001 eller/800001/myorigin). Eftersom \* . AZUREEDGE.net CNAME skapas i förhållande till ursprungs katalogen på Verizon CDN-värdnamnet som standard, ska Azure CDN användare använda **ursprung** svärdet. 

    Till exempel: https: \/ / &lt; Endpoint &gt; . azureedge.net/**MyFolder/index.htm**

  URL: en pekar på följande Verizon CDN-värdnamn: http: \/ /WPC.0001. &lt; &gt;**MyFolder/index.htm** för domän/800001/myorigin/

- En kant-CNAME-URL skrivs om till en CDN-URL före en URL-jämförelse.

Exempelvis pekar båda på följande URL: er till samma till gång och har därför samma URL-sökväg:

- CDN-URL: http: \/ /WPC.0001. &lt; domän &gt; -800001/CustomerOrigin/Path/Asset.htm
- Edge CNAME-URL: http: \/ / &lt; Endpoint &gt; . azureedge.net/Path/Asset.htm

    Ytterligare information:
    
    - URL-sökväg (relativ till rot):/800001/CustomerOrigin/path/asset.htm
   
    - URL-sökväg (relativ till ursprung):/path/asset.htm

- Frågesträngar i URL: en ignoreras.
- Använd alternativet **Ignorera fall** för att kontrol lera om en Skift läges känslig jämförelse utförs.
- Värdet som angetts för matchnings villkoret jämförs med den relativa sökvägen till den exakta begäran som gjorts av klienten.

- Om du vill matcha alla begär Anden som görs till en viss katalog använder du [katalogen URL-sökväg](#url-path-directory) eller matchnings villkoret för [URL-sökvägen](#url-path-wildcard) .

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>URL-sökväg regex

Jämför URL-sökvägen för en begäran till det angivna [reguljära uttrycket](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

**Matchningarna** / **stämmer inte överens** med det villkor som URL-sökvägen regex matchnings villkoret är uppfyllt under.

- **Matchningar**: kräver att begäran innehåller en URL-sökväg som matchar det angivna reguljära uttrycket.
- **Matchar inte**: kräver att begäran innehåller en URL-sökväg som inte matchar det angivna reguljära uttrycket.

Viktig information:

- En kant-CNAME-URL skrivs om till en CDN-URL före jämförelse med URL.

    Exempelvis pekar båda URL: erna till samma till gång och har därför samma URL-sökväg.

     - CDN-URL: http: \/ /WPC.0001. &lt; domän &gt; -800001/CustomerOrigin/Path/Asset.htm

     - Edge CNAME-URL: http: \/ /My.domain.com/Path/Asset.htm

    Ytterligare information:
    
     - URL-sökväg:/800001/CustomerOrigin/path/asset.htm

- Frågesträngar i URL: en ignoreras.
    
- Använd alternativet **Ignorera fall** för att kontrol lera om en Skift läges känslig jämförelse utförs.
    
- Blank steg i URL-sökvägen ska ersättas med "%20".

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>Jokertecken för URL-sökväg

Jämför en begär ande relativa URL-sökväg till det angivna jokertecken.

**Matchningarna** / **stämmer inte överens** med det angivna villkoret för URL-sökvägen matchnings villkoret är uppfyllt.

- **Matchningar**: kräver att begäran innehåller en URL-sökväg som matchar det angivna jokertecken.
- **Matchar inte**: kräver att begäran innehåller en URL-sökväg som inte matchar det angivna jokertecken.

Viktig information:

- **Relativt till** -alternativ: det här alternativet anger om URL-jämförelsen börjar före eller efter innehålls åtkomst punkten.

   Det här alternativet kan ha följande värden:
     - **Rot**: anger att URL-jämförelsen börjar direkt efter CDN-värdnamnet.

       Till exempel: http: \/ /WPC.0001. &lt; domän &gt; / **800001/myorigin/MyFolder/index.htm**

     - **Ursprung**: anger att URL-jämförelsen börjar efter innehålls åtkomst punkten (till exempel/000001 eller/800001/myorigin). Eftersom \* . AZUREEDGE.net CNAME skapas i förhållande till ursprungs katalogen på Verizon CDN-värdnamnet som standard, ska Azure CDN användare använda **ursprung** svärdet. 

       Till exempel: https: \/ / &lt; Endpoint &gt; . azureedge.net/**MyFolder/index.htm**

     URL: en pekar på följande Verizon CDN-värdnamn: http: \/ /WPC.0001. &lt; &gt;**MyFolder/index.htm** för domän/800001/myorigin/

- En kant-CNAME-URL skrivs om till en CDN-URL före jämförelse med URL.

    Exempelvis pekar båda på följande URL: er till samma till gång och har därför samma URL-sökväg:
     - CDN-URL: http://wpc.0001.&lt ;d omain &gt; /800001/CustomerOrigin/Path/Asset.htm
     - Edge CNAME-URL: http: \/ / &lt; Endpoint &gt; . azureedge.net/Path/Asset.htm
    
    Ytterligare information:
    
     - URL-sökväg (relativ till rot):/800001/CustomerOrigin/path/asset.htm
    
     - URL-sökväg (relativ till ursprung):/path/asset.htm
    
- Ange flera URL-sökvägar genom att avgränsa var och en med ett enda blank steg.

   Till exempel:/Marketing/Asset. */Sales/*. htm

- Frågesträngar i URL: en ignoreras.
    
- Använd alternativet **Ignorera fall** för att kontrol lera om en Skift läges känslig jämförelse utförs.
    
- Ersätt blank steg i URL-sökvägen med "%20".
    
- Värdet som anges för en URL-sökväg kan dra nytta av [jokertecken](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Varje URL Path-mönster kan innehålla en eller flera asterisker (*), där varje asterisk kan matcha en sekvens med ett eller flera tecken.

#### <a name="sample-scenarios"></a>Exempel scenarier

Exempel konfigurationerna i följande tabell förutsätter att matchnings villkoret är uppfyllt när en begäran matchar angivet URL-mönster:

Värde                   | I förhållande till    | Resultat 
------------------------|----------------|-------
*/test.html */test.php  | Rot eller ursprung | Det här mönstret matchas efter begär Anden för till gångar med namnet "test. html" eller "test. php" i vilken mapp som helst.
/80ABCD/origin/text/*   | Rot           | Det här mönstret matchas när den begärda till gången uppfyller följande kriterier: <br />– Det måste finnas i ett kund ursprung som kallas "ursprung". <br />-Den relativa sökvägen måste börja med en mapp med namnet "text". Det vill säga att den begärda till gången antingen finns i mappen "text" eller någon av dess rekursiva undermappar.
*/CSS/* */JS/*          | Rot eller ursprung | Det här mönstret matchas av alla CDN-eller Edge CNAME-URL: er som innehåller en CSS-eller JS-mapp.
*. jpg *. gif *. png       | Rot eller ursprung | Det här mönstret matchas av alla CDN-eller Edge CNAME-URL: er som slutar med. jpg,. gif eller. png. Ett alternativt sätt att ange det här mönstret är med [URL-sökvägen tillägg matchnings villkor](#url-path-extension).
/images/* /media/*      | Ursprung         | Det här mönstret matchas av CDN-eller Edge CNAME-URL: er vars relativa sökväg börjar med en "images"-eller "media"-mapp. <br />-CDN-URL: http: \/ /WPC.0001. &lt; domän &gt; /800001/myorigin/images/Sales/event1.png<br />– Exempel-CNAME CNAME-URL: http: \/ /CDN.mydomain.com/images/Sales/event1.png

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>URL-frågans litteral

Jämför en förfrågans frågesträng med det angivna värdet.

**Matchningarna** / **stämmer inte överens** med det villkor som anger under vilken URL-frågans litterala matchnings villkor är uppfyllt.

- **Matchningar**: kräver att begäran innehåller en URL-frågesträng som matchar den angivna frågesträngen.
- **Matchar inte**: kräver att begäran innehåller en URL-frågesträng som inte matchar den angivna frågesträngen.

Viktig information:

- Endast den exakta frågesträngen matchar detta matchnings villkor.
    
- Använd alternativet **Ignorera fall** för att kontrol lera Skift läges känsligheten för jämförelser med frågesträngar.
    
- Ta inte med något inledande frågetecken (?) i text Strängs värde texten.
    
- För vissa tecken krävs URL-kodning. Använd procent symbolen för URL: en och koda följande tecken:

   Tecken | URL-kodning
   ----------|---------
   Space     | %20
   &         | %25

- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
   - Slutför cache-fyllning
   - Standard intern max ålder
   - Framtvinga intern max ålder
   - Ignorera ursprung no-cache
   - Intern Max-inaktuell

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>Parameter för URL-fråga

Identifierar begär Anden som innehåller den angivna frågesträngen. Den här parametern har angetts till ett värde som matchar ett angivet mönster. Parametrar för frågesträng (till exempel parameter = värde) i URL: en för begäran avgör om det här villkoret är uppfyllt. Detta matchnings villkor identifierar en frågesträngparametern efter dess namn och accepterar ett eller flera värden för parametervärdet. 

**Matchningarna** / **stämmer inte överens** med det villkor som anger under vilken URL som matchnings villkoret för URL-frågan är uppfyllt.

- **Matchningar**: kräver en begäran att innehålla den angivna parametern med ett värde som matchar minst ett av de värden som har definierats i matchnings villkoret.
- **Matchar inte**: kräver att begäran uppfyller något av följande villkor:
  - Den innehåller inte den angivna parametern.
  - Den innehåller den angivna parametern, men dess värde matchar inte något av de värden som har definierats i det här matchnings villkoret.

Detta matchnings villkor är ett enkelt sätt att ange parameter namn/värde-kombinationer. Om du vill ha mer flexibilitet om du matchar en frågesträngparametern kan du använda [URL-frågans jokertecken](#url-query-wildcard) matchnings villkor.

Viktig information:

- Endast ett enda URL-frågenamn kan anges per instans av matchnings villkoret.
    
- Eftersom jokertecken inte stöds när ett parameter namn anges, är det bara de exakta parameter namns matchningarna som är giltiga för jämförelse.
- Parameter värde (n) kan innehålla [jokertecken](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Varje parameter värde mönster kan bestå av en eller flera asterisker (*), där varje asterisk kan matcha en sekvens med ett eller flera tecken.
   - För vissa tecken krävs URL-kodning. Använd procent symbolen för URL: en och koda följande tecken:

       Tecken | URL-kodning
       ----------|---------
       Space     | %20
       &         | %25

- Ange flera parameter värden för frågesträng genom att avgränsa var och en med ett enda blank steg. Matchnings villkoret uppfylls när en begäran innehåller en av de angivna kombinationerna av namn och värde.

   - Exempel 1:

     - Konfiguration:

       Värdea Värdeb

     - Den här konfigurationen matchar följande parametrar för frågesträng:

       Parameter1 = Värdea
    
       Parameter1 = Värdeb

   - Exempel 2:

     - Konfiguration: 

        Värde% 20A värde% 20B

     - Den här konfigurationen matchar följande parametrar för frågesträng:

       Parameter1 = värde% 20A

       Parameter1 = värde% 20B

- Matchnings villkoret uppfylls endast när det finns en exakt matchning till minst en av de angivna kombinationerna av frågesträngs namn och värde.

   Om du till exempel använder konfigurationen i föregående exempel skulle parameter namn/värde-kombinationen "Parameter1 = ValueAdd" inte anses vara en matchning. Men om du anger något av följande värden matchar den kombinationen av namn och värde:

   - Valuea Värdeb ValueAdd
   - Värdea * Värdeb

- Använd alternativet **Ignorera fall** för att kontrol lera Skift läges känsligheten för jämförelser med frågesträngar.
    
- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
   - Slutför cache-fyllning
   - Standard intern max ålder
   - Framtvinga intern max ålder
   - Ignorera ursprung no-cache
   - Intern Max-inaktuell

#### <a name="sample-scenarios"></a>Exempelscenarier

Följande exempel visar hur det här alternativet fungerar i vissa situationer:

Name  | Värde |  Resultat
------|-------|--------
Användare  | Joe   | Det här mönstret matchas när frågesträngen för en begärd URL är "? User = Johan."
Användare  | *     | Det här mönstret matchas när frågesträngen för en begärd URL innehåller en användar parameter.
E-post | Joe\* | Det här mönstret matchas när frågesträngen för en begärd URL innehåller en e-postparameter som börjar med "Johan".

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>URL-fråga regex

Identifierar begär Anden som innehåller den angivna frågesträngen. Den här parametern har angetts till ett värde som matchar ett angivet [reguljärt uttryck](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

**Matchningarna** / **stämmer inte överens** med det villkor som anger under vilken URL som URL-frågan regex matchnings villkor är uppfyllt.

- **Matchningar**: kräver att begäran innehåller en URL-frågesträng som matchar det angivna reguljära uttrycket.
- **Matchar inte**: kräver att begäran innehåller en URL-frågesträng som inte matchar det angivna reguljära uttrycket.

Viktig information:

- Endast exakta matchningar till det angivna reguljära uttrycket uppfyller matchnings villkoret.
    
- Använd alternativet **Ignorera fall** för att kontrol lera Skift läges känsligheten för jämförelser med frågesträngar.
    
- För det här alternativet börjar en frågesträng med det första tecknet efter frågetecknet (?) avgränsare för frågesträngen.
    
- För vissa tecken krävs URL-kodning. Använd procent symbolen för URL: en och koda följande tecken:

   Tecken | URL-kodning | Värde
   ----------|--------------|------
   Space     | %20          | \%tjugo
   &         | %25          | \%25.1

   Observera att procentuella symboler måste föregås av Escape.

- Dubbla escape-tecken för vanlig reguljära uttryck (till exempel \^ $. +) för att inkludera ett omvänt snedstreck i det reguljära uttrycket.

   Ett exempel:

   Värde | Tolkas som 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
   - Slutför cache-fyllning
   - Standard intern max ålder
   - Framtvinga intern max ålder
   - Ignorera ursprung no-cache
   - Intern Max-inaktuell

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>Jokertecken för URL-fråga

Jämför det eller de angivna värdena mot frågans frågesträng.

**Matchningarna** / **stämmer inte överens** med de villkor som URL-frågans jokertecken matchar villkoret är uppfyllt i.

- **Matchningar**: kräver att begäran innehåller en URL-frågesträng som matchar det angivna jokertecknet.
- **Matchar inte**: kräver att begäran innehåller en URL-frågesträng som inte matchar det angivna jokertecknet.

Viktig information:

- För det här alternativet börjar en frågesträng med det första tecknet efter frågetecknet (?) avgränsare för frågesträngen.
- Parameter värden kan innehålla [jokertecken](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Varje parameter värde mönster kan bestå av en eller flera asterisker (*), där varje asterisk kan matcha en sekvens med ett eller flera tecken.
   - För vissa tecken krävs URL-kodning. Använd procent symbolen för URL: en och koda följande tecken:

     Tecken | URL-kodning
     ----------|---------
     Space     | %20
     &         | %25

- Ange flera värden genom att begränsa var och en med ett enda blank steg.

   Exempel: *Parameter1 = valuea* *Värdeb* *Parameter1 = ValueC&parameter2 = värderade*

- Endast exakta matchningar till minst ett av de angivna frågesträngarna uppfyller matchnings villkoret.
    
- Använd alternativet **Ignorera fall** för att kontrol lera Skift läges känsligheten för jämförelser med frågesträngar.
    
- På grund av hur cacheinställningar spåras är det här matchnings villkoret inkompatibelt med följande funktioner:
   - Slutför cache-fyllning
   - Standard intern max ålder
   - Framtvinga intern max ålder
   - Ignorera ursprung no-cache
   - Intern Max-inaktuell

#### <a name="sample-scenarios"></a>Exempelscenarier

Följande exempel visar hur det här alternativet fungerar i vissa situationer:

 Name                 | Beskrivning
 ---------------------|------------
användare = Johan              | Det här mönstret matchas när frågesträngen för en begärd URL är "? User = Johan."
\*användare = \* \* utdatanivå =\* | Det här mönstret matchas när CDN URL-frågan innehåller antingen användar-eller utdatanivå-parametern.

[Tillbaka till början](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Content Delivery Network](cdn-overview.md)
- [Regel motor referens](cdn-verizon-premium-rules-engine-reference.md)
- [Regelmotor – villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regelmotor – funktioner](cdn-verizon-premium-rules-engine-reference-features.md)
- [Åsidosätta standard-HTTP-beteendet med hjälp av regel motorn](cdn-verizon-premium-rules-engine.md)
