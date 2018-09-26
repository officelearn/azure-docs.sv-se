---
title: Låsa utgående trafik i Azure App Service Environment
description: Beskriver hur du integrerar med Azure-Brandvägg för att skydda utgående trafik
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: ccompy
ms.openlocfilehash: bcd5cb15c003ea146263a6e71a280f7da42ba830
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47168025"
---
# <a name="app-service-environment-certificates-overview"></a>Översikt över App Service Environment-certifikat 

App Service Environment (ASE) har ett antal externa beroenden som den behöver åtkomst till för att fungera korrekt. ASE bor i kundens Azure-nätverk (VNet). Kunder måste tillåta den ASE-beroende trafik, vilket är ett problem för kunder som vill låsa alla utgående trafiken från sina virtuella nätverk.

Det finns ett antal inkommande beroenden som har en ase-miljö. Inkommande hanteringstrafik kan inte skickas via en brandväggsenhet. Källadresser för den här trafiken är kända och publiceras i den [hanteringsadresser för App Service Environment](https://docs.microsoft.com/azure/app-service/environment/management-addresses) dokumentet. Du kan skapa regler för Nätverkssäkerhetsgruppen med den informationen för att skydda inkommande trafik.

De utgående ASE-beroendena definieras nästan helt och hållet med FQDN: er som inte har statiska adresser bakom dem. Bristen på statiska adresser innebär att Nätverkssäkerhetsgrupper (NSG) inte kan användas för att låsa den utgående trafiken från en ase-miljö. Adresserna ändras tillräckligt ofta att det går inte att ställa in regler baserat på aktuell upplösning och använda den för att skapa NSG: er. 

Lösning för att skydda utgående adresser ligger i att en brandväggsenhet som kan styra utgående trafik baserat på domännamn. Azure Networking-teamet har placera en ny nätverksenhet i förhandsversion som kallas Azure-brandvägg. Azure-brandväggen kan begränsa utgående HTTP och HTTPS-trafik baserat på DNS-namnet på målet.  

## <a name="configuring-azure-firewall-with-your-ase"></a>Konfigurera Brandvägg för Azure med din ASE 

Stegen för att låsa utgående trafik från din ASE med Brandvägg för Azure är:

1. Skapa en Azure-brandvägg i det virtuella nätverket där din ASE är eller blir. [Azure brandväggen documenation](https://docs.microsoft.com/azure/firewall/)
2. Välj App Service Environment FQDN-tagg i Användargränssnittet för Azure-brandväggen
3. Skapa en routningstabell med hanteringsadresserna från [hanteringsadresser för App Service Environment]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) med ett nexthop för Internet. Tabellen routningsposterna krävs för att undvika problem med asymmetrisk routning. 
4. Lägg till vägar för IP-adress-beroenden som anges nedan i beroenden för IP-adress med ett nexthop för Internet. 
5. Lägg till en väg i ditt routningstabellen för 0.0.0.0/0 med nästa hopp som din nätverksinstallation för Brandvägg för Azure
6. Skapa tjänstslutpunkter för ditt ASE-undernät till Azure SQL och Azure Storage
7. Tilldela routningstabellen som du skapade till ditt ASE-undernät  

## <a name="application-traffic"></a>Programtrafik 

Stegen ovan kan din ASE att fungera utan problem. Du måste fortfarande konfigurera saker att tillgodose behoven för ditt program. Det finns två problem för program i en ASE som har konfigurerats med Azure-brandvägg.  

- Programberoenden FQDN måste läggas till i Azure-brandväggen eller routningstabellen
- Vägar som måste skapas för adresser som trafiken kommer från att undvika problem med asymmetrisk Routning

Om ditt program har beroenden, måste läggas till din Azure-brandvägg. Skapa regler för att tillåta HTTP/HTTPS-trafik och nätverk regler för allt annat. 

Om du vet det adressintervall som din programtrafik begäran kommer från, kan du lägga till att till i routningstabellen som har tilldelats till ditt ASE-undernät. Om adressintervallet är stora eller Ospecificerad, kan du använda en nätverksenhet som Application Gateway för att ge dig en adress för att lägga till i din routningstabellen. Mer information om hur du konfigurerar en Programgateway med din ILB ASE finns [integrera din ILB ASE med en Application Gateway](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)


## <a name="dependencies"></a>Beroenden

Azure App Service har ett antal externa beroenden. De kan kategoriskt delas upp i flera huvudområden:

- Tjänstens slutpunkt kan tjänster konfigureras Tjänsteslutpunkter med om du vill låsa utgående nätverkstrafik.
- IP-adresslutpunkter täcks inte med ett domännamn. Detta kan vara ett problem för brandväggsenheter som räknar alla HTTPS-trafik till använda domännamn. IP-adresslutpunkter ska läggas till i routningstabellen som har angetts i ASE-undernät.
- FQDN HTTP/HTTPS-slutpunkter kan placeras i enheten för brandväggen.
- Jokertecken HTTP/HTTPS-slutpunkterna är beroenden som kan variera beroende på din ASE baserat på ett antal kvalificerare. 
- Linux-beroenden är endast ett problem om du distribuerar Linux-appar i din ASE. Om du inte distribuerar Linux-appar i din ASE, behöver inte dessa adresser som ska läggas till i brandväggen. 


#### <a name="service-endpoint-capable-dependencies"></a>Tjänsteslutpunkt kan beroenden 

| Slutpunkt |
|----------|
| Azure SQL |
| Azure Storage |
| Azure KeyVault |


#### <a name="ip-address-dependencies"></a>IP-adress beroenden 

| Slutpunkt |
|----------|
| 40.77.24.27:443 |
| 13.82.184.151:443 |
| 13.68.109.212:443 |
| 13.90.249.229:443 |
| 13.91.102.27:443 |
| 104.45.230.69:443 |
| 168.62.226.198:12000 |


#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-beroenden 

| Slutpunkt |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.Windows.com:443 |
|login.Windows.NET:443 |
|login.microsoftonline.com:443 |
|Client.wns.Windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|OCSP.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|CRL.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.Thawte.com:443 |
|crl3.digicert.com:80 |
|OCSP.digicert.com:80 |
|csc3 – 2009-2.crl.verisign.com:80 |
|CRL.VeriSign.com:80 |
|OCSP.VeriSign.com:80 |
|azperfcounters1.BLOB.Core.Windows .net: 443 |
|azurewatsonanalysis prod.core.windows.net:443 |
|Global.Metrics.nsatc.NET:80   |
|AZ-prod.metrics.nsatc.net:443 |
|antares.Metrics.nsatc.NET:443 |
|azglobal black.azglobal.metrics.nsatc.net:443 |
|azglobal red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.BLOB.Core.Windows.NET:443 |
|clientconfig.Passport.NET:443 |
|Packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|Management.Core.Windows.NET:443 |
|Management.Core.Windows.NET:80 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge .net: 443 |
|verifiering v2.sls.microsoft.com:443 |
|flighting.CP.WD.microsoft.com:443 |
|DMD.metaservices.microsoft.com:80 |
|Admin.Core.Windows.NET:443 |
|azureprofileruploads.BLOB.Core.Windows.NET:443 |
|azureprofileruploads2.BLOB.Core.Windows .net: 443 |
|azureprofileruploads3.BLOB.Core.Windows .net: 443 |
|azureprofileruploads4.BLOB.Core.Windows .net: 443 |
|azureprofileruploads5.BLOB.Core.Windows .net: 443 |

#### <a name="wildcard-httphttps-dependencies"></a>Jokertecken HTTP/HTTPS-beroenden 

| Slutpunkt |
|----------|
|gr-Prod -\*. cloudapp.net:443 |
| \*. management.azure.com:443 |
| \*. update.microsoft.com:443 |
| \*. windowsupdate.microsoft.com:443 |
|grmdsprod\*mini\*. servicebus.windows.net:443 |
|grmdsprod\*lini\*. servicebus.windows.net:443 |
|grsecprod\*mini\*. servicebus.windows.net:443 |
|grsecprod\*lini\*. servicebus.windows.net:443 |
|graudprod\*mini\*. servicebus.windows.net:443 |
|graudprod\*lini\*. servicebus.windows.net:443 |

#### <a name="linux-dependencies"></a>Linux-beroenden 

| Slutpunkt |
|----------|
|wawsinfraprodbay063.BLOB.Core.Windows .net: 443 |
|registret 1.docker.io:443 |
|auth.docker.IO:443 |
|Production.cloudflare.docker.com:443 |
|Download.docker.com:443 |
|US.Archive.Ubuntu.com:80 |
|Download.Mono project.com:80 |
|Packages.treasuredata.com:80|
|Security.Ubuntu.com:80 |

