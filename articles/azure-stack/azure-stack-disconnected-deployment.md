---
title: "Azure frånkopplade distributionsbeslut för Azure-Stack integrerat system | Microsoft Docs"
description: "Kontrollera distributionen planeringsbeslut för flera noder Azure Stack Azure-anslutna distributioner."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: e697dec0f3d104af073fd61bac81a00e182524e1
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Azure-frånkopplade distribution planeringsbeslut för Azure-stacken integrerat system
När du har valt [hur du ska integrera Azure Stack i molnmiljön hybrid](azure-stack-connection-models.md), du kan sedan slutföra din Azure-stacken distributionsbeslut.

Med den frånkopplade från Azure-distribution kan du distribuera och använda Azure-stacken utan en anslutning till Internet. Men med en frånkopplad distribution är du begränsade till en AD FS-Identitetslagret och kapacitet-baserade fakturering modellen. 

Välj det här alternativet om du:
- Ha säkerhets- eller andra begränsningar som du behöver distribuera Azure-stacken i en miljö som inte är ansluten till Internet.
- Vill du blockera data (inklusive användningsdata) skickas till Azure.
- Om du vill använda Azure-stacken som en lösning för privata moln som har distribuerats till företagets intranät och inte vill delta i hybridmoln.

> [!TIP]
> Ibland kan kallas den här typen av miljö även ett ”ubåt scenario”.

En frånkopplad distribution strikt innebär inte att du senare inte kan ansluta din Azure Stack-instans till Azure för hybridscenarion klient-VM. Det betyder att du inte har anslutning till Azure under distributionen eller du inte vill använda Azure Active Directory som din identitet store.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Funktioner som är försämrad eller inte tillgänglig i frånkopplade distributioner 
Azure-stacken utformades fungerar bäst när du är ansluten till Azure, så det är viktigt att Observera att det finns vissa funktioner och funktionalitet som är försämrad eller delvis är tillgängligt i frånkopplat läge. 

|Funktion|Effekt i frånkopplat läge|
|-----|-----|
|Distribution av Virtuella datorer med DSC-tillägg för att konfigurera distributionen för VM-post|Nedsatt - efter DSC-tillägg till Internet senaste WMF.|
|Distribution av Virtuella datorer med Docker-tillägg för att köra Docker-kommandon|Nedsatt – Docker kontrollerar Internet för den senaste versionen och den här kontrollen misslyckas.|
|Dokumentationen länkar i stacken Azure-portalen|Inte tillgänglig länkar till exempel ge Feedback, hjälp, Snabbstart, etc. som använder en URL-adress inte kommer att fungera.|
|Aviseringen reparation/minskning som refererar till en online reparation guide|Inte tillgänglig – alla avisering reparation länkar som använder en URL-adress inte kommer att fungera.|
|Marketplace-syndikeringsfeed – möjlighet att välja och lägga till Gallery-paket direkt från Azure Marketplace|Inte tillgänglig – den här funktionen kräver anslutning till Azure och ett Azure Active Directory-konto.|
|Hantera en Azure-Stack-distribution med hjälp av Azure Active Directory federation-konton|Inte tillgänglig – den här funktionen kräver anslutning till Azure. AD FS med en lokal Active Directory-instans måste användas i stället.|
|Till exempel Webbappar och SQL-Resursprovidrar|Inte tillgänglig - Resursproviders som Webbappar och SQL kräver Internetåtkomst för innehåll.|
|Kommandoradsgränssnitt (CLI)|Nedsatt – har CLI nedsatt funktionalitet när det gäller autentisering och etablering av tjänst principer.|
|Visual Studio – Cloud discovery|Nedsatt – Cloud Discovery identifierar antingen olika moln eller fungerar inte.|
|Visual Studio – AD FS|Nedsatt – endast Visual Studio Enterprise stöder AD FS.
Telemetri|Inte tillgänglig telemetridata för Azure-Stack som tillsammans med eventuella från tredje part gallery-paket som beror på telemetridata.|
|Certifikat|Inte tillgänglig – Internetanslutning krävs för certifikatet listan över Återkallade och Online Certificate Status Protocol (OSCP) i samband med HTTPS.|
|Key-Vault|Nedsatt – är vanliga användningsfall för Key Vault att ett program läsa hemligheter vid körning. För det här måste programmet ett huvudnamn för tjänsten i katalogen. Vanliga användare (icke-administratörer) är som standard tillåtet att lägga till tjänstens huvudnamn i Azure Active Directory. I AD (med AD FS) är de inte. Detta placerar en överskrida i slutpunkt till slutpunkt-upplevelse eftersom en måste alltid gå via en directory-administratören att lägga till alla program.| 

## <a name="learn-more"></a>Läs mer
- Information om användningsområden, köpa, partners och OEM maskinvaruleverantörer finns i [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) produktsidan.
- Information om plan och geo tillgänglighet för Azure-stacken integrerade system finns i faktabladet: [Azure stacken: ett tillägg för Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Läs mer om Microsoft Azure-stacken paketera och prissättning [ladda ned PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Nästa steg
[Datacenter nätverksintegration](azure-stack-network.md)