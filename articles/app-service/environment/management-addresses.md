---
title: Hanteringsadresser för App Service Environment - Azure
description: Visar en lista över hanteringsadresserna som används för att kommandot en App Service Environment
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f76dd423cb3f7fbae6cc88d064e49dc2d56f1a1c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792734"
---
# <a name="app-service-environment-management-addresses"></a>Hanteringsadresser för App Service Environment

App Service Environment (ASE) är en enskild klient-distribution i Azure App Service som körs i Azure Virtual Network (VNet).  När ASE körs i ditt virtuella nätverk, måste den fortfarande att vara tillgänglig från ett antal dedikerade IP-adresser som används av Azure App Service för att hantera tjänsten.  När det gäller en ASE inom hanteringstrafik användarstyrd-nätverket. Om den här trafiken är blockerad eller skickas fel, kommer du bli inaktiveras ASE. Mer information om ASE nätverk beroenden finns [nätverk överväganden och App Service Environment][networking]. Allmän information om ASE som du kan börja med [introduktion till App Service Environment][intro].

Alla ase har en offentlig VIP vilken trafik som kommer in. Inkommande hanteringstrafik från dessa adresser kommer från till port 454 och 455 på offentlig VIP för ASE. Det här dokumentet innehåller App Service-källadresser för av hanteringstrafik till ASE. De här adresserna finns också i IP-Service-tagg med namnet AppServiceManagement.

De adresser som anges nedan kan konfigureras i en routningstabell för att undvika asymmetrisk routning problem med hanteringstrafik. Vägar agera på trafik på IP-nivå och inte har tillgång till trafikriktningen eller att trafiken är en del av en TCP-svarsmeddelande. Om svarsadressen för en TCP-begäran skiljer sig från den adress den skickades till, har du ett problem med asymmetrisk routning. Om du vill undvika asymmetrisk routning problem med din ASE hanteringstrafik, måste du säkerställa att svar skickas tillbaka från samma adress som de skickades till. Information om hur du konfigurerar din ASE ska fungera i en miljö där utgående trafik som skickas i lokala [konfigurera din ASE med Tvingad tunneltrafik][forcedtunnel]

## <a name="list-of-management-addresses"></a>Lista över hanteringsadresser ##

| Region | Adresser |
|--------|-----------|
| Alla offentliga regioner | 13.64.115.203, 13.75.127.117, 13.94.141.115, 13.94.143.126, 13.94.149.179, 23.102.135.246, 23.102.188.65, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.124.47.188, 52.151.25.45, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 157.55.176.93, 157.55.208.185, 191.236.154.88 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Konfigurera en Nätverkssäkerhetsgrupp

Med Nätverkssäkerhetsgrupper behöver du inte bekymra dig om de enskilda adresserna eller underhålla din egen konfiguration. Det finns en IP-service-tagg med namnet AppServiceManagement som hålls uppdaterad med alla adresser. Gå till portalen för att använda den här tjänsttaggen IP i NSG, öppna Network Security grupper Användargränssnittet och välj inkommande säkerhetsregler. Om du har en befintlig regel för inkommande hanteringstrafik kan du redigera den. Om den här NSG inte skapades med din ASE, eller om det är allt nytt, välj sedan **Lägg till**. Under källa listrutan väljer **Tjänsttagg**.  Välj under källtjänsttagg, **AppServiceManagement**. Ange källa portintervall \*, som mål **alla**, målportsintervall till **454 455**, protokollet för att **TCP**, och för att **Tillåt** . Om du skapar regeln, måste du ange prioritet. 

![Skapa en Nätverkssäkerhetsgrupp med tjänsttaggen][1]

## <a name="configuring-a-route-table"></a>Konfigurera en routningstabell

