---
title: Spärra utgående trafik
description: Lär dig hur du integrerar med Azure-brandväggen för att skydda utgående trafik från en App Service-miljö.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 03/31/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3dadb57c6358623974de1a27e1601d99b28fee32
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584322"
---
# <a name="locking-down-an-app-service-environment"></a>Låsa en apptjänstmiljö

App Service Environment (ASE) har ett antal externa beroenden som den kräver åtkomst till för att fungera korrekt. ASE finns i kunden Azure Virtual Network (VNet). Kunder måste tillåta ASE-beroendetrafik, vilket är ett problem för kunder som vill låsa alla utgående från sitt virtuella nätverk.

Det finns ett antal inkommande slutpunkter som används för att hantera en ASE. Den inkommande hanteringstrafiken kan inte skickas via en brandväggsenhet. Källadresserna för den här trafiken är kända och publiceras i dokumentet [apptjänstmiljöhanteringsadresser.](https://docs.microsoft.com/azure/app-service/environment/management-addresses) Det finns också ett servicetag med namnet AppServiceManagement som kan användas med NSG(Network Security Groups) för att skydda inkommande trafik.

ASE-utgående beroenden definieras nästan helt med FQDN: er, som inte har statiska adresser bakom sig. Bristen på statiska adresser innebär att nätverkssäkerhetsgrupper inte kan användas för att låsa den utgående trafiken från en ASE. Adresserna ändras tillräckligt ofta för att man inte kan ställa in regler baserat på den aktuella upplösningen och använda den för att skapa NSG:er. 

Lösningen för att skydda utgående adresser är att använda en brandväggsenhet som kan styra utgående trafik baserat på domännamn. Azure-brandväggen kan begränsa utgående HTTP- och HTTPS-trafik baserat på målets FQDN.  

## <a name="system-architecture"></a>Systemarkitektur

Distribuera en ASE med utgående trafik som går igenom en brandväggsenhet kräver att ändra vägar på ASE-undernätet. Rutterna fungerar på IP-nivå. Om du inte är försiktig med att definiera dina rutter kan du tvinga TCP-svarstrafik att köpa från en annan adress. När din svarsadress skiljer sig från adresstrafiken skickades till, kallas problemet asymmetrisk routning och det kommer att bryta TCP.

Det måste finnas vägar definierade så att inkommande trafik till ASE kan svara tillbaka på samma sätt som trafiken kom in. Vägar måste definieras för inkommande hanteringsbegäranden och för inkommande programbegäranden.

Trafiken till och från en ASE måste följa följande konventioner

* Trafiken till Azure SQL, Storage och Event Hub stöds inte med hjälp av en brandväggsenhet. Denna trafik måste skickas direkt till dessa tjänster. Sättet att få det att hända är att konfigurera tjänstslutpunkter för dessa tre tjänster. 
* Rutttabellregler måste definieras som skickar inkommande hanteringstrafik tillbaka från där den kom.
* Rutttabellregler måste definieras som skickar inkommande programtrafik tillbaka från där den kom. 
* All annan trafik som lämnar ASE kan skickas till brandväggsenheten med en rutttabellregel.

![ASE med Azure-brandväggens anslutningsflöde][5]

## <a name="locking-down-inbound-management-traffic"></a>Låsa inkommande ledningstrafik

Om ase-undernätet inte redan har tilldelats en NSG skapar du ett. I NSG anger du den första regeln för att tillåta trafik från Service Tag med namnet AppServiceManagement på portarna 454, 455. Regeln att tillåta åtkomst från AppServiceManagement-taggen är det enda som krävs från offentliga IPs för att hantera din ASE. Adresserna som ligger bakom det Service Tag används endast för att administrera Azure App Service. Hanteringstrafiken som flödar genom dessa anslutningar krypteras och skyddas med autentiseringscertifikat. Typisk trafik på den här kanalen innehåller saker som kundinitierade kommandon och hälsoavsökningar. 

ASEs som görs via portalen med ett nytt undernät görs med en NSG som innehåller tillåt-regeln för AppServiceManagement-taggen.  

Din ASE måste också tillåta inkommande begäranden från load balancer-taggen på port 16001. Begäranden från belastningsutjämnaren på port 16001 håller vid liv kontroller mellan belastningsutjämnaren och ASE-frontändarna. Om port 16001 är blockerad blir din ASE ohälsosam.

## <a name="configuring-azure-firewall-with-your-ase"></a>Konfigurera Azure-brandväggen med din ASE 

Stegen för att låsa utgående från din befintliga ASE med Azure-brandväggen är:

1. Aktivera tjänstslutpunkter till SQL, Storage och Event Hub i DITT ASE-undernät. Om du vill aktivera tjänstslutpunkter går du till nätverksportalen > undernät och väljer Microsoft.EventHub, Microsoft.SQL och Microsoft.Storage från listrutan Tjänstslutpunkter. När du har aktiverat tjänstslutpunkter till Azure SQL måste alla Azure SQL-beroenden som dina appar har konfigurerats med tjänstslutpunkter också. 

   ![välj tjänstslutpunkter][2]
  
1. Skapa ett undernät med namnet AzureFirewallSubnet i det virtuella nätverk där din ASE finns. Följ anvisningarna i [Azure-brandväggens dokumentation](https://docs.microsoft.com/azure/firewall/) för att skapa din Azure-brandvägg.

1. Välj Lägg till programregelsamling från Azure Firewall-användargränssnittet > regler > programregelsamling. Ange ett namn, prioritet och ange Tillåt. I avsnittet FQDN-taggar anger du ett namn, anger källadresserna till * och väljer FQDN-taggen för App Service Environment och Windows Update. 
   
   ![Lägga till programregel][1]
   
1. Välj Lägg till nätverksregelsamling > regler > nätverksregelsamlingen i Azure Firewall. Ange ett namn, prioritet och ange Tillåt. I avsnittet Regler under IP-adresser anger du ett namn, väljer en ptocol av **Alla**, anger * till käll- och måladresser och anger portarna till 123. Med den här regeln kan systemet utföra klocksynkronisering med NTP. Skapa en annan regel på samma sätt att port 12000 för att hjälpa triage alla systemproblem. 

   ![Lägga till NTP-nätverksregel][3]
   
1. Välj Lägg till nätverksregelsamling > regler > nätverksregelsamlingen i Azure Firewall. Ange ett namn, prioritet och ange Tillåt. I avsnittet Regler under Tjänsttaggar anger du ett namn, väljer ett protokoll för **Alla**, ange * till källadresser, väljer en tjänsttagg för AzureMonitor och anger portarna till 80, 443. Den här regeln gör det möjligt för systemet att förse Azure Monitor med hälso- och måttinformation.

   ![Lägga till ntp-tjänsttagnätverksregel][6]
   
1. Skapa en flödestabell med hanteringsadresser från [App Service Environment-hanteringsadresser]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) med nästa hopp på Internet. Flödestabellposter krävs för att undvika asymmetriska routningsproblem. Lägg till vägar för IP-adressberoenden som anges nedan i IP-adressberoenden med nästa hopp på Internet. Lägg till en virtuell installation väg till din vägtabell för 0.0.0.0/0 med nästa hopp som din Azure Firewall privat IP-adress. 

   ![Skapa en flödestabell][4]
   
1. Tilldela den flödestabell som du skapade till ASE-undernätet.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Distribuera din ASE bakom en brandvägg

Stegen för att distribuera din ASE bakom en brandvägg är desamma som att konfigurera din befintliga ASE med en Azure-brandvägg förutom att du behöver skapa ditt ASE-undernät och sedan följa föregående steg. Om du vill skapa ASE i ett befintligt undernät måste du använda en Resource Manager-mall enligt beskrivningen i dokumentet om [hur du skapar ASE med en Resource Manager-mall](https://docs.microsoft.com/azure/app-service/environment/create-from-template).

## <a name="application-traffic"></a>Programtrafik 

Ovanstående steg gör det möjligt för din ASE att fungera utan problem. Du måste fortfarande konfigurera saker för att tillgodose dina programbehov. Det finns två problem för program i en ASE som är konfigurerad med Azure-brandväggen.  

- Programberoenden måste läggas till i Azure-brandväggen eller vägtabellen. 
- Vägar måste skapas för programtrafiken för att undvika asymmetriska routningsproblem

Om dina program har beroenden måste de läggas till i din Azure-brandvägg. Skapa programregler för att tillåta HTTP/HTTPS-trafik och nätverksregler för allt annat. 

Om du känner till adressintervallet som din programbegäran trafik kommer från, kan du lägga till det i vägtabellen som är tilldelad till ditt ASE-undernät. Om adressintervallet är stort eller ospecificerat kan du använda en nätverksinstallation som Programgateway för att ge dig en adress att lägga till i din rutttabell. Mer information om hur du konfigurerar en programgateway med din ILB ASE finns i [Integrera din ILB ASE med en Application Gateway](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

Den här användningen av Application Gateway är bara ett exempel på hur du konfigurerar systemet. Om du följde den här sökvägen måste du lägga till en väg till ASE-undernätsrutttabellen så att svarstrafiken som skickas till Programgatewayen skulle gå dit direkt. 

## <a name="logging"></a>Loggning 

Azure-brandväggen kan skicka loggar till Azure Storage-, Event Hub- eller Azure Monitor-loggar. Om du vill integrera din app med alla mål som stöds går du till Azure Firewall-portalen > diagnostikloggar och aktiverar loggarna för önskad destination. Om du integrerar med Azure Monitor-loggar kan du se loggning för all trafik som skickas till Azure-brandväggen. Om du vill se den trafik som nekas öppnar du logganalysarbetsytaportalen > loggar och anger en fråga som 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Det är användbart att integrera din Azure-brandvägg med Azure Monitor-loggar när du först får ett program att fungera när du inte är medveten om alla programberoenden. Du kan läsa mer om Azure Monitor-loggar från [Analysera loggdata i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).
 
## <a name="dependencies"></a>Beroenden

Följande information krävs endast om du vill konfigurera en annan brandväggsinstallation än Azure-brandväggen. 

- Tjänstslutpunktskompatibla tjänster bör konfigureras med tjänstslutpunkter.
- IP-adressberoenden är för trafik som inte är HTTP/S (både TCP- och UDP-trafik)
- FQDN HTTP/HTTPS-slutpunkter kan placeras i brandväggsenheten.
- HTTP/HTTPS-slutpunkter med jokertecken är beroenden som kan variera med din ASE baserat på ett antal kvalificerare. 
- Linux-beroenden är bara ett problem om du distribuerar Linux-appar till din ASE. Om du inte distribuerar Linux-appar till din ASE behöver dessa adresser inte läggas till i brandväggen. 

#### <a name="service-endpoint-capable-dependencies"></a>Beroenden för tjänstslutpunktskompatibla 

| Slutpunkt |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>BEROENDEN FÖR IP-adress

| Slutpunkt | Information |
|----------| ----- |
| \*:123 | NTP-klockkontroll. Trafiken kontrolleras vid flera slutpunkter på port 123 |
| \*:12000 | Den här porten används för viss systemövervakning. Om det är blockerat blir vissa problem svårare att triage, men din ASE kommer att fortsätta att fungera |
| 40.77.24.27:80 | Behövs för att övervaka och varna om ASE-problem |
| 40.77.24.27:443 | Behövs för att övervaka och varna om ASE-problem |
| 13.90.249.229:80 | Behövs för att övervaka och varna om ASE-problem |
| 13.90.249.229:443 | Behövs för att övervaka och varna om ASE-problem |
| 104.45.230.69:80 | Behövs för att övervaka och varna om ASE-problem |
| 104.45.230.69:443 | Behövs för att övervaka och varna om ASE-problem |
| 13.82.184.151:80 | Behövs för att övervaka och varna om ASE-problem |
| 13.82.184.151:443 | Behövs för att övervaka och varna om ASE-problem |

Med en Azure-brandvägg får du automatiskt allt nedan konfigurerat med FQDN-taggarna. 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-beroenden 

| Slutpunkt |
|----------|
|graph.microsoft.com:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |
|ctldl.windowsupdate.com:80 |
|ctldl.windowsupdate.com:443 |

#### <a name="wildcard-httphttps-dependencies"></a>HTTP/HTTPS-beroenden för jokertecken 

| Slutpunkt |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
| \*.identity.azure.net:443 |
| \*.ctldl.windowsupdate.com:80 |
| \*.ctldl.windowsupdate.com:443 |

#### <a name="linux-dependencies"></a>Linux-beroenden 

| Slutpunkt |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |
| \*.cdn.mscr.io:443 |
|mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>Usa Gov beroenden

För ASEs i regioner i USA Gov följer du instruktionerna i den [konfigurera Azure-brandväggen med ditt ASE-avsnitt](https://docs.microsoft.com/azure/app-service/environment/firewall-integration#configuring-azure-firewall-with-your-ase) i det här dokumentet för att konfigurera en Azure-brandvägg med din ASE.

Om du vill använda en annan enhet än Azure-brandväggen i US Gov 

* Tjänstslutpunktskompatibla tjänster bör konfigureras med tjänstslutpunkter.
* FQDN HTTP/HTTPS-slutpunkter kan placeras i brandväggsenheten.
* HTTP/HTTPS-slutpunkter med jokertecken är beroenden som kan variera med din ASE baserat på ett antal kvalificerare.

Linux är inte tillgängligt i regioner i USA Gov och är därför inte listat som en valfri konfiguration.

#### <a name="service-endpoint-capable-dependencies"></a>Beroenden för tjänstslutpunktskompatibla ####

| Slutpunkt |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>BEROENDEN FÖR IP-adress

| Slutpunkt | Information |
|----------| ----- |
| \*:123 | NTP-klockkontroll. Trafiken kontrolleras vid flera slutpunkter på port 123 |
| \*:12000 | Den här porten används för viss systemövervakning. Om det är blockerat blir vissa problem svårare att triage, men din ASE kommer att fortsätta att fungera |
| 40.77.24.27:80 | Behövs för att övervaka och varna om ASE-problem |
| 40.77.24.27:443 | Behövs för att övervaka och varna om ASE-problem |
| 13.90.249.229:80 | Behövs för att övervaka och varna om ASE-problem |
| 13.90.249.229:443 | Behövs för att övervaka och varna om ASE-problem |
| 104.45.230.69:80 | Behövs för att övervaka och varna om ASE-problem |
| 104.45.230.69:443 | Behövs för att övervaka och varna om ASE-problem |
| 13.82.184.151:80 | Behövs för att övervaka och varna om ASE-problem |
| 13.82.184.151:443 | Behövs för att övervaka och varna om ASE-problem |

#### <a name="dependencies"></a>Beroenden ####

| Slutpunkt |
|----------|
| \*.ctldl.windowsupdate.com:80 |
| \*.management.usgovcloudapi.net:80 |
| \*.update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com
|ocsp.digicert.0 |
|ocsp.msocsp.co|
|ocsp.verisign.0 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*.management.usgovcloudapi.net:443 |
|\*.update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
