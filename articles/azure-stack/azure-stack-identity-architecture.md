---
title: "Identity-arkitektur för Azure-Stack | Microsoft Docs"
description: "Läs mer om Identity-arkitektur som du kan använda med Azure-stacken."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 0f3e28f7726afab02211902b5ba2e478ae8065df
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="identity-architecture-for-azure-stack"></a>Identity-arkitektur för Azure-Stack
Innan du väljer en identitetsleverantör ska användas med Azure-stacken, Förstå viktiga skillnader mellan alternativen i Azure Active Directory (Azure AD) och Active Directory Federation Services (AD FS). 

## <a name="capabilities-and-limitations"></a>Funktioner och begränsningar 
Den identitetsleverantör som du kan begränsa dina alternativ, inklusive stöd för flera innehavare. 

  

|Funktionen eller scenario        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Ansluten till internet     |Ja       |Valfri|
|Stöd för flera innehavare     |Ja       |Nej      |
|Marketplace-syndikeringsfeed       |Ja       |Ja - kräver användning av den [offline Marketplace syndikering](azure-stack-download-azure-marketplace-item.md#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) verktyget.|
|Stöd för Active Directory Authentication Library (ADAL) |Ja |Ja|
|Stöd för verktyg som Azure-kommandoradsgränssnittet (CLI), Visual Studio (VS) och PowerShell  |Ja |Ja|
|Skapa tjänstens huvudnamn via portalen     |Ja |Nej|
|Skapa tjänstens huvudnamn med certifikat      |Ja |Ja|
|Skapa tjänstens huvudnamn med hemligheter (nycklar)    |Ja |Nej|
|Program kan använda tjänsten diagram           |Ja |Nej|
|Program kan använda identitetsleverantör för inloggning |Ja |Ja - kräver program för att federera med din lokala AD FS. |

## <a name="topologies"></a>Topologier
Följande avsnitt innehåller information om identity-topologier som du kan använda.

### <a name="azure-ad--single-tenant"></a>Azure AD-stöd för en innehavare 
Som standard använder Azure-stacken en enskild klient topologi när du installerar Azure Stack och använda Azure AD. 

En enskild klient-topologi är användbart när:
- Alla användare är en del av samma klientorganisation.
- En tjänsteleverantör värd för en Azure-Stack-instans för en organisation.  

![Azure Stack-topologi med en enskild klient-topologi med Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Med den här topologin:
- Azure-stacken registrerar alla program och tjänster med samma Azure AD directory-klient. 
- Azure-stacken autentiserar de användare och program från katalogen, inklusive token. 
- Identiteter för administratörer (molnoperatörer) och klienten användare finns i samma katalog-klienten. 
- Om du vill aktivera en användare från en annan katalog åtkomst till den här Azure Stack-miljön måste du [bjuda in användare som gäst](azure-stack-identity-overview.md#guest-users) till klientkatalogen.  

### <a name="azure-ad--multi-tenant"></a>Azure AD – flera innehavare
Molnoperatörer kan konfigurera Azure-Stack för att tillåta åtkomst till program och klienter från en eller flera organisationer. Användare åtkomst till program via användarportalen. I den här konfigurationen är på administrationsportalen för (används av operatorn moln) begränsad till användare från en katalog. 

En topologi med flera innehavare är användbart när:
- En tjänsteleverantör vill ge användare från flera organisationer åtkomst till Azure-stacken.

![Azure Stack-topologi med en topologi med flera innehavare med Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Med den här topologin:
- Åtkomst till resurser som ska vara på grundval av per organisation. 
- Användare från en organisation bör inte att bevilja åtkomst till resurser till användare utanför organisationen.  
- Identiteter för administratörer (molnoperatörer) kan finnas i en separat katalog klient än identiteter för användare. Den här separationen ger kontot isolering på nivån identity-providern. 
 
### <a name="ad-fs"></a>AD FS  
AD FS-topologin är obligatorisk när någon av följande villkor föreligger:
- Azure-stacken ansluter inte till Internet.
- Azure-stacken kan ansluta till Internet, men du väljer att använda AD FS för din identitetsleverantör.
  
![Azure Stack-topologi med AD FS](media/azure-stack-identity-architecture/adfs.png)

Med den här topologin:
- Stöd för användning i produktion, måste du integrera den inbyggda Azure Stack AD FS-instansen med en befintlig AD FS-instans som backas upp av Active Directory (AD), via ett federationsförtroende. 
- Du kan integrera Graph-tjänsten i Azure-stacken med din befintliga AD.  Du kan också använda OData baserat Graph API-tjänster som stöder API: er som är konsekventa med Azure AD Graph API.  

  Om du vill interagera med dina AD Graph API kräver autentiseringsuppgifter från din AD som har skrivskyddad behörighet Annonsen. 
  - Inbyggda AD FS är baserad på Server 2016. 
  - Din AD FS och AD måste baseras på Server 2012 eller tidigare. 
  
  Interaktioner mellan din AD och inbyggda AD FS är inte begränsat till OpenID Connect och kan använda alla ömsesidigt stödda protokoll.  
  - Användarkonton skapas och hanteras i din lokala AD.
  - Tjänstens huvudnamn och registreringar för program som hanteras i den inbyggda AD.



## <a name="next-steps"></a>Nästa steg
- [Översikt över identitet](azure-stack-identity-overview.md)   
- [Datacenter-integrering - identitet](azure-stack-integrate-identity.md)