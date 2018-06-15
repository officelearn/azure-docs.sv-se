---
title: Felsöka försämrad status på Azure Traffic Manager
description: Felsökning av Traffic Manager-profiler när den visas som försämrad status.
services: traffic-manager
documentationcenter: ''
author: chadmath
manager: cshepard
ms.assetid: 8af0433d-e61b-4761-adcc-7bc9b8142fc6
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: genli
ms.openlocfilehash: f5b2f471b13db67411f15f32abad5afc644c04ba
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
ms.locfileid: "32777738"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Felsöka försämrad tillstånd på Azure Traffic Manager

Den här artikeln beskriver hur du felsöker en Azure Traffic Manager-profil som visas en status degraderad. Överväg att du har konfigurerat en Traffic Manager-profilen som pekar på vissa av dina cloudapp.net värdbaserade tjänster i det här scenariot. Om hälsotillståndet för din Traffic Manager visar en **degraderad** status och status för en eller flera slutpunkter kan vara **degraderad**:

![försämrad slutpunktstillstånd](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Om hälsotillståndet för din Traffic Manager visar en **inaktiv** status båda slutpunkterna kanske att **inaktiverad**:

![Inaktiva Traffic Manager-status](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Förstå Traffic Manager-avsökningar

* Traffic Manager anser att en slutpunkt som ska vara ONLINE endast när avsökningen tar emot ett 200 HTTP-svar tillbaka från avsökningen sökvägen. Andra icke-200-svaret är ett fel.
* 30 x redirect misslyckas, även om den omdirigerade platsen returnerar en 200.
* Certifikatfel ignoreras för HTTPs-avsökningar.
* Det faktiska innehållet i sökvägen för avsökningen spelar ingen roll, så länge en 200 returneras. Avsökning av en URL till vissa statiskt innehåll som ”/ favicon.ico” är en vanlig metod. Dynamiskt innehåll som ASP-sidor kan inte alltid returnerar 200, även om programmet är felfritt.
* Ett bra tips är att ange avsökningen sökvägen till något som har tillräckligt med logik för att fastställa att platsen är uppåt eller nedåt. I exemplet ovan, genom att ange sökvägen till ”/ favicon.ico”, är du bara testar den w3wp.exe svarar. Den här avsökningen kan inte ange att ditt webbprogram är felfri. Ett bättre alternativ är att ange en sökväg till en något, till exempel ”/ Probe.aspx” som har logik för att fastställa hälsotillståndet för platsen. Du kan till exempel använda prestandaräknare som CPU-användning eller mäta antalet misslyckade begäranden. Eller du kan försöka komma åt databasresurser eller sessionens tillstånd för att se till att webbprogrammet fungerar.
* Om alla slutpunkter i en profil är försämrade sedan behandlar Traffic Manager alla slutpunkter som felfria och vägar trafik till alla slutpunkter. Det här beteendet garanteras att problem med mekanismen avsöknings inte resulterar i en fullständig avbrott i tjänsten.

## <a name="troubleshooting"></a>Felsökning

Om du vill felsöka ett avsökningen fel, behöver du ett verktyg som visar HTTP-statuskoden returnerade från URL för webbavsökning. Det finns många verktyg som visar rådata HTTP-svaret.

* [Fiddler](http://www.telerik.com/fiddler)
* [CURL](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Du kan också använda fliken nätverk i F12 felsökningsverktyg i Internet Explorer för att visa HTTP-svar.

Det här exemplet som vi vill se svar från våra URL för webbavsökning: http://watestsdp2008r2.cloudapp.net:80/Probe. Följande PowerShell-exempel illustrerar problemet.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Exempel på utdata:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Observera att vi tagit emot ett svar för omdirigering. Som nämnts tidigare anger, alla StatusCode än anses 200 vara fel. Traffic Manager ändras status för endpoint till Offline. Kontrollera webbplatskonfigurationen för för att säkerställa att rätt StatusCode kan returneras från avsökningen sökvägen för att lösa problemet. Konfigurera om Traffic Manager-avsökning peka till en sökväg som returnerar en 200.

Om din avsökningen använder HTTPS-protokollet, kan du behöva inaktivera certifikat kontroll för att undvika SSL/TLS-fel under testet. Följande PowerShell-satser inaktivera certifikatvalidering av för den aktuella PowerShell-sessionen:

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

## <a name="next-steps"></a>Nästa steg

[Om Traffic Manager-trafikroutningsmetoder](traffic-manager-routing-methods.md)

[Vad är Traffic Manager](traffic-manager-overview.md)

[Cloud Services](http://go.microsoft.com/fwlink/?LinkId=314074)

[Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/)

[Åtgärder för Traffic Manager (REST API-referens)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager-Cmdlets][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
