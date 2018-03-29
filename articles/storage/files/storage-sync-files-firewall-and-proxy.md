---
title: Azure filsynkronisering lokala inställningar för brandväggar och proxyservrar | Microsoft Docs
description: Azure filsynkronisering lokala nätverkskonfigurationen
services: storage
documentationcenter: ''
author: fauhse
manager: klaasl
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: fauhse
ms.openlocfilehash: 81425c6ac4e463bd4242328206bd43ce78a1105a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Azure filen proxy- och brandväggsinställningarna synkroniseringsinställningar
Azure filsynkronisering ansluter dina lokala servrar till Azure Files, aktivera synkronisering för flera platser och i molnet skiktning funktioner. Därmed kan måste en lokal server vara ansluten till internet. IT-administratör måste du bestämma bästa sökvägen för servern som ska få åtkomst till Azure-molntjänster.

Den här artikeln ger insyn i specifika krav och tillgängliga alternativ för att kunna och på ett säkert sätt ansluta servern till Azure filsynkronisering.

## <a name="overview"></a>Översikt
Azure filsynkronisering fungerar som en tjänst orchestration mellan Windows Server, Azure-filresursen och flera andra Azure-tjänster för att synkronisera data enligt beskrivningen i sync-grupp. För Azure filsynkronisering ska fungera korrekt, behöver du konfigurera dina servrar för att kommunicera med följande Azure-tjänster:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Autentiseringstjänster

> [!Note]  
> Azure filen Sync-agent på Windows Server initierar alla begäranden till molntjänster, vilket resulterar i att bara behöva ta hänsyn utgående trafik från ett perspektiv i brandväggen. <br /> Ingen Azure-tjänsten startar en anslutning till Azure filsynkronisering agenten.


## <a name="ports"></a>Portar
Azure filsynkronisering flyttar filen data och metadata endast över HTTPS och kräver port 443 till att öppna utgående.
Därför är all trafik krypterad.

## <a name="networks-and-special-connections-to-azure"></a>Nätverk och särskilda anslutningar till Azure
Azure filsynkronisering agenten har inga krav angående särskilda kanaler som [ExpressRoute](../../expressroute/expressroute-introduction.md), etc. till Azure.

Azure filsynkronisering fungerar på något sätt som tillåter in Azure automatiskt om anpassning till olika inställningar som bandbredd, svarstid som ger administratörer kontroll för att finjustera. Alla funktioner är inte tillgänglig just nu. Om du vill konfigurera specifika beteendet berätta för oss [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Azure filsynkronisering stöder för närvarande datoromfattande proxy-inställningar. Den här Proxyinställningen är transparent för Azure filsynkronisering agenten som servern hela trafiken dirigeras via den här proxyn.

App-specifik proxyinställningar är för närvarande under utveckling och kommer att stödjas i en framtida version av Azure filsynkronisering agent. Det går att konfigurera en proxyserver specifikt för Azure filsynkronisering trafik.

## <a name="firewall"></a>Brandvägg
Port 443 måste vara öppna utgående som nämns i föregående avsnitt. Baserat på principer i ditt datacenter, branch eller din region, kan ytterligare begränsa trafik via den här porten till vissa domäner vara desired eller krävs.

I följande tabell beskrivs domänerna som krävs för kommunikation:

| Tjänst | Domän | Användning |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | Alla användare-anrop (till exempel PowerShell) genomgår att/denna URL, inklusive inledande server registrering anropet. |
| **Azure Active Directory** | https://login.windows.net | Azure Resource Manager-anrop måste göras av en autentiserad användare. Ska lyckas, används URL: en för autentisering av användare. |
| **Azure Active Directory** | https://graph.windows.net/ | Som del av distributionen av Azure filsynkronisering skapas ett huvudnamn för tjänsten i prenumerationens Azure Active Directory. Den här URL: en används för den. Detta säkerhetsobjekt används för att delegera en minimal uppsättning rättigheter till tjänsten Azure filsynkronisering. Den användare som utför installationen av Azure filsynkronisering måste vara en autentiserad användare med behörighet för ägaren av prenumerationen. |
| **Azure Storage** | &ast;.core.windows.net | När servern hämtar en fil, sedan utför servern som flytt av data som är mer effektivt när kommunicerar direkt med Azure-filresursen i Lagringskontot. Servern har en SAS-nyckel som bara tillåter för åtkomst till resursen för filen. |
| **Azure File Sync** | &ast;.one.microsoft.com | Efter första server registration servern tar emot en regionala URL för tjänstinstansen Azure filsynkronisering i regionen. Servern kan använda URL: en för att kommunicera direkt och effektivt sätt med den instans som hanterar synkroniserades. |

> [!Important]
> När så att trafik till &ast;. one.microsoft.com, trafik till mer än bara synkroniseringstjänsten går från servern. Det finns många fler Microsoft-tjänster under underdomäner.

Om &ast;. one.microsoft.com är för breda, du kan begränsa serverns kommunikation genom att tillåta endast explicit regionala instanser av tjänsten Azure synkroniseras. Vilka instanser att välja beror på regionen synkroniseringstjänsten lagring som du har distribuerat och registrerat på servern. Det är den region som du vill tillåta för servern. Det kommer snart att flera URL: er för att aktivera nya funktionerna för verksamhetskontinuitet. 

| Region | Azure filsynkronisering regionala slutpunkts-URL |
|--------|---------------------------------------|
| Östra Australien | https://kailani-aue.one.microsoft.com |
| Centrala Kanada | https://kailani-cac.one.microsoft.com |
| Östra USA | https://kailani1.one.microsoft.com |
| Sydostasien | https://kailani10.one.microsoft.com |
| Storbritannien, södra | https://kailani-uks.one.microsoft.com |
| Västra Europa | https://kailani6.one.microsoft.com |
| Västra USA | https://kailani.one.microsoft.com |

> [!Important]
> Om du definierar dessa detaljerad brandväggsregler, kontrollera det här dokumentet ofta och uppdatera brandväggsreglerna för att undvika avbrott i tjänsten på grund av inaktuell eller ofullständig URL-listorna i inställningarna för brandväggen.

## <a name="summary-and-risk-limitation"></a>Sammanfattning och risker begränsning
Visar tidigare i det här dokumentet innehåller URL-adresser som Azure filsynkronisering för närvarande kommunicerar med. Brandväggar måste kunna tillåta trafik utgående till dessa domäner som svar från dem. Microsoft strävar efter att behålla den här listan uppdateras.

Konfigurera domänen att begränsa brandväggsregler kan vara ett mått för att förbättra säkerheten. Om dessa brandväggskonfigurationer används måste en Tänk på att URL: er ska läggas till och ändras med tiden. Det är därför ett lämpligt mått för att kontrollera tabellerna i det här dokumentet som en del av en process för ändringshantering från en Azure filsynkronisering agent-version till en annan i en test-distribution av den senaste agenten. Det här sättet kan du se till att brandväggen är konfigurerad för att tillåta trafik till domäner senaste agenten kräver.

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av en Azure filsynkronisering](storage-sync-files-planning.md)
- [Distribuera Azure filsynkronisering (förhandsgranskning)](storage-sync-files-deployment-guide.md)
