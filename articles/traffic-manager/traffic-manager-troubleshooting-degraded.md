---
title: Felsöka degraderat status på Azure Traffic Manager
description: Så här felsöker Traffic Manager-profiler när den visas som degraderat status.
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
ms.openlocfilehash: e314bac630ce06fbcd62081cc6e9f5e85930d32b
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42058193"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Felsöka degraderat tillstånd i Azure Traffic Manager

Den här artikeln beskriver hur du felsöker en Azure Traffic Manager-profil som visar ett degraderat tillstånd. Överväg att du har konfigurerat en Traffic Manager-profil som pekar på några av dina cloudapp.net värdbaserade tjänster i det här scenariot. Om hälsotillståndet för dina Traffic Manager visar en **degraderad** status och status för en eller flera slutpunkter kan vara **degraderad**:

![status degraderad slutpunkt](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Om hälsotillståndet för dina Traffic Manager visar en **inaktiv** status båda slutpunkterna kanske att **inaktiverad**:

![Inaktiva Traffic Manager-status](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Så här fungerar Traffic Manager avsökningar

* Traffic Manager tar hänsyn till en slutpunkt för att vara ONLINE endast när avsökningen får ett 200 HTTP-svar från sökvägen för avsökning. Andra icke-200-svar är ett fel.
* En 30 gånger omdirigering misslyckas, även om den omdirigerade platsen returnerar 200.
* Certifikatfel ignoreras för HTTPs-avsökningar.
* Det faktiska innehållet i sökvägen för avsökning spelar så länge 200 returneras. Avsökning av en URL till vissa statiskt innehåll som ”/ favicon.ico” är en vanlig metod. Dynamiskt innehåll, t.ex. ASP-sidor kanske inte alltid returnerar 200, även om programmet är felfri.
* Ett bra tips är att ange sökvägen för avsökning till något som har tillräckligt med logik för att fastställa att webbplatsen är upp eller ned. I exemplet ovan, genom att ange sökvägen till ”/ favicon.ico”, är du bara testar den w3wp.exe svarar. Den här avsökningen kan inte ange att ditt webbprogram är felfri. Ett bättre alternativ är att ange en sökväg till en något som ”/ Probe.aspx” som har logik för att fastställa hälsotillståndet för platsen. Du kan till exempel använda prestandaräknare som CPU-användning eller mäta antalet misslyckade förfrågningar. Eller du kan försöka komma åt databasresurser eller sessionstillstånd för att se till att webbprogrammet fungerar.
* Om alla slutpunkter i en profil är försämrade sedan behandlar Traffic Manager alla slutpunkter som felfria och dirigerar trafik till alla slutpunkter. Det här beteendet garanteras att problem med sökning mekanism inte leder till en fullständig avbrott i tjänsten.

## <a name="troubleshooting"></a>Felsökning

Om du vill felsöka ett avsökningsfel, behöver du ett verktyg som visar HTTP-statuskoden returnerade från URL för webbavsökning. Det finns många verktyg som visar rådata HTTP-svaret.

* [Fiddler](http://www.telerik.com/fiddler)
* [CURL](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Du kan också använda fliken nätverk i F12 felsökningsverktyg i Internet Explorer för att visa HTTP-svar.

I det här exemplet vi vill se svar från våra URL för webbavsökning: http://watestsdp2008r2.cloudapp.net:80/Probe. Följande PowerShell-exempel illustrerar problemet.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Exempel på utdata:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Observera att vi har tagit emot en omdirigeringssvaret. Som nämnts tidigare, alla StatusCode än anses 200 vara fel. Traffic Manager ändras statusen för slutpunkten till Offline. Lös problemet genom att kontrollera webbplatskonfiguration för att säkerställa att rätt StatusCode kan returneras från sökvägen för avsökning. Konfigurera om Traffic Manager-avsökningen för att peka till en sökväg som returnerar 200.

Om din avsökningen använder HTTPS-protokollet, kan du behöva inaktivera certifikat kontrollerar för att undvika SSL/TLS-fel under testet. Följande PowerShell-uttryck inaktivera certifikatsverifiering för den aktuella PowerShell-sessionen:

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

[Om Traffic Manager routningsmetoder](traffic-manager-routing-methods.md)

[Vad är Traffic Manager](traffic-manager-overview.md)

[Cloud Services](http://go.microsoft.com/fwlink/?LinkId=314074)

[Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/)

[Åtgärder för Traffic Manager (REST API-referens)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager-cmdletar][1]

[1]: https://docs.microsoft.com/powershell/module/azurerm.trafficmanager
