---
title: Azure hanteringsadresser för App Service Environment
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
ms.date: 05/29/2018
ms.author: ccompy
ms.openlocfilehash: b54d5abed3e0d29103f5a15ea7fb6dc37dba240e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967818"
---
# <a name="app-service-environment-management-addresses"></a>Hanteringsadresser för App Service Environment

App Service Environment (ASE) är en distribution av Azure App Service till ett undernät i Azure Virtual Network (VNet).  Ase: N måste kunna nås från Hanteringsplanet används av Azure App Service.  Den här trafiken för hantering av ASE inom användarstyrd-nätverket. Om den här trafiken är blockerad eller skickas fel, kommer du bli inaktiveras ASE. Mer information om ASE nätverk beroenden finns [nätverk överväganden och App Service Environment][networking]. Allmän information om ASE som du kan börja med [introduktion till App Service Environment][intro].

Det här dokumentet visar en lista över App Service-källadresser för av hanteringstrafik till ASE och har två viktiga syften.  

1. Du kan använda dessa adresser för att skapa Nätverkssäkerhetsgrupper till att låsa inkommande trafik.  
2. Du kan skapa flöden med adresserna för Tvingad tunneltrafik distributioner. Information om hur du konfigurerar din ASE ska fungera i en miljö där utgående trafik som skickas i lokala [konfigurera din ASE med Tvingad tunneltrafik][forcedtunnel]

Alla ase har en offentlig VIP vilken trafik som kommer in. Inkommande hanteringstrafik från dessa adresser kommer från till port 454 och 455 på offentlig VIP för ASE.  

## <a name="list-of-management-addresses"></a>Lista över hanteringsadresser ##

| Region | Adresser |
|--------|-----------|
| Alla offentliga regioner | 70.37.57.58, 157.55.208.185, 52.174.22.21, 13.94.149.179, 13.94.143.126, 13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141, 23.102.188.65, 191.236.154.88, 13.64.115.203, 65.52.193.203, 70.37.89.222, 52.224.105.172, 23.102.135.246 |
| Microsoft Azure Government (Fairfax eller MAG) | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="get-your-management-addresses-from-api"></a>Hämta din management-adresser från API: et ##

Du kan visa hanteringsadresserna som matchar din ASE med följande API-anrop.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

API: et returnerar ett JSON-dokument som innehåller alla inkommande adresser för din ASE. Listan över adresser innehåller hanteringsadresserna, den VIP som används av din ASE och ASE undernätsadressintervall själva.  

Att anropa API: et med den [armclient](https://github.com/projectkudu/ARMClient) använder du följande kommandon men du kan ersätta i din prenumerations-ID, resursgrupp och ASE-namn.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
