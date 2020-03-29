---
title: Core Rapporter från Verizon | Microsoft-dokument
description: 'Du kan visa användningsmönster för CDN med hjälp av följande rapporter: Bandbredd, Överförda data, Träffar, Cachestatus, Cache träffförhållande, IPV4/IPV6-data som överförts.'
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d48ddafdc1ec30ae1533b3a3101582f33e7f4b5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594164"
---
# <a name="core-reports-from-verizon"></a>Core Rapporter från Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Genom att använda Verizon Core Reports via hantera portalen för Verizon-profiler kan du visa användningsmönster för cdn med följande rapporter:

* Bandbredd
* Överförda uppgifter
* Träffar
* Cachestatus
* Träffförhållande för cache
* IPV4/IPV6-data som överförts

## <a name="accessing-verizon-core-reports"></a>Komma åt Verizon Core Reports
1. Klicka på knappen **Hantera** i CDN-profilbladet.
   
    ![Knappen Hantera CDN-profil](./media/cdn-reports/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över fliken **Analytics** och hovra sedan över det utfällbara **kärnrapporterna.** Klicka på en rapport i menyn.
   
    ![CDN-hanteringsportal - Core Reports-menyn](./media/cdn-reports/cdn-core-reports.png)

3. För varje rapport väljer du ett datumintervall från listan **Datumintervall.** Du kan antingen välja ett fördefinierat datumintervall, till exempel **Idag** eller **Den här veckan,** eller så kan du välja **Anpassad** och manuellt ange ett datumintervall genom att klicka på kalenderikonerna. 

4. När du har valt ett datumintervall klickar du på **Gå** för att generera rapporten. 

4. Om du vill exportera data i Excel-format klickar du på Excel-ikonen ovanför knappen **Gå.**

## <a name="bandwidth"></a>Bandbredd
Bandbreddsrapporten består av ett diagram och en datatabell som anger CDN-bandbreddsanvändningen för HTTP och HTTPS under en viss tidsperiod, i Mbps. Du kan visa bandbreddsanvändningen för alla POP-adresser eller för en viss POP. Med den här rapporten kan du visa trafiktoppar och distribution för POP.This report allows you to view the traffic spikes and distribution for POPs.

I listan **Kantnoder** väljer du **Alla kantnoder** för att visa trafik från alla noder eller välja en viss region.

Rapporten uppdateras var femte minut.

![Rapport om bandbredd](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Överförda uppgifter
Den här rapporten består av ett diagram och en datatabell som anger CDN-trafikanvändningen för HTTP och HTTPS under en viss tidsperiod, i GB. Du kan visa trafikanvändningen för alla POP-adresser eller för en viss POP. Med den här rapporten kan du visa trafiktoppar och distribution mellan POP.This report allows you to view the traffic spikes and distribution across POPs.

I listan **Kantnoder** väljer du **Alla kantnoder** för att visa trafik från alla noder eller välja en viss region.

Rapporten uppdateras var femte minut.

![Överförd rapport](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Träffar (statuskoder)
I den här rapporten beskrivs distributionen av statuskoder för begäran för ditt innehåll. Varje begäran om innehåll genererar en HTTP-statuskod. Statuskoden beskriver hur kant-POP hanterade begäran. En 2xx-statuskod anger till exempel att begäran har skickats till en klient, medan en 4xx-statuskod anger att ett fel uppstod. Mer information om HTTP-statuskoder finns i [Lista över HTTP-statuskoder](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Träffar rapport](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Cachestatus
I den här rapporten beskrivs distributionen av cacheträffar och cachemissar för klientbegäranden. Eftersom de snabbaste prestandaresultaten från cacheträffar kan du optimera dataleveranshastigheter genom att minimera cachemissar och utgångna cacheträffar. 

Om du vill minska cachemissar konfigurerar du ursprungsservern så att du minimerar användningen av följande: 
 * `no-cache`svarsrubriker
 * Cachelagring av frågesträngar, om det inte är absolut nödvändigt  
 * Svarskoder som inte kan cachelagras

Om du vill minska cacheträffar `max-age` som utgångna har du angett en tillgångs till en lång period för att minimera antalet begäranden till ursprungsservern.

![Rapport över cachestatus](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Huvudcachestatus inkluderar:
* TCP_HIT: Serveras från kantserver. Objektet fanns i cacheminnet och har inte överskridit sin maxålder.
* TCP_MISS: Serveras från ursprungsservern. Objektet fanns inte i cacheminnet och svaret var tillbaka till ursprunget.
* TCP_EXPIRED _MISS: Serveras från ursprungsservern efter förlängning med ursprung. Objektet fanns i cacheminnet, men hade överskridit sin maxålder. En förlängning med ursprung resulterade i att cacheobjektet ersattes av ett nytt svar från ursprunget.
* TCP_EXPIRED _HIT: Serveras från Edge efter förlängning med ursprung. Objektet var i cacheminnet men hade överskridit sin maxålder. En förlängning med ursprungsservern resulterade i att cacheobjektet inte ändras.

### <a name="full-list-of-cache-statuses"></a>Fullständig lista över cachestatus
* TCP_HIT - Den här statusen rapporteras när en begäran visas direkt från POP till klienten. En tillgång visas omedelbart från en POP när den cachelagras på popen närmast klienten och har en giltig time-to-live (TTL). TTL bestäms av följande svarshuvuden:
  
  * Cache-kontroll: s-maxage
  * Cache-kontroll: max-ålder
  * Upphör
* TCP_MISS: Den här statusen anger att en cachelagd version av den begärda tillgången inte hittades på pop närmast klienten. Tillgången begärs från antingen en ursprungsserver eller en ursprungssköldserver. Om ursprungsservern eller ursprungssköldservern returnerar en tillgång, serveras den till klienten och cachelagras på både klienten och kantservern. Annars returneras en statuskod som inte är 200 (till exempel 403 Förbjuden eller 404 hittades inte).
* TCP_EXPIRED_HIT: Den här statusen rapporteras när en begäran som riktar sig till en tillgång med en förfallen TTL har serverats direkt från POP till klienten. Till exempel när tillgångens maxålder har gått ut. 
  
   En begäran om utgångna resultat resulterar vanligtvis i en begäran om förlängning till ursprungsservern. För att en TCP_EXPIRED_HIT status ska inträffa måste ursprungsservern ange att det inte finns någon nyare version av tillgången. Den här situationen resulterar vanligtvis i en uppdatering av tillgångens cachekontroll och upphör att gälla.
* TCP_EXPIRED_MISS: Den här statusen rapporteras när en nyare version av en cachelagd tillgång som har upphört att gälla visas från POP till klienten. Den här statusen inträffar när TTL för en cachelagrad tillgång har upphört att gälla (till exempel utgångna maxålder) och ursprungsservern returnerar en nyare version av tillgången. Den här nya versionen av tillgången visas till klienten i stället för den cachelagrade versionen. Dessutom cachelagras den på kantservern och klienten.
* CONFIG_NOCACHE: Den här statusen anger att en kundspecifik konfiguration kant POP hindrade tillgången från att cachelagras.
* NONE - Den här statusen anger att en cacheinnehållskontroll inte utfördes.
* TCP_CLIENT_REFRESH_MISS: Den här statusen rapporteras när en HTTP-klient, till exempel en webbläsare, tvingar en kant POP att hämta en ny version av en inaktuell tillgång från ursprungsservern. Som standard förhindrar servrarna en HTTP-klient från att tvinga kantservrarna att hämta en ny version av tillgången från ursprungsservern.
* TCP_PARTIAL_HIT: Den här statusen rapporteras när en byteintervallbegäran resulterar i en träff för en delvis cachelagrad tillgång. Det begärda byteintervallet visas omedelbart från POP till klienten.
* UNCACHEABLE: Den här statusen rapporteras `Cache-Control` `Expires` när en tillgångs och rubriker anger att den inte ska cachelagras på en POP eller av HTTP-klienten. Dessa typer av begäranden visas från ursprungsservern.

## <a name="cache-hit-ratio"></a>Träffförhållande för cache
Den här rapporten anger procentandelen cachelagrade begäranden som serverades direkt från cacheminnet.

Rapporten innehåller följande uppgifter:

* Det begärda innehållet cachelagrades på popen närmast beställaren.
* Begäran delgavs direkt från kanten av vårt nätverk.
* Begäran krävde inte förlängning med ursprungsservern.

Rapporten innehåller inte:

* Begäranden som nekas på grund av filtreringsalternativ för land/region.
* Begäranden om tillgångar vars rubriker anger att de inte ska cachelagras. Till exempel `Cache-Control: private` `Cache-Control: no-cache`förhindrar `Pragma: no-cache` eller rubriker att en tillgång cachelagras.
* Begäran om byteintervall för delvis cachelagrat innehåll.

Formeln är: (TCP_ HIT/(TCP_ HIT+TCP_MISS)*100

![Rapport över träffkvot för cache](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>IPV4/IPV6-data som överförts
Den här rapporten visar trafikanvändningsdistributionen i IPV4 vs IPV6.

![IPV4/IPV6-data som överförts](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Överväganden
Rapporter kan bara genereras under de senaste 18 månaderna.

