---
title: Felsöka försämrad status i Azure Traffic Manager
description: Felsöker Traffic Manager-profiler när den visas som försämrad status.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: rohink
ms.openlocfilehash: c398763405472c9018a5c30d34fbd3963ecb93b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938370"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Felsöka degraderat tillstånd på Azure Traffic Manager

I den här artikeln beskrivs felsÃ¶kning av en Azure Traffic Manager-profil som visar en försämrad status. Som ett första steg i felsökningen av ett försämrat tillstånd i Azure Traffic Manager är att aktivera diagnostikloggning.  Mer information finns i [Aktivera diagnostikloggar.](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-diagnostic-logs) I det här scenariot bör du tänka på att du har konfigurerat en Traffic Manager-profil som pekar på några av dina cloudapp.net värdtjänster. Om hälsotillståndet för Trafikhanteraren visar en **försämrad** status kan statusen för en eller flera slutpunkter **försämras:**

![försämrad slutpunktsstatus](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Om hälsotillståndet för Trafikhanteraren visar en **inaktiv** status kan båda slutpunkterna vara **inaktiverade:**

![Status för Inaktiv trafikhanterare](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Förstå Traffic Manager-avsökningar

* Traffic Manager anser att en slutpunkt endast är ONLINE när avsökningen tar emot ett HTTP 200-svar tillbaka från avsökningsvägen. Om du programmet returnerar någon annan HTTP-svarskod bör du lägga till [svarskoden i Antal statuskodintervall](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) för Din Traffic Manager-profil.
* Ett 30x-omdirigeringssvar behandlas som ett fel om du inte har angett detta som en giltig svarskod i [Antal statuskodintervall](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) för Traffic Manager-profilen. Traffic Manager avsöker inte omdirigeringsmålet.
* För HTTPs-avsökningar ignoreras certifikatfel.
* Det faktiska innehållet i avsökningsvägen spelar ingen roll, så länge en 200 returneras. Sondering en url till vissa statiska innehåll som "/ favicon.ico" är en vanlig teknik. Dynamiskt innehåll, som ASP-sidorna, kanske inte alltid returnerar 200, även när programmet är felfritt.
* En bästa praxis är att ställa in avsökningssökvägen till något som har tillräckligt med logik för att avgöra att platsen är upp eller ner. I föregående exempel, genom att ange sökvägen till "/favicon.ico", testar du bara att w3wp.exe svarar. Den här avsökningen kanske inte indikerar att webbprogrammet är felfritt. Ett bättre alternativ skulle vara att ställa in en sökväg till något som "/Probe.aspx" som har logik för att avgöra webbplatsens hälsa. Du kan till exempel använda prestandaräknare för cpu-användning eller mäta antalet misslyckade begäranden. Du kan också försöka komma åt databasresurser eller sessionstillstånd för att se till att webbprogrammet fungerar.
* Om alla slutpunkter i en profil försämras behandlar Traffic Manager alla slutpunkter som felfria och dirigerar trafik till alla slutpunkter. Detta säkerställer att problem med avbödermekanismen inte resulterar i ett fullständigt avbrott i tjänsten.

## <a name="troubleshooting"></a>Felsökning

Om du vill felsöka ett avsökningsfel behöver du ett verktyg som visar att HTTP-statuskoden returneras från avsöknings-URL:en. Det finns många verktyg som visar det råa HTTP-svaret.

* [Fiddler](https://www.telerik.com/fiddler)
* [Curl](https://curl.haxx.se/)
* [Wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Du kan också använda fliken Nätverk i F12-felsökningsverktygen i Internet Explorer för att visa HTTP-svaren.

I det här exemplet vill vi se svaret\/från vår avsöknings-URL: http: /watestsdp2008r2.cloudapp.net:80/Probe. Följande PowerShell-exempel illustrerar problemet.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Exempel på utdata:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Observera att vi fick ett omdirigeringssvar. Som tidigare nämnts anses alla statuskod än 200 vara ett fel. Traffic Manager ändrar slutpunktsstatusen till Offline. LÃ¶s problemet genom att kontrollera webbplatskonfigurationen för att säkerställa att rätt statuskod kan returneras frÃ¥r från avsökningssökvägen. Konfigurera om Traffic Manager-avsökningen så att den pekar på en bana som returnerar en 200.Reconfigure the Traffic Manager probe to point to a path that returns a 200.

Om avsökningen använder HTTPS-protokollet kan du behöva inaktivera certifikatkontroll för att undvika SSL/TLS-fel under testet. Följande PowerShell-satser inaktiverar certifikatverifiering för den aktuella PowerShell-sessionen:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Efterföljande moment

[Om trafikhanterare trafikroutningsmetoder](traffic-manager-routing-methods.md)

[Vad är Traffic Manager](traffic-manager-overview.md)

[Molntjänster](https://go.microsoft.com/fwlink/?LinkId=314074)

[Azure App-tjänst](https://azure.microsoft.com/documentation/services/app-service/web/)

[Åtgärder i Traffic Manager (REST-API-referens)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager Cmdlets][1]

[1]: https://docs.microsoft.com/powershell/module/az.trafficmanager