Hanteringsadresserna kan placeras i en routningstabell med ett nexthop för internet för att säkerställa att alla inkommande hanteringstrafik kan gå tillbaka via samma väg. Dessa vägar behövs när du konfigurerar Tvingad tunneltrafik. Du kan använda portal, PowerShell eller Azure CLI för att skapa routningstabellen.  Kommandon för att skapa en routningstabell med hjälp av Azure CLI från en PowerShell-kommandotolk finns nedan. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    az network route-table create --name $rt --resource-group $rg --location $location
    az network route-table route create -g $rg --route-table-name $rt -n 13.64.115.203 --next-hop-type Internet --address-prefix 13.64.115.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.75.127.117 --next-hop-type Internet --address-prefix 13.75.127.117/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.141.115 --next-hop-type Internet --address-prefix 13.94.141.115/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.143.126 --next-hop-type Internet --address-prefix 13.94.143.126/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.149.179 --next-hop-type Internet --address-prefix 13.94.149.179/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.135.246 --next-hop-type Internet --address-prefix 23.102.135.246/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.188.65 --next-hop-type Internet --address-prefix 23.102.188.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.120.64 --next-hop-type Internet --address-prefix 40.83.120.64/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.121.56 --next-hop-type Internet --address-prefix 40.83.121.56/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.125.161 --next-hop-type Internet --address-prefix 40.83.125.161/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.124.47.188 --next-hop-type Internet --address-prefix 40.124.47.188/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.151.25.45 --next-hop-type Internet --address-prefix 52.151.25.45/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.152.214 --next-hop-type Internet --address-prefix 52.165.152.214/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.153.122 --next-hop-type Internet --address-prefix 52.165.153.122/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.154.193 --next-hop-type Internet --address-prefix 52.165.154.193/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.158.140 --next-hop-type Internet --address-prefix 52.165.158.140/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.174.22.21 --next-hop-type Internet --address-prefix 52.174.22.21/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.177.147 --next-hop-type Internet --address-prefix 52.178.177.147/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.184.149 --next-hop-type Internet --address-prefix 52.178.184.149/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.190.65 --next-hop-type Internet --address-prefix 52.178.190.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.195.197 --next-hop-type Internet --address-prefix 52.178.195.197/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.56.50 --next-hop-type Internet --address-prefix 52.187.56.50/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.59.251 --next-hop-type Internet --address-prefix 52.187.59.251/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.19 --next-hop-type Internet --address-prefix 52.187.63.19/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.37 --next-hop-type Internet --address-prefix 52.187.63.37/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.224.105.172 --next-hop-type Internet --address-prefix 52.224.105.172/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.225.177.153 --next-hop-type Internet --address-prefix 52.225.177.153/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.14.230 --next-hop-type Internet --address-prefix 65.52.14.230/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.172.237 --next-hop-type Internet --address-prefix 65.52.172.237/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.193.203 --next-hop-type Internet --address-prefix 65.52.193.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.57.58 --next-hop-type Internet --address-prefix 70.37.57.58/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.89.222 --next-hop-type Internet --address-prefix 70.37.89.222/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.141 --next-hop-type Internet --address-prefix 104.44.129.141/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.243 --next-hop-type Internet --address-prefix 104.44.129.243/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.255 --next-hop-type Internet --address-prefix 104.44.129.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.134.255 --next-hop-type Internet --address-prefix 104.44.134.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.208.54.11 --next-hop-type Internet --address-prefix 104.208.54.11/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.176.93 --next-hop-type Internet --address-prefix 157.55.176.93/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.208.185 --next-hop-type Internet --address-prefix 157.55.208.185/32
    az network route-table route create -g $rg --route-table-name $rt -n 191.236.154.88 --next-hop-type Internet --address-prefix 191.236.154.88/32

När din routningstabellen har skapats måste du konfigurera den på din ASE-undernät.  

## <a name="get-your-management-addresses-from-api"></a>Hämta din management-adresser från API: et ##

Du kan visa hanteringsadresserna som matchar din ASE med följande API-anrop.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

API: et returnerar ett JSON-dokument som innehåller alla inkommande adresser för din ASE. Listan över adresser innehåller hanteringsadresserna, den VIP som används av din ASE och ASE undernätsadressintervall själva.  

Att anropa API: et med den [armclient](https://github.com/projectkudu/ARMClient) använder du följande kommandon men du kan ersätta i din prenumerations-ID, resursgrupp och ASE-namn.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
