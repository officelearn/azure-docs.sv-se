---
title: Azure CDN från Verizon Premium regler motor match villkor | Microsoft-dokument
description: Referensdokumentation för Azure Content Delivery Network från Verizon Premium regler motor match villkor.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593206"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN från Verizon Premium regler motor match villkor

I den här artikeln visas detaljerade beskrivningar av tillgängliga matchningsvillkor för CDN (Azure Content Delivery Network) från Verizon [Premium-regelmotorn](cdn-verizon-premium-rules-engine.md).

Den andra delen av en regel är matchningsvillkoret. Ett matchningsvillkor identifierar specifika typer av begäranden för vilka en uppsättning funktioner ska utföras.

Du kan till exempel använda ett matchningsvillkor för att:

- Filtrera begäranden om innehåll på en viss plats.
- Filtrera begäranden som genereras från en viss IP-adress eller ett visst land/en region.
- Filtrera begäranden efter huvudinformation.

## <a name="always-match-condition"></a>Matcha alltid villkor

Villkoret Alltid matchning tillämpar en standarduppsättning funktioner på alla begäranden.

Namn | Syfte
-----|--------
[Alltid](#always) | Tillämpar en standarduppsättning funktioner på alla begäranden.

## <a name="device-match-condition"></a>Villkor för enhetsmatchning

Enhetsmatchningsvillkoret identifierar begäranden som görs från en mobil enhet baserat på dess egenskaper.  

Namn | Syfte
-----|--------
[Enhet](#device) | Identifierar begäranden som görs från en mobil enhet baserat på dess egenskaper.

## <a name="location-match-conditions"></a>Platsmatchningsvillkor

Villkoren för platsmatchning identifierar begäranden baserat på beställarens plats.

Namn | Syfte
-----|--------
[AS-nummer](#as-number) | Identifierar begäranden som kommer från ett visst nätverk.
[Land/region](#country) | Identifierar begäranden som kommer från de angivna länderna/regionerna.

## <a name="origin-match-conditions"></a>Villkor för ursprungsmatchning

Ursprungsmatchningsvillkoren identifierar begäranden som pekar på lagring av Content Delivery Network eller en kundursprungsserver.

Namn | Syfte
-----|--------
[CDN Ursprung](#cdn-origin) | Identifierar begäranden om innehåll som lagras i lagring av innehållsleveransnätverk.
[Kundens ursprung](#customer-origin) | Identifierar begäranden om innehåll som lagras på en viss kundursprungsserver.

## <a name="request-match-conditions"></a>Begär matchningsvillkor

Villkor för begäranmatchning identifierar begäranden baserat på deras egenskaper.

Namn | Syfte
-----|--------
[Klientens IP-adress](#client-ip-address) | Identifierar begäranden som kommer från en viss IP-adress.
[Cookie Parameter](#cookie-parameter) | Kontrollerar cookies som är kopplade till varje begäran för det angivna värdet.
[Regex för cookieparameter](#cookie-parameter-regex) | Kontrollerar cookies som är associerade med varje begäran för det angivna reguljära uttrycket.
[Kant Cname](#edge-cname) | Identifierar begäranden som pekar på ett specifikt CNAME-kant.
[Refererande domän](#referring-domain) | Identifierar begäranden som refererades från de angivna värdnamnen.
[Literal för begär rubrik](#request-header-literal) | Identifierar begäranden som innehåller den angivna huvuduppsättningen till ett angivet värde.
[Begär rubrik Regex](#request-header-regex) | Identifierar begäranden som innehåller den angivna huvuduppsättningen till ett värde som matchar det angivna reguljära uttrycket.
[Jokertecken för begäran om huvud](#request-header-wildcard) | Identifierar begäranden som innehåller den angivna huvuduppsättningen till ett värde som matchar det angivna mönstret.
[Metod för begäran](#request-method) | Identifierar begäranden med http-metoden.
[Schema för begäran](#request-scheme) | Identifierar begäranden via http-protokollet.

## <a name="url-match-conditions"></a>Villkor för URL-matchning

Url-matchningsvillkoren identifierar begäranden baserat på deras webbadresser.

Namn | Syfte
-----|--------
[URL-sökvägskatalog](#url-path-directory) | Identifierar begäranden efter deras relativa sökväg.
[Url-sökvägstillägg](#url-path-extension) | Identifierar begäranden efter filnamnstillägget.
[Filnamn för URL-sökväg](#url-path-filename) | Identifierar begäranden efter filnamnet.
[URL-sökväg Litteral](#url-path-literal) | Jämför en begärans relativa sökväg med det angivna värdet.
[URL-sökväg Regex](#url-path-regex) | Jämför en begärans relativa sökväg med det angivna reguljära uttrycket.
[Jokertecken för URL-sökväg](#url-path-wildcard) | Jämför en begärans relativa sökväg med det angivna mönstret.
[URL-fråga litteral](#url-query-literal) | Jämför frågesträngen för en begäran med det angivna värdet.
[Parameter för URL-fråga](#url-query-parameter) | Identifierar begäranden som innehåller den angivna frågesträngparametern inställd på ett värde som matchar ett angivet mönster.
[URL-fråga Regex](#url-query-regex) | Identifierar begäranden som innehåller den angivna frågesträngparametern inställd på ett värde som matchar ett angivet reguljärt uttryck.
[Jokertecken för URL-fråga](#url-query-wildcard) | Jämför det angivna värdet mot begärans frågesträng.

## <a name="reference-for-rules-engine-match-conditions"></a>Referens för regler motor match villkor

---

### <a name="always"></a>Alltid

Villkoret Alltid matchning tillämpar en standarduppsättning funktioner på alla begäranden.

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>AS-nummer

AS-nummernätverket definieras av dess autonoma systemnummer (ASN). 

/Alternativet **Matches****Matchar inte** bestämmer villkoren för matchningsvillkoret AS-nummer:

- **Matchar**: Kräver att ASN för klientnätverket matchar ett av de angivna ASN:erna. 
- **Matchar inte:** Kräver att ASN för klientnätverket inte matchar något av de angivna ASN:erna.

Viktig information:

- Ange flera ASN:er genom att avgränsa var och en med ett enda blanksteg. Till exempel matchar 64514 64515 förfrågningar som kommer från antingen 64514 eller 64515.
- Vissa begäranden kanske inte returnerar ett giltigt ASN.Certain requests might not return a valid ASN. Ett frågetecken (?) matchar begäranden för vilka ett giltigt ASN inte kunde fastställas.
- Ange hela ASN för önskat nätverk. Partiella värden kommer inte att matchas.
- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
  - Fullständig cachefyllning
  - Standardintern maxålder
  - Tvinga inre max-ålder
  - Ignorera ursprungsnr cache
  - Intern Max-inaktuella

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>CDN Ursprung

MATCHNINGSvillkoret CDN Origin är uppfyllt när båda följande villkor är uppfyllda:

- Innehåll från CDN-lagring begärdes.
- Begäran URI använder den typ av innehållsåtkomstpunkt (till exempel /000001) som definieras i det här matchningsvillkoret:
  - CDN URL: Begäran URI måste innehålla den valda åtkomstpunkten för innehåll.
  - Edge CNAME URL: Motsvarande kant CNAME-konfiguration måste peka på den valda åtkomstpunkten för innehåll.
  
Viktig information:

- Åtkomstpunkten för innehåll identifierar den tjänst som ska betjäna det begärda innehållet.
- Använd inte ett OCH IF-uttalande för att kombinera vissa matchningsvillkor. Om du till exempel kombinerar ett MATCHNINGSvillkor för CDN Origin med ett matchningsvillkor för Kundursprung skulle ett matchningsmönster som aldrig kunde matchas. Därför kan två CDN Origin-matchningsvillkor inte kombineras via en OCH IF-sats.

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Klientens IP-adress

Alternativet**Matchar matchar inte** bestämmer villkoren för matchningsvillkoret för klient-IP-adress: **Matches**/

- **Matchar**: Kräver att klientens IP-adress matchar en av de angivna IP-adresserna. 
- **Matchar inte:** Kräver att klientens IP-adress inte matchar någon av de angivna IP-adresserna. 

Viktig information:

- Använd CIDR-notation.
- Ange flera IP-adresser och/eller IP-adressblock genom att avgränsa var och en med ett enda blanksteg. Ett exempel:
  - **IPv4-exempel:** 1.2.3.4 10.20.30.40 matchar alla begäranden som kommer från adressen 1.2.3.4 eller 10.20.30.40.
  - **IPv6-exempel**: 1:2:3:5:7:7:8 10:20:30:40:50:60:70:80 matchar alla begäranden som kommer från adressen 1:2:3:5:5:6:7:8 eller 10:20:30:40:50:60:70:80.
- Syntaxen för ett IP-adressblock är bas-IP-adressen följt av ett snedstreck och prefixstorleken. Ett exempel:
  - **IPv4-exempel:** 5.5.5.64/26 matchar alla begäranden som kommer från adresserna 5.5.5.64 till 5.5.5.127.
  - **IPv6-exempel**: 1:2:3:/48 matchar alla förfrågningar som kommer från adresser 1:2:3:0:0:0:0:0 till 1:2:3:ffff:ffff:ffff:ffff:ffff:ffff.
- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
  - Fullständig cachefyllning
  - Standardintern maxålder
  - Tvinga inre max-ålder
  - Ignorera ursprungsnr cache
  - Intern Max-inaktuella

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Cookie Parameter

/Alternativet **Matches****Matchar inte** avgör villkoren för matchning av cookieparametern.

- **Matchningar**: Kräver en begäran om att innehålla den angivna cookien med ett värde som matchar minst ett av de värden som definieras i det här matchningsvillkoret.
- **Matchar inte:** Kräver att begäran uppfyller något av följande villkor:
  - Den innehåller inte den angivna cookien.
  - Den innehåller den angivna cookien, men dess värde matchar inte något av de värden som definieras i det här matchningsvillkoret.
  
Viktig information:

- Cookie namn:
  - Eftersom jokerteckenvärden, inklusive asterisker (*), inte stöds när du anger ett cookie-namn, är endast exakta cookie-namnmatchningar berättigade till jämförelse.
  - Endast ett enda cookienamn kan anges per instans av det här matchningsvillkoret.
  - Jämförelser av cookienamn är skiftlägesokänsliga.
- Cookie-värde:
  - Ange flera cookie-värden genom att avgränsa var och en med ett enda blanksteg.
  - Ett cookie-värde kan dra nytta av [jokerteckenvärden.](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)
  - Om ett jokerteckenvärde inte har angetts kommer endast en exakt matchning att uppfylla det här matchningsvillkoret. Om du till exempel anger "Värde" matchas "Värde", men inte "Värde1" eller "Värde2".
  - Använd alternativet **Ignorera ärende** för att kontrollera om en skiftlägeskänslig jämförelse görs mot begärans cookie-värde.
- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
  - Fullständig cachefyllning
  - Standardintern maxålder
  - Tvinga inre max-ålder
  - Ignorera ursprungsnr cache
  - Intern Max-inaktuella

[Överst på sidan](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Regex för cookieparameter

Villkoret Cookie Parameter Regex matchar definierar ett cookienamn och ett värde. Du kan använda [reguljära uttryck](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) för att definiera önskat cookie-värde.

Alternativet Matchar inte avgör villkoren för att matcha cookieparametern Regex.The **Matches**/**Does Not Match** determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matchar**: Kräver en begäran om att innehålla den angivna cookien med ett värde som matchar det angivna reguljära uttrycket.
- **Matchar inte:** Kräver att begäran uppfyller något av följande villkor:
  - Den innehåller inte den angivna cookien.
  - Den innehåller den angivna cookien, men dess värde matchar inte det angivna reguljära uttrycket.
  
Viktig information:

- Cookie namn:
  - Eftersom reguljära uttryck och jokerteckenvärden, inklusive asterisker (*), inte stöds när du anger ett cookie-namn, är endast exakta cookienamnsmatchningar kvalificerade för jämförelse.
  - Endast ett enda cookienamn kan anges per instans av det här matchningsvillkoret.
  - Jämförelser av cookienamn är skiftlägesokänsliga.
- Cookie-värde:
  - Ett cookie-värde kan dra nytta av reguljära uttryck.
  - Använd alternativet **Ignorera ärende** för att kontrollera om en skiftlägeskänslig jämförelse görs mot begärans cookie-värde.
- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
  - Fullständig cachefyllning
  - Standardintern maxålder
  - Tvinga inre max-ålder
  - Ignorera ursprungsnr cache
  - Intern Max-inaktuella

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Land/region

Du kan ange ett land via landskoden. 

/Alternativet **Matches****Matchar inte** bestämmer villkoren för att matcha landvillkoret är uppfyllt:

- **Matchar**: Kräver att begäran innehåller de angivna landskodvärdena. 
- **Matchar inte**: Kräver att begäran inte innehåller de angivna landskodvärdena.

Viktig information:

- Ange flera landskoder genom att avgränsa var och en med ett enda blanksteg.
- Jokertecken stöds inte när du anger en landskod.
- Landskoderna "EU" och "AP" omfattar inte alla IP-adresser i dessa regioner.
- Vissa begäranden kanske inte returnerar en giltig landskod. Ett frågetecken (?) matchar begäranden för vilka en giltig landskod inte kunde fastställas.
- Landskoder är skiftlägeskänsliga.
- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
  - Fullständig cachefyllning
  - Standardintern maxålder
  - Tvinga inre max-ålder
  - Ignorera ursprungsnr cache
  - Intern Max-inaktuella

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementera landsfiltrering med hjälp av regelmotorn

Med det här matchningsvillkoret kan du utföra en mängd anpassningar baserat på den plats från vilken en begäran har sitt ursprung. Beteendet för funktionen Landsfiltrering kan till exempel replikeras via följande konfiguration:

- Matchning av URL-sökvägsã¤ã¤nder: Ange [matchningsvillkoret](#url-path-wildcard) url path matching to the directory that will be secured. 
    Lägg till en asterisk till slutet av den relativa sökvägen för att säkerställa att åtkomsten till alla dess underordnade kommer att begränsas av den här regeln.

- Landmatchning: Ställ in landmatchningsvillkoret på önskad uppsättning länder.
  - Tillåt: Ange villkoret landmatchning till **Matchar inte** så att endast de angivna länderna får åtkomst till innehåll som lagras på den plats som definieras av matchningsvillkoret för URL-sökväg.
  - Blockera: Ange villkoret Land matchning till **Matcher** för att blockera de angivna länderna från att komma åt innehåll som lagras på den plats som definieras av matchningsvillkoret för URL-sökväg.

- Funktionen Neka åtkomst (403): Aktivera [funktionen Neka åtkomst (403)](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) för att replikera tillåt- eller blockdelen av funktionen Landsfiltrering.

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Kundens ursprung

Viktig information:

- Matchningsvillkoret Kundursprung uppfylls oavsett om innehåll begärs via en CDN-URL eller en CNAME-url för kant som pekar på det valda kundursprunget.
- Det går inte att ta bort en kundursprungskonfiguration som refereras av en regel från sidan Kundursprung. Innan du försöker ta bort en konfiguration av kundens ursprung kontrollerar du att följande konfigurationer inte refererar till den:
  - Ett matchningsvillkor för kundursprung
  - En kant CNAME-konfiguration
- Använd inte ett OCH IF-uttalande för att kombinera vissa matchningsvillkor. Om du till exempel kombinerar ett matchningsvillkor för Kundursprung med ett MATCHNINGSvillkor för CDN Origin skulle ett matchningsmönster som aldrig kunde matchas. Därför kan två matchningsvillkor för Kundursprung inte kombineras via en OCH IF-sats.

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Enhet

Enhetsmatchningsvillkoret identifierar begäranden som görs från en mobil enhet baserat på dess egenskaper. Identifiering av mobila enheter uppnås genom [WURFL](http://wurfl.sourceforge.net/). 

/Alternativet **Matches****Matchar inte** avgör under vilka förhållanden enhetsmatchningsvillkoret är uppfyllt:

- **Matchar**: Kräver att beställarens enhet matchar det angivna värdet. 
- **Matchar inte**: Kräver att beställarens enhet inte matchar det angivna värdet.

Viktig information:

- Använd alternativet **Ignorera ärende** för att ange om det angivna värdet är skiftlägeskänsligt.
- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
  - Fullständig cachefyllning
  - Standardintern maxålder
  - Tvinga inre max-ålder
  - Ignorera ursprungsnr cache
  - Intern Max-inaktuella

#### <a name="string-type"></a>Strängtyp

En WURFL-funktion accepterar vanligtvis en kombination av siffror, bokstäver och symboler. På grund av den här funktionens flexibla karaktär måste du välja hur värdet som är associerat med det här matchningsvillkoret ska tolkas. I följande tabell beskrivs tillgängliga alternativ:

Typ     | Beskrivning
---------|------------
Bokstavlig  | Välj det här alternativet om du vill förhindra att de flesta tecken får särskild betydelse genom att använda deras [bokstavliga värde](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Jokertecken | Välj det här alternativet om du vill dra nytta av alla [jokertecken]([jokertecken](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Välj det här alternativet om du vill använda [reguljära uttryck](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Reguljära uttryck är användbara när du vill definiera ett teckenmönster.

#### <a name="wurfl-capabilities"></a>WURFL-funktioner

En WURFL-funktion refererar till en kategori som beskriver mobila enheter. Den valda funktionen avgör vilken typ av beskrivning av mobila enheter som används för att identifiera begäranden.

I följande tabell visas WURFL-funktioner och deras variabler för regelmotorn.

> [!NOTE]
> Följande variabler stöds i funktionerna **Ändra klientbegäran och** **ändra klientsvarshuvud.**

Funktion | Variabel | Beskrivning | Exempelvärden
-----------|----------|-------------|----------------
Varumärke | %{wurfl_cap_brand_name} | En sträng som anger enhetens varumärke. | Samsung
Enhet OS | %{wurfl_cap_device_os} | En sträng som anger vilket operativsystem som är installerat på enheten. | iOS
Enhetens operativsystemversion | %{wurfl_cap_device_os_version} | En sträng som anger versionsnumret för det operativsystem som är installerat på enheten. | 1.0.1
Dubbel orientering | %{wurfl_cap_dual_orientation} | En boolesk som anger om enheten stöder dubbel orientering. | true
HTML-önskad DTD | %{wurfl_cap_html_preferred_dtd} | En sträng som anger den mobila enhetens önskade dokumenttypsdefinition (DTD) för HTML-innehåll. | ingen<br/>xhtml_basic<br/>html5 (på andra)
Bild inlining | %{wurfl_cap_image_inlining} | En boolesk som anger om enheten stöder Base64-kodade bilder. | false
Är Android | %{wurfl_vcap_is_android} | En boolesk som anger om enheten använder Android OS. | true
Är IOS | %{wurfl_vcap_is_ios} | En boolesk som anger om enheten använder iOS. | false
Är Smart TV | %{wurfl_cap_is_smarttv} | En boolesk som anger om enheten är en smart-TV. | false
Är Smartphone | %{wurfl_vcap_is_smartphone} | En boolesk som anger om enheten är en smartphone. | true
Är Tablet | %{wurfl_cap_is_tablet} | En boolesk som anger om enheten är en surfplatta. Den här beskrivningen är OS-oberoende. | true
Är trådlös enhet | %{wurfl_cap_is_wireless_device} | En boolesk som anger om enheten betraktas som en trådlös enhet. | true
Marknadsföring Namn | %{wurfl_cap_marketing_name} | En sträng som anger enhetens marknadsföringsnamn. | BlackBerry 8100 Pärla
Mobil webbläsare | %{wurfl_cap_mobile_browser} | En sträng som anger webbläsaren som används för att begära innehåll från enheten. | Chrome
Mobil webbläsarversion | %{wurfl_cap_mobile_browser_version} | En sträng som anger vilken version av webbläsaren som används för att begära innehåll från enheten. | 31
Modellnamn | %{wurfl_cap_model_name} | En sträng som anger enhetens modellnamn. | s3 (s3)
Progressiv nedladdning | %{wurfl_cap_progressive_download} | En boolesk som anger om enheten stöder uppspelning av ljud och video medan den fortfarande laddas ned. | true
Utgivningsdatum | %{wurfl_cap_release_date} | En sträng som anger det år och den månad då enheten lades till i WURFL-databasen.<br/><br/>Format:`yyyy_mm` | 2013_december
Upplösning Höjd | %{wurfl_cap_resolution_height} | Ett heltal som anger enhetens höjd i pixlar. | 768
Upplösning Bredd | %{wurfl_cap_resolution_width} | Ett heltal som anger enhetens bredd i pixlar. | 1024

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Kant Cname

Viktig information:

- Listan över tillgängliga CNAMEs-kanter är begränsad till de kant-CNAMEs-nätverk som har konfigurerats på sidan Edge CNAMEs för den plattform där regelmotorn konfigureras.
- Innan du försöker ta bort en kant CNAME-konfiguration kontrollerar du att ett Edge Cname-matchningsvillkor inte refererar till det. Edge CNAME-konfigurationer som har definierats i en regel kan inte tas bort från sidan Edge CNAMEs.
- Använd inte ett OCH IF-uttalande för att kombinera vissa matchningsvillkor. Om du till exempel kombinerar ett Edge Cname-matchningsvillkor med ett matchningsvillkor för Kundursprung skulle ett matchningsmönster som aldrig kunde matchas skapas. Därför kan två Edge Cname-matchningsvillkor inte kombineras via en OCH IF-sats.
- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
  - Fullständig cachefyllning
  - Standardintern maxålder
  - Tvinga inre max-ålder
  - Ignorera ursprungsnr cache
  - Intern Max-inaktuella

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Refererande domän

Värdnamnet som är associerat med den refererare genom vilken innehåll begärdes avgör om villkoret Refererande domän är uppfyllt.

Alternativet**Matchar inte avgör** under vilka förhållanden matchningsvillkoret refererar domän är uppfyllt: **Matches**/

- **Matchar**: Kräver att det refererande värdnamnet matchar de angivna värdena. 
- **Matchar inte**: Kräver att det refererande värdnamnet inte matchar det angivna värdet.

Viktig information:

- Ange flera värdnamn genom att avgränsa var och en med ett enda blanksteg.
- Det här matchningsvillkoret stöder [jokerteckenvärden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Om det angivna värdet inte innehåller en asterisk måste det vara en exakt matchning för referentens värdnamn. Om du till exempel anger "mydomain.com" skulle det inte matcha "www.mydomain.com".
- Använd alternativet **Ignorera ärende** för att kontrollera om en skiftlägeskänslig jämförelse görs.
- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
  - Fullständig cachefyllning
  - Standardintern maxålder
  - Tvinga inre max-ålder
  - Ignorera ursprungsnr cache
  - Intern Max-inaktuella

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Literal för begär rubrik

/Alternativet **Matches****Matchar inte** avgör under vilka förhållanden matchningsvillkoret Förfråger matchar.

- **Matchar**: Kräver att begäran innehåller det angivna huvudet. Dess värde måste matcha det som definieras i det här matchningsvillkoret.
- **Matchar inte:** Kräver att begäran uppfyller något av följande villkor:
  - Den innehåller inte det angivna huvudet.
  - Den innehåller det angivna huvudet, men dess värde matchar inte det som definieras i det här matchningsvillkoret.
  
Viktig information:

- Jämförelser av rubriknamn är alltid skiftlägesokänsliga. Använd alternativet **Ignorera ärende** för att styra skiftlägeskänsligheten för jämförelser av huvudvärde.
- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
  - Fullständig cachefyllning
  - Standardintern maxålder
  - Tvinga inre max-ålder
  - Ignorera ursprungsnr cache
  - Intern Max-inaktuella

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Begär rubrik Regex

Alternativet **Matchar**/**inte** avgör under vilka förhållanden villkoret Begär rubrik Regex är uppfyllt.

- **Matchar**: Kräver att begäran innehåller det angivna huvudet. Dess värde måste matcha det mönster som definieras i det angivna [reguljära uttrycket](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Matchar inte:** Kräver att begäran uppfyller något av följande villkor:
  - Den innehåller inte det angivna huvudet.
  - Den innehåller det angivna huvudet, men dess värde matchar inte det angivna reguljära uttrycket.

Viktig information:

- Sidhuvudets namn:
  - Jämförelser av rubriknamn är skiftlägesokänsliga.
  - Ersätt blanksteg i rubriknamnet med %20.
- Huvudvärde:
  - Ett rubrikvärde kan dra nytta av reguljära uttryck.
  - Använd alternativet **Ignorera ärende** för att styra skiftlägeskänsligheten för jämförelser av huvudvärde.
  - Matchningsvillkoret uppfylls endast när ett huvudvärde exakt matchar minst ett av de angivna mönstren.
- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
  - Fullständig cachefyllning
  - Standardintern maxålder
  - Tvinga inre max-ålder
  - Ignorera ursprungsnr cache
  - Intern Max-inaktuella

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Jokertecken för begäran om huvud

Alternativet **Matchar**/**inte** avgör under vilka förhållanden matchningsvillkoret för begäranhuvudet är uppfyllt.

- **Matchar**: Kräver att begäran innehåller det angivna huvudet. Dess värde måste matcha minst ett av de värden som definieras i det här matchningsvillkoret.
- **Matchar inte:** Kräver att begäran uppfyller något av följande villkor:
  - Den innehåller inte det angivna huvudet.
  - Den innehåller det angivna huvudet, men dess värde matchar inte något av de angivna värdena.
  
Viktig information:

- Sidhuvudets namn:
  - Jämförelser av rubriknamn är skiftlägesokänsliga.
  - Blanksteg i rubriknamnet ska ersättas med %20. Du kan också använda det här värdet för att ange blanksteg i ett huvudvärde.
- Huvudvärde:
  - Ett rubrikvärde kan dra nytta av [jokerteckenvärden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Använd alternativet **Ignorera ärende** för att styra skiftlägeskänsligheten för jämförelser av huvudvärde.
  - Det här matchningsvillkoret uppfylls när ett huvudvärde exakt matchar minst ett av de angivna mönstren.
  - Ange flera värden genom att avgränsa var och en med ett enda blanksteg.
- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
  - Fullständig cachefyllning
  - Standardintern maxålder
  - Tvinga inre max-ålder
  - Ignorera ursprungsnr cache
  - Intern Max-inaktuella

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Metod för begäran

Matchningsvillkoret för begärandemetoden uppfylls endast när tillgångar begärs via den valda begärandemetoden. De tillgängliga metoderna för begäran är:

- HÄMTA
- HEAD
- POST
- Alternativ
- PUT
- DELETE
- Spåra
- Ansluta

Viktig information:

- Som standard kan endast GET-begäran metoden generera cachelagrat innehåll i nätverket. Alla andra begäransmetoder proxied genom nätverket.
- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
  - Fullständig cachefyllning
  - Standardintern maxålder
  - Tvinga inre max-ålder
  - Ignorera ursprungsnr cache
  - Intern Max-inaktuella

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Schema för begäran

Matchningsvillkoret för begärandeschema uppfylls endast när tillgångar begärs via det valda protokollet. De tillgängliga protokollen är:

- HTTP
- HTTPS

Viktig information:

- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
  - Fullständig cachefyllning
  - Standardintern maxålder
  - Tvinga inre max-ålder
  - Ignorera ursprungsnr cache
  - Intern Max-inaktuella

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>URL-sökvägskatalog

Identifierar en begäran efter den relativa sökvägen, som utesluter filnamnet på den begärda tillgången.

Alternativet**Matchar inte bestämmer** villkoren för matchning av URL-sökvägskatalogen. **Matches**/

- **Matchar**: Kräver att begäran innehåller en relativ URL-sökväg, exklusive filnamnet, som matchar det angivna URL-mönstret.
- **Matchar inte:** Kräver att begäran innehåller en relativ URL-sökväg, exklusive filnamn, som inte matchar det angivna URL-mönstret.

Viktig information:

- Använd alternativet **Relativt för** att ange om URL-jämförelsen ska starta före eller efter åtkomstpunkten för innehåll. Åtkomstpunkten för innehåll är den del av sökvägen som visas mellan Verizon CDN-värdnamnet och den relativa sökvägen till den begärda tillgången (till exempel /800001/CustomerOrigin). Den identifierar en plats efter servertyp (till exempel CDN eller kundursprung) och ditt kundkontonummer.

   Följande värden är tillgängliga för alternativet **Relativt:**
  - **Rot:** Anger att URL-jämförelsepunkten börjar direkt efter CDN-värdnamnet. 

  Till exempel:\/http: /wpc.0001. &lt;&gt;domän/**800001/myorigin/myfolder**/index.htm

  - **Ursprung**: Anger att URL-jämförelsepunkten börjar efter innehållsåtkomstpunkten (till exempel /000001 eller /800001/myorigin). Eftersom \*.azureedge.net CNAME skapas i förhållande till ursprungskatalogen på Verizon CDN-värdnamnet som standard, bör Azure CDN-användare använda **ursprungsvärdet.** 

  Till exempel:\//&lt;https:&gt;slutpunkt .azureedge.net/**myfolder**/index.htm 

  Den här URL:en pekar på följande\/Värdnamn för Verizon CDN: http: /wpc.0001. &lt;domän&gt;/800001/myorigin/**myfolder**/index.htm

- En CNAME-url för kant skrivs om till en CDN-URL före URL-jämförelsen.

    Båda följande webbadresser pekar till exempel på samma tillgång och har därför samma URL-sökväg.
  - CDN URL:\/http: /wpc.0001. &lt;domän&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Url till Edge CNAME:\//&lt;http: slutpunkt&gt;.azureedge.net/path/asset.htm
    
    Ytterligare information:
  - Anpassad domän:\/https: /my.domain.com/path/asset.htm
    
    - URL-sökväg (i förhållande till rot): /800001/CustomerOrigin/path/
    
    - URL-sökväg (i förhållande till ursprung): /path/

- Den del av URL:en som används för URL-jämförelsen avslutas strax före filnamnet på den begärda tillgången. Ett avslutande snedstreck framåt är det sista tecknet i den här typen av bana.

- Ersätt eventuella blanksteg i URL-sökvägen med %20.

- Varje URL-sökvägsmönster kan innehålla en eller flera asterisker (*), där varje asterisk matchar en sekvens med ett eller flera tecken.

- Ange flera URL-sökvägar i mönstret genom att avgränsa var och en med ett enda blanksteg.

    Till exempel: */sales/ */marketing/

- En URL-sökvägsspecifikation kan dra nytta av [jokerteckenvärden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Använd alternativet **Ignorera ärende** för att kontrollera om en skiftlägeskänslig jämförelse utförs.

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>Url-sökvägstillägg

Identifierar begäranden genom filtillägget för den begärda tillgången.

Alternativet**Matchar inte bestämmer** villkoren för matchningsvillkoret för URL-sökvägstillägg. **Matches**/

- **Matchar**: Kräver att URL:en för begäran innehåller ett filnamnstillägg som exakt matchar det angivna mönstret.

   Om du till exempel anger "htm", "htm"-tillgångar matchas, men inte "html"-tillgångar.  

- **Matchar inte:** Kräver att URL-begäran innehåller ett filnamnstillägg som inte matchar det angivna mönstret.

Viktig information:

- Ange vilka filtillägg som ska matchas i rutan **Värde.** Ta inte med en inledande period. Använd till exempel htm i stället för .htm.

- Använd alternativet **Ignorera ärende** för att kontrollera om en skiftlägeskänslig jämförelse utförs.

- Ange flera filtillägg genom att avgränsa varje tillägg med ett enda blanksteg. 

    Till exempel: htm html

- Om du till exempel anger "htm" matchar "htm"-tillgångar, men inte "html"-tillgångar.

#### <a name="sample-scenario"></a>Exempelscenario

Följande exempelkonfiguration förutsätter att det här matchningsvillkoret uppfylls när en begäran matchar ett av de angivna tilläggen.

Värdespecifikation: asp aspx php html

Det här matchningsvillkoret uppfylls när webbadresserna som slutar med följande tillägg:

- .asp
- .aspx
- .php (på en)
- .html

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>Filnamn för URL-sökväg

Identifierar begäranden med filnamnet på den begärda tillgången. I det här matchningsvillkoret består ett filnamn av namnet på den begärda tillgången, en period och filtillägget (till exempel index.html).

Alternativet**Matchar inte bestämmer** villkoren för matchning av URL-sökvägsfilnamn. **Matches**/

- **Matchar**: Kräver att begäran innehåller ett filnamn i url-sökvägen som matchar det angivna mönstret.
- **Matchar inte:** Kräver att begäran innehåller ett filnamn i url-sökvägen som inte matchar det angivna mönstret.

Viktig information:

- Använd alternativet **Ignorera ärende** för att kontrollera om en skiftlägeskänslig jämförelse utförs.

- Om du vill ange flera filtillägg separerar du varje tillägg med ett enda blanksteg.

    Till exempel: index.htm index.html

- Ersätt blanksteg i ett filnamnsvärde med %20.

- Ett filnamnsvärde kan dra nytta av [jokerteckenvärden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Varje filnamnsmönster kan till exempel bestå av en eller flera asterisker (*), där varje asterisk matchar en sekvens med ett eller flera tecken.

- Om jokertecken inte anges kommer endast en exakt matchning att uppfylla detta matchningsvillkor.

    Om du till exempel anger "presentation.ppt" matchar du en tillgång med namnet "presentation.ppt", men inte en med namnet "presentation.pptx".

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>URL-sökväg Litteral

Jämför url-sökvägen för en begäran, inklusive filnamn, med det angivna värdet.

Alternativet**Matchar inte bestämmer** villkoren för att matcha villkoret för URL-sökvägs litteral. **Matches**/

- **Matchar**: Kräver att begäran innehåller en URL-sökväg som matchar det angivna mönstret.
- **Matchar inte:** Kräver att begäran innehåller en URL-sökväg som inte matchar det angivna mönstret.

Viktig information:

- Använd alternativet **Relativt för** att ange om url-jämförelsepunkten ska börja före eller efter åtkomstpunkten för innehåll. 

    Följande värden är tillgängliga för alternativet **Relativt:**
  - **Rot:** Anger att URL-jämförelsepunkten börjar direkt efter CDN-värdnamnet.

    Till exempel:\/http: /wpc.0001. &lt;&gt;domän/**800001/myorigin/myfolder/index.htm**

  - **Ursprung**: Anger att URL-jämförelsepunkten börjar efter innehållsåtkomstpunkten (till exempel /000001 eller /800001/myorigin). Eftersom \*.azureedge.net CNAME skapas i förhållande till ursprungskatalogen på Verizon CDN-värdnamnet som standard, bör Azure CDN-användare använda **ursprungsvärdet.** 

    Till exempel:\//&lt;https:&gt;slutpunkt .azureedge.net/**myfolder/index.htm**

  Den här URL:en pekar på följande\/Värdnamn för Verizon CDN: http: /wpc.0001. &lt;domän&gt;/800001/myorigin/**myfolder/index.htm**

- En CNAME-url för kant skrivs om till en CDN-URL före en URL-jämförelse.

Båda följande webbadresser pekar till exempel på samma tillgång och har därför samma URL-sökväg:

- CDN URL:\/http: /wpc.0001. &lt;domän&gt;/800001/CustomerOrigin/path/asset.htm
- Url till Edge CNAME:\//&lt;http: slutpunkt&gt;.azureedge.net/path/asset.htm

    Ytterligare information:
    
    - URL-sökväg (relativ till rot): /800001/CustomerOrigin/path/asset.htm
   
    - URL-sökväg (i förhållande till ursprung): /path/asset.htm

- Frågesträngar i URL:en ignoreras.
- Använd alternativet **Ignorera ärende** för att kontrollera om en skiftlägeskänslig jämförelse utförs.
- Värdet som anges för det här matchningsvillkoret jämförs med den relativa sökvägen för den exakta begäran som gjorts av klienten.

- Om du vill matcha alla begäranden som görs till en viss katalog använder du [URL-sökvägskatalogen](#url-path-directory) eller [matchningsvillkoret för URL-sökväg.](#url-path-wildcard)

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>URL-sökväg Regex

Jämför url-sökvägen för en begäran med det angivna [reguljära uttrycket](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

Alternativet**Matchar inte avgör** under vilka förhållanden villkoret URL Path Regex är uppfyllt. **Matches**/

- **Matchar**: Kräver att begäran innehåller en URL-sökväg som matchar det angivna reguljära uttrycket.
- **Matchar inte:** Kräver att begäran innehåller en URL-sökväg som inte matchar det angivna reguljära uttrycket.

Viktig information:

- En CNAME-url för kant skrivs om till en CDN-URL före url-jämförelse.

    Båda webbadresserna pekar till exempel på samma tillgång och har därför samma URL-sökväg.

     - CDN URL:\/http: /wpc.0001. &lt;domän&gt;/800001/CustomerOrigin/path/asset.htm

     - Url till Edge CNAME: http:\//my.domain.com/path/asset.htm

    Ytterligare information:
    
     - URL-sökväg: /800001/CustomerOrigin/path/asset.htm

- Frågesträngar i URL:en ignoreras.
    
- Använd alternativet **Ignorera ärende** för att kontrollera om en skiftlägeskänslig jämförelse utförs.
    
- Blanksteg i URL-sökvägen ska ersättas med %20.

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>Jokertecken för URL-sökväg

Jämför en begärans relativa URL-sökväg med det angivna jokertecknet.

Alternativet**Matchar inte avgör** under vilka förhållanden matchningsvillkoret url-sökväg är uppfyllt. **Matches**/

- **Matchar**: Kräver att begäran innehåller en URL-sökväg som matchar det angivna jokertecknet.
- **Matchar inte:** Kräver att begäran innehåller en URL-sökväg som inte matchar det angivna jokertecknet.

Viktig information:

- **I förhållande till** alternativet: Det här alternativet avgör om url-jämförelsepunkten börjar före eller efter åtkomstpunkten för innehåll.

   Det här alternativet kan ha följande värden:
     - **Rot:** Anger att URL-jämförelsepunkten börjar direkt efter CDN-värdnamnet.

       Till exempel:\/http: /wpc.0001. &lt;&gt;domän/**800001/myorigin/myfolder/index.htm**

     - **Ursprung**: Anger att URL-jämförelsepunkten börjar efter innehållsåtkomstpunkten (till exempel /000001 eller /800001/myorigin). Eftersom \*.azureedge.net CNAME skapas i förhållande till ursprungskatalogen på Verizon CDN-värdnamnet som standard, bör Azure CDN-användare använda **ursprungsvärdet.** 

       Till exempel:\//&lt;https:&gt;slutpunkt .azureedge.net/**myfolder/index.htm**

     Den här URL:en pekar på följande\/Värdnamn för Verizon CDN: http: /wpc.0001. &lt;domän&gt;/800001/myorigin/**myfolder/index.htm**

- En CNAME-url för kant skrivs om till en CDN-URL före url-jämförelse.

    Båda följande webbadresser pekar till exempel på samma tillgång och har därför samma URL-sökväg:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Url till Edge CNAME:\//&lt;http: slutpunkt&gt;.azureedge.net/path/asset.htm
    
    Ytterligare information:
    
     - URL-sökväg (relativ till rot): /800001/CustomerOrigin/path/asset.htm
    
     - URL-sökväg (i förhållande till ursprung): /path/asset.htm
    
- Ange flera URL-sökvägar genom att avgränsa var och en med ett enda blanksteg.

   Till exempel: /marketing/asset.* /sales/*.htm

- Frågesträngar i URL:en ignoreras.
    
- Använd alternativet **Ignorera ärende** för att kontrollera om en skiftlägeskänslig jämförelse utförs.
    
- Ersätt blanksteg i URL-sökvägen med %20.
    
- Det värde som anges för en URL-sökväg kan dra nytta av [jokerteckenvärden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Varje URL-sökvägsmönster kan innehålla en eller flera asterisker (*), där varje asterisk kan matcha en sekvens med ett eller flera tecken.

#### <a name="sample-scenarios"></a>Exempel på scenarier

Exempelkonfigurationerna i följande tabell förutsätter att det här matchningsvillkoret uppfylls när en begäran matchar det angivna URL-mönstret:

Värde                   | I förhållande till    | Resultat 
------------------------|----------------|-------
*/test.html */test.php  | Rot eller ursprung | Detta mönster matchas av begäranden om tillgångar som heter "test.html" eller "test.php" i någon mapp.
/80ABCD/ursprung/text/*   | Rot           | Det här mönstret matchas när den begärda tillgången uppfyller följande kriterier: <br />- Den måste finnas på en kundursprung som kallas "ursprung". <br />- Den relativa sökvägen måste börja med en mapp som heter "text". Det vill än, den begärda tillgången kan antingen finnas i "text"-mappen eller en av dess rekursiva undermappar.
*/css/* */js/*          | Rot eller ursprung | Det här mönstret matchas av alla CNAME- eller edge-CNAME-URL:er som innehåller en css- eller js-mapp.
*.jpg *.gif *.png       | Rot eller ursprung | Det här mönstret matchas av alla CNAME-url:er för CDN eller kant som slutar med .jpg, .gif eller .png. Ett alternativt sätt att ange det här mönstret är med [matchningsvillkoret för URL-sökvägstillägg](#url-path-extension).
/bilder/* /media/*      | Ursprung         | Det här mönstret matchas av CDN- eller edge-CNAME-url:er vars relativa sökväg börjar med en "images" eller "media"-mapp. <br />- CDN URL:\/http: /wpc.0001. &lt;domän&gt;/800001/myorigin/images/sales/event1.png<br />- Exempel kant CNAME\/URL: http: /cdn.mydomain.com/images/sales/event1.png

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>URL-fråga litteral

Jämför frågesträngen för en begäran med det angivna värdet.

Alternativet**Matchar matchar inte** bestämmer villkoren för att matcha villkoret för URL-frågelitteralmatchning. **Matches**/

- **Matchar**: Kräver att begäran innehåller en URL-frågesträng som matchar den angivna frågesträngen.
- **Matchar inte:** Kräver att begäran innehåller en URL-frågesträng som inte matchar den angivna frågesträngen.

Viktig information:

- Endast exakta frågesträngmatchningar uppfyller det här matchningsvillkoret.
    
- Använd alternativet **Ignorera ärende** för att styra skiftlägeskänsligheten för frågesträngjämförelser.
    
- Ta inte med ett inledande frågetecken (?) i frågesträngens värdetext.
    
- Vissa tecken kräver URL-kodning. Använd procentsymbolen för att URL-koda följande tecken:

   Tecken | URL-kodning
   ----------|---------
   Space     | %20
   &         | %25

- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
   - Fullständig cachefyllning
   - Standardintern maxålder
   - Tvinga inre max-ålder
   - Ignorera ursprungsnr cache
   - Intern Max-inaktuella

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>Parameter för URL-fråga

Identifierar begäranden som innehåller den angivna frågesträngparametern. Den här parametern är inställd på ett värde som matchar ett angivet mönster. Frågesträngparametrar (till exempel parameter=värde) i begäran URL avgöra om detta villkor är uppfyllt. Det här matchningsvillkoret identifierar en frågesträngparameter med dess namn och accepterar ett eller flera värden för parametervärdet. 

Alternativet**Matchar matchar inte** bestämmer villkoren för matchningsvillkoret för URL-frågeparametern. **Matches**/

- **Matchar**: Kräver en begäran om att innehålla den angivna parametern med ett värde som matchar minst ett av de värden som definieras i det här matchningsvillkoret.
- **Matchar inte:** Kräver att begäran uppfyller något av följande villkor:
  - Den innehåller inte den angivna parametern.
  - Den innehåller den angivna parametern, men dess värde matchar inte något av de värden som definieras i det här matchningsvillkoret.

Det här matchningsvillkoret är ett enkelt sätt att ange kombinationer av parameternamn/värde. Om du vill ha mer flexibilitet om du matchar en frågesträngparameter bör du överväga att använda matchningsvillkoret för [URL Query-jokertecken.](#url-query-wildcard)

Viktig information:

- Endast ett enda URL-frågeparameternamn kan anges per instans av det här matchningsvillkoret.
    
- Eftersom jokerteckenvärden inte stöds när ett parameternamn anges kan endast exakta parameternamnmatchningar jämföras.
- Parametervärden kan innehålla [jokerteckenvärden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Varje parametervärdemönster kan bestå av en eller flera asterisker (*), där varje asterisk kan matcha en sekvens med ett eller flera tecken.
   - Vissa tecken kräver URL-kodning. Använd procentsymbolen för att URL-koda följande tecken:

       Tecken | URL-kodning
       ----------|---------
       Space     | %20
       &         | %25

- Ange flera frågesträngparametervärden genom att avgränsa var och en med ett enda blanksteg. Det här matchningsvillkoret uppfylls när en begäran innehåller en av de angivna namn-/värdekombinationerna.

   - Exempel 1:

     - Konfiguration:

       Värde- värdeB

     - Den här konfigurationen matchar följande frågesträngparametrar:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Exempel 2:

     - Konfiguration: 

        Värde%20A Värde%20B

     - Den här konfigurationen matchar följande frågesträngparametrar:

       Parameter1=Värde%20A

       Parameter1=Värde%20B

- Det här matchningsvillkoret uppfylls endast när det finns en exakt matchning till minst en av de angivna frågesträngnamn/värdekombinationerna.

   Om du till exempel använder konfigurationen i föregående exempel betraktas inte parameternamn/värdekombinationen "Parameter1=ValueAdd" som en matchning. Om du anger något av följande värden matchar det dock den kombinationen av namn/värde:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Använd alternativet **Ignorera ärende** för att styra skiftlägeskänsligheten för frågesträngjämförelser.
    
- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
   - Fullständig cachefyllning
   - Standardintern maxålder
   - Tvinga inre max-ålder
   - Ignorera ursprungsnr cache
   - Intern Max-inaktuella

#### <a name="sample-scenarios"></a>Exempelscenarier

I följande exempel visas hur det här alternativet fungerar i specifika situationer:

Namn  | Värde |  Resultat
------|-------|--------
Användare  | Joe   | Det här mönstret matchas när frågesträngen för en begärd WEBBADRESS är "?user=joe".
Användare  | *     | Det här mönstret matchas när frågesträngen för en begärd URL innehåller en användarparameter.
E-post | Joe\* | Det här mönstret matchas när frågesträngen för en begärd URL innehåller en e-postparameter som börjar med "Joe".

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>URL-fråga Regex

Identifierar begäranden som innehåller den angivna frågesträngparametern. Den här parametern är inställd på ett värde som matchar ett angivet [reguljärt uttryck](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

/Alternativet **Matches****Matchar inte** avgör under vilka förhållanden url-frågevillkoret Regex är uppfyllt.

- **Matchar**: Kräver att begäran innehåller en URL-frågesträng som matchar det angivna reguljära uttrycket.
- **Matchar inte:** Kräver att begäran innehåller en URL-frågesträng som inte matchar det angivna reguljära uttrycket.

Viktig information:

- Endast exakta matchningar till det angivna reguljära uttrycket uppfyller det här matchningsvillkoret.
    
- Använd alternativet **Ignorera ärende** för att styra skiftlägeskänsligheten för frågesträngjämförelser.
    
- I det här alternativet börjar en frågesträng med det första tecknet efter frågetecknet (?) avgränsare för frågesträngen.
    
- Vissa tecken kräver URL-kodning. Använd procentsymbolen för att URL-koda följande tecken:

   Tecken | URL-kodning | Värde
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Observera att procentsymboler måste komma undan.

- Dubbel-escape speciella reguljära uttryck \^tecken (till exempel $.+) för att inkludera ett omvänt snedstreck i det reguljära uttrycket.

   Ett exempel:

   Värde | Tolkas som 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
   - Fullständig cachefyllning
   - Standardintern maxålder
   - Tvinga inre max-ålder
   - Ignorera ursprungsnr cache
   - Intern Max-inaktuella

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>Jokertecken för URL-fråga

Jämför det angivna värdet/värdena mot begärans frågesträng.

/Alternativet **Matches****Matchar inte** avgör under vilka förhållanden matchningsvillkoret för URL Query är uppfyllt.

- **Matchar**: Kräver att begäran innehåller en URL-frågesträng som matchar det angivna jokertecknet.
- **Matchar inte:** Kräver att begäran innehåller en URL-frågesträng som inte matchar det angivna jokertecknet.

Viktig information:

- I det här alternativet börjar en frågesträng med det första tecknet efter frågetecknet (?) avgränsare för frågesträngen.
- Parametervärden kan innehålla [jokerteckenvärden:](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)
   - Varje parametervärdemönster kan bestå av en eller flera asterisker (*), där varje asterisk kan matcha en sekvens med ett eller flera tecken.
   - Vissa tecken kräver URL-kodning. Använd procentsymbolen för att URL-koda följande tecken:

     Tecken | URL-kodning
     ----------|---------
     Space     | %20
     &         | %25

- Ange flera värden genom att avgränsa var och en med ett enda blanksteg.

   Till exempel: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Endast exakta matchningar till minst ett av de angivna frågesträngmönster uppfyller det här matchningsvillkoret.
    
- Använd alternativet **Ignorera ärende** för att styra skiftlägeskänsligheten för frågesträngjämförelser.
    
- På grund av hur cacheinställningar spåras är det här matchningsvillkoret inkompatibelt med följande funktioner:
   - Fullständig cachefyllning
   - Standardintern maxålder
   - Tvinga inre max-ålder
   - Ignorera ursprungsnr cache
   - Intern Max-inaktuella

#### <a name="sample-scenarios"></a>Exempelscenarier

I följande exempel visas hur det här alternativet fungerar i specifika situationer:

 Namn                 | Beskrivning
 ---------------------|------------
användare=joe              | Det här mönstret matchas när frågesträngen för en begärd WEBBADRESS är "?user=joe".
\*användare=\* \*optout=\* | Det här mönstret matchas när CDN-URL-frågan innehåller antingen parametern användare eller optout.

[Överst på sidan](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Content Delivery Network](cdn-overview.md)
- [Referens för regler motor](cdn-verizon-premium-rules-engine-reference.md)
- [Regler motor villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regler motorfunktioner](cdn-verizon-premium-rules-engine-reference-features.md)
- [Åsidosätta standard-HTTP-beteende med hjälp av regelmotorn](cdn-verizon-premium-rules-engine.md)
