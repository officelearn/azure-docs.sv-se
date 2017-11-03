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
ms.openlocfilehash: f4886b1d78dfa87cf25737fb46c12b5963034f27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Azure CDN regelmotor matchar villkoren
Det här avsnittet innehåller detaljerade beskrivningar av tillgängliga matchar villkoren för Azure Content Delivery Network (CDN) [regelmotor](cdn-rules-engine.md).

Den andra delen av en regel är matchar villkoret. En matchar villkoret identifierar vissa typer av begäranden som en uppsättning funktioner kommer att utföras.

Till exempel kan den användas för att filtrera förfrågningar om innehåll på en viss plats begäranden som genereras från en viss IP-adress eller ett land eller av huvudinformation.

## <a name="always"></a>Alltid

Alltid matchar villkoret är avsedd att gäller en standarduppsättning med funktioner för alla begäranden.

## <a name="device"></a>Enhet

Enheten matchar villkoret identifierar begäranden från en mobil enhet baserat på dess egenskaper.  Identifiering av mobila enheter uppnås genom [WURFL](http://wurfl.sourceforge.net/).  WURFL funktioner och deras CDN regelmotor variabler visas nedan.

> [!NOTE] 
> Variablerna nedan stöds i den **ändra klienten begär huvud** och **ändra klienten svarshuvud** funktioner.

Funktion | Variabel | Beskrivning | Exempel värden
-----------|----------|-------------|----------------
Varumärke | % {wurfl_cap_brand_name} | En sträng som anger namnet på enheten. | Samsung
Enhetens OS | % {wurfl_cap_device_os} | En sträng som anger operativsystemet installerat på enheten. | IOS
Enhetens operativsystemversion | % {wurfl_cap_device_os_version} | En sträng som anger versionsnumret för Operativsystemet som är installerad på enheten. | 1.0.1
Dubbla orientering | % {wurfl_cap_dual_orientation} | Ett booleskt värde som anger om enheten har stöd för dubbel orientering. | SANT
HTML önskad DTD | % {wurfl_cap_html_preferred_dtd} | En sträng som anger den mobila enheten prioriterade dokumenttypsdefinition (DTD) för HTML-innehåll. | Ingen<br/>xhtml_basic<br/>HTML5
Bild Inlining | % {wurfl_cap_image_inlining} | Ett booleskt värde som anger om enheten stöder Base64-kodade bilder. | FALSKT
Är Android | % {wurfl_vcap_is_android} | Ett booleskt värde som anger om enheten använder i Android OS. | SANT
Är IOS | % {wurfl_vcap_is_ios} | Ett booleskt värde som anger om enheten använder iOS. | FALSKT
Är Smart TV | % {wurfl_cap_is_smarttv} | Ett booleskt värde som anger om enheten är en smart TV. | FALSKT
Är Smartphone | % {wurfl_vcap_is_smartphone} | Ett booleskt värde som anger om enheten är en smartphone. | SANT
Är Tablet | % {wurfl_cap_is_tablet} | Ett booleskt värde som anger om enheten är en surfplatta. Det här är en oberoende av OS-beskrivning. | SANT
Är trådlösa enheter | % {wurfl_cap_is_wireless_device} | Ett booleskt värde som anger om enheten betraktas som en trådlös enhet. | SANT
Marknadsföring namn | % {wurfl_cap_marketing_name} | En sträng som anger marknadsföring enhetsnamn. | BlackBerry 8100 Pearl
Mobila webbläsare | % {wurfl_cap_mobile_browser} | En sträng som anger den webbläsare som begär innehåll från enheten. | Chrome
Mobila webbläsarversion | % {wurfl_cap_mobile_browser_version} | En sträng som anger versionen av webbläsaren för att begära innehåll från enheten. | 31
Modellnamnet | % {wurfl_cap_model_name} | En sträng som anger enhetens namn. | S3
Progressiv hämtning | % {wurfl_cap_progressive_download} | Ett booleskt värde som anger om enheten har stöd för uppspelning av ljud och video medan den hämtas fortfarande. | SANT
Utgivningsdatum | % {wurfl_cap_release_date} | En sträng som anger år och månad då enheten har lagts till WURFL-databasen.<br/><br/>Format:`yyyy_mm` | 2013_december
Lösning höjd | % {wurfl_cap_resolution_height} | Ett heltal som anger enhetens höjd i bildpunkter. | 768
Lösning bredd | % {wurfl_cap_resolution_width} | Ett heltal som anger enhetens bredd i bildpunkter. | 1024


## <a name="location"></a>Plats

Dessa villkor är avsedda att identifiera förfrågningar baserat på förfrågarens plats matchning.

Namn | Syfte
-----|--------
SOM tal | Identifierar förfrågningar som kommer från ett visst nätverk.
Land/region | Identifierar förfrågningar som kommer från de angivna länderna.


## <a name="origin"></a>Ursprung

Dessa villkor är avsedda att identifiera matcha begäranden som pekar på CDN lagring eller en kund ursprungsservern.

Namn | Syfte
-----|--------
CDN ursprung | Identifierar förfrågningar om innehåll som lagras på CDN lagring.
Kunden ursprung | Identifierar förfrågningar om innehåll som lagras på en viss kund ursprungsservern.


## <a name="request"></a>Förfrågan

Dessa villkor är avsedda att identifiera förfrågningar baserat på deras egenskaper matchar.

Namn | Syfte
-----|--------
Klientens IP-adress | Identifierar förfrågningar som kommer från en viss IP-adress.
Cookie-Parameter | Kontrollerar cookies som är associerade med varje begäran för det angivna värdet.
Cookie-parametern Regex | Kontrollerar cookies som är associerade med varje begäran för det angivna reguljära uttrycket.
Edge Cname | Identifierar begäranden som pekar på en specifik kant CNAME-post.
Refererande domän | Identifierar begäranden som har hänvisats från den angivna hostname(s).
Begäran sidhuvud Literal | Identifierar begäranden som innehåller det angivna huvudet inställt på angivna värden.
Begäran sidhuvud Regex | Identifierar begäranden som innehåller det angivna huvudet inställt på ett värde som matchar det angivna reguljära uttrycket.
Begäran huvud med jokertecken | Identifierar begäranden som innehåller det angivna huvudet inställt på ett värde som matchar det angivna mönstret.
Metod för begäran | Identifierar begäranden via sina HTTP-metoden.
Schemat för begäran | Identifierar begäranden via sina HTTP-protokollet.

## <a name="url"></a>URL: EN

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

