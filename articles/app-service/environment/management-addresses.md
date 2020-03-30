---
title: Hanteringsadresser
description: Hitta de hanteringsadresser som används för att styra en App Service-miljö. Konfigurerade dem i en vägtabell för att undvika asymmetriska routningsproblem.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 11/13/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7d7f97552e8faadee1af928a9ce4e1eea2df476e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687114"
---
# <a name="app-service-environment-management-addresses"></a>Hanteringsadresser för apptjänstmiljö

App Service Environment (ASE) är en enda klientdistribution av Azure App Service som körs i ditt Virtuella Azure-nätverk (VNet).  Medan ASE körs i ditt virtuella nätverk måste den fortfarande vara tillgänglig från ett antal dedikerade IP-adresser som används av Azure App Service för att hantera tjänsten.  När det gäller en ASE passerar hanteringstrafiken det användarstyrda nätverket. Om den här trafiken är blockerad eller felutredd avbryts ASE. Mer information om ASE-nätverksberoenden finns i [Nätverksöverväganden och App Service-miljön][networking]. För allmän information om ASE kan du börja med [Introduktion till App Service Environment][intro].

Alla ASEs har en offentlig VIP som ledningstrafik kommer in. Den inkommande hanteringstrafiken från dessa adresser kommer in från till portarna 454 och 455 på den offentliga VIP för din ASE. I det här dokumentet visas apptjänstens källadresser för hanteringstrafik till ASE. Dessa adresser finns också i IP-servicetag med namnet AppServiceManagement.

Adresserna som anges nedan kan konfigureras i en vägtabell för att undvika asymmetriska routningsproblem med hanteringstrafiken. Rutter agerar på trafik på IP-nivå och har inte en medvetenhet om trafikriktningen eller att trafiken är en del av ett TCP-svarsmeddelande. Om svarsadressen för en TCP-begäran skiljer sig från den adress som skickades till, har du ett asymmetriskt routningsproblem. Om du vill undvika asymmetriska routningsproblem med ASE-hanteringstrafiken måste du se till att svaren skickas tillbaka från samma adress som de skickades till. Mer information om hur du konfigurerar DIN ASE så att den fungerar i en miljö där utgående trafik skickas lokalt finns i [Konfigurera din ASE med påtvingad tunnel][forcedtunnel]

## <a name="list-of-management-addresses"></a>Lista över hanteringsadresser ##

| Region | Adresser |
|--------|-----------|
| Alla offentliga regioner | 13.64.115.203, 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 23.100.226.236, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.90.240.166, 40.91.126.196, 40.112.242.192, 40.119.4.111, 40.124.47.188, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.80.89, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.43.242.137, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 104.214.49.0, 157.55.176.93, 157.55.208.185, 191.233.203.64, 191.236.154.88 |
| Microsoft Azure-regering | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Konfigurera en nätverkssäkerhetsgrupp

Med Nätverkssäkerhetsgrupper behöver du inte oroa dig för de enskilda adresserna eller underhålla din egen konfiguration. Det finns ett IP-tjänstmärke med namnet AppServiceManagement som hålls uppdaterad med alla adresser. Om du vill använda det här IP-tjänstmärket i NSG går du till portalen, öppnar användargränssnittet för nätverkssäkerhetsgrupper och väljer Regler för inkommande säkerhet. Om du har en befintlig regel för inkommande hanteringstrafik redigerar du den. Om den här NSG:n inte skapades med din ASE, eller om den är helt ny, väljer du **Lägg till**. Under listrutan Källa väljer du **Service Tag**.  Välj **AppServiceManagement**under servicemärket Källa . Ange källportintervallen \*till , Mål till **Alla,** Målportintervall till **454-455**, Protokoll till **TCP**och Åtgärd för att **tillåta**. Om du skapar regeln måste du ange prioritet. 

![skapa en NSG med servicetag][1]

## <a name="configuring-a-route-table"></a>Konfigurera en rutttabell

Hanteringsadresserna kan placeras i en rutttabell med nästa hopp på Internet för att säkerställa att all inkommande hanteringstrafik kan gå tillbaka genom samma sökväg. Dessa rutter behövs vid konfigurering av forcerad tunnel. Om du vill skapa flödestabellen kan du använda portalen, PowerShell eller Azure CLI.  Kommandona för att skapa en vägtabell med Azure CLI från en PowerShell-fråga finns nedan. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    $managementAddresses = "13.64.115.203", "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "23.100.226.236", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.90.240.166", "40.91.126.196", "40.112.242.192", "40.119.4.111", "40.124.47.188", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.80.89", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.14.230", "65.52.172.237", "65.52.193.203", "70.37.57.58", "70.37.89.222", "104.43.242.137", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "104.214.49.0", "157.55.176.93", "157.55.208.185", "191.233.203.64", "191.236.154.88"

    az network route-table create --name $rt --resource-group $rg --location $location
    foreach ($ip in $managementAddresses) {
        az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
    }

När rutttabellen har skapats måste du ställa in den på ASE-undernätet.  

## <a name="get-your-management-addresses-from-api"></a>Hämta dina hanteringsadresser från API ##

Du kan lista de hanteringsadresser som matchar till din ASE med följande API-anrop.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

API:et returnerar ett JSON-dokument som innehåller alla inkommande adresser för DIN ASE. Listan över adresser innehåller hanteringsadresser, VIP som används av din ASE och ASE-undernätets adressintervall.  

För att anropa API med [armklienten](https://github.com/projectkudu/ARMClient) använder du följande kommandon men ersätter i ditt prenumerations-ID, resursgrupp och ASE-namn.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
