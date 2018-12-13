---
title: Integrerade Azure frånkopplade distributionsbeslut för Azure Stack-system | Microsoft Docs
description: Kontrollera distributionen planeringsbeslut för flera noder Azure Stack Azure-ansluten distributioner.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 33512b47eff75421ce07b02f9c17ae3028152568
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276262"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Integrerade Azure-frånkopplade distribution planeringsbeslut för Azure Stack-system
När du har bestämt dig [hur du ska integrera Azure Stack i hybridmolnmiljön](azure-stack-connection-models.md), du kan sedan slutföra din Azure Stack-distributionsbeslut.

Du kan distribuera och använda Azure Stack utan en anslutning till internet. Men med en frånkopplad distribution är du begränsad till en AD FS-Identitetslagret och kapacitetsbaserad faktureringsmodell. Eftersom flera innehavare kräver användning av Azure AD, stöds inte flera innehavare för frånkopplade distributioner. 

Välj det här alternativet om du:
- Har säkerhets- eller andra begränsningar som kräver att du kan distribuera Azure Stack i en miljö som inte är ansluten till Internet.
- Vill du blockera data (inklusive användningsdata) skickas till Azure.
- Om du vill använda Azure Stack som en privat molnlösning som har distribuerats till företagets intranät och inte är intresserad av hybridscenarier.

> [!TIP]
> Ibland kan kallas den här typen av miljö även ett ”ubåt scenario”.

En frånkopplad distribution strikt innebär inte att du senare inte kan ansluta din Azure Stack-instans till Azure för hybridscenarion klient-VM. Det innebär att du inte har anslutning till Azure under distributionen eller du inte vill använda Azure Active Directory som ditt identitetsarkiv.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Funktioner som är försämrad eller inte tillgänglig i frånkopplade distributioner 
Azure Stack har utformats för att fungerar bäst när du är ansluten till Azure, så det är viktigt att Observera att det finns vissa egenskaper och funktioner som är försämrad eller delvis är tillgängligt i frånkopplat läge. 

|Funktion|Påverkan i frånkopplat läge|
|-----|-----|
|Distribution av virtuell dator med DSC-tillägg för att konfigurera virtuella datorer efter distribution|Skadade - DSC-tillägget ser ut till Internet för den senaste WMF.|
|Distribution av virtuella datorer med Docker-tillägg för att köra Docker-kommandon|Skadade – Docker kontrollerar Internet för den senaste versionen och den här kontrollen misslyckas.|
|Länkarna till dokumentation i Azure Stack Portal|Ej tillgänglig länkar, till exempel ge Feedback, hjälp, Snabbstart, etc. som använder en Internet-URL: en inte fungerar.|
|Aviseringen reparation/lösning som refererar till en online reparation guide|Ej tillgängligt – alla reparation av aviseringar länkar som använder en Internet-URL: en inte fungerar.|
|Marketplace – möjlighet att välja och lägga till Gallery-paket direkt från Azure Marketplace|Skadade – när du distribuerar Azure Stack i frånkopplat läge (utan någon Internetanslutning) kan du kan inte hämta marketplace-objekt med hjälp av Azure Stack-portalen. Men du kan använda den [marketplace syndikering verktyget](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) hämta marketplace-objekt till en dator som är ansluten till internet och överföra dem till Azure Stack-miljön.|
|Använda Azure Active Directory federation-konton för att hantera en Azure Stack-distribution|Ej tillgängligt – den här funktionen kräver anslutning till Azure. AD FS med en lokal Active Directory-instans måste användas i stället.|
|App Services|Skadade - kan WebApps kräver Internetåtkomst för uppdaterat innehåll.|
|Kommandoradsgränssnitt (CLI)|Skadade – minskat CLI funktioner när det gäller autentisering och etablering av Service Principles.|
|Visual Studio – Cloud discovery|Skadade – Cloud Discovery identifierar antingen olika moln eller fungerar inte alls.|
|Visual Studio – AD FS|Skadade – endast Visual Studio Enterprise stöder AD FS.
Telemetri|Ej tillgänglig telemetridata för Azure Stack som tillsammans med eventuella från tredje part gallery-paket som beror på telemetridata.|
|Certifikat|Är inte tillgänglig – Internetanslutning krävs för certifikat listan över Återkallade och Online Certificate Status Protocol (OSCP) i samband med HTTPS.|
|Key Vault|Skadade – är ett vanligt användningsfall för Key Vault att ha ett program läsa hemligheter vid körning. För det här måste programmet ett huvudnamn för tjänsten i katalogen. I Azure Active Directory är vanliga användare (icke-administratörer) som standard tillåts att lägga till tjänstens huvudnamn. I AD (med AD FS) är de inte. Detta placerar en överskrida i slutpunkt till slutpunkt-upplevelse eftersom en alltid måste gå igenom en directory-administratör att lägga till alla program.| 

## <a name="learn-more"></a>Läs mer
- Information om användningsfall, inköp, partner och OEM maskinvaruleverantörer finns i den [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) produktsidan.
- Information om plan och geo-tillgänglighet för Azure Stack integrerade system finns i dokumentet: [Azure Stack: En utökning av Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Mer information om Microsoft Azure Stack förpackning och priser [ladda ned .pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Nästa steg
[Datacenter nätverksintegrering](azure-stack-network.md)