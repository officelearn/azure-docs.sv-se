---
title: Identitetsarkitektur för Azure Stack | Microsoft Docs
description: Läs mer om identitetsarkitektur som du kan använda med Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/07/2018
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 11/07/2018
ms.openlocfilehash: b739db654a182433bbe1f47528d1ab99f1b10c08
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242169"
---
# <a name="identity-architecture-for-azure-stack"></a>Identitetsarkitektur för Azure Stack
Innan du väljer en identitetsprovider som ska användas med Azure Stack, Förstå viktiga skillnader mellan alternativen i Azure Active Directory (Azure AD) och Active Directory Federation Services (AD FS). 

## <a name="capabilities-and-limitations"></a>Funktioner och begränsningar 
Den identitetsprovider som du väljer kan begränsa dina alternativ, inklusive stöd för flera innehavare. 

  

|Funktion eller scenario        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Ansluten till internet     |Ja       |Valfri|
|Stöd för flera innehavare     |Ja       |Nej      |
|Erbjudanden på Marketplace |Ja       |Ja. Kräver att den [offline Marketplace-syndikering](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) verktyget.|
|Stöd för Active Directory Authentication Library (ADAL) |Ja |Ja|
|Stöd för verktyg som Azure CLI, Visual Studio och PowerShell  |Ja |Ja|
|Skapa tjänstens huvudnamn via Azure portal     |Ja |Nej|
|Skapa tjänstens huvudnamn med certifikat      |Ja |Ja|
|Skapa tjänstens huvudnamn med hemligheter (nycklar)    |Ja |Nej|
|Program kan använda Graph-tjänsten           |Ja |Nej|
|Program kan använda identitetsleverantör för att logga in |Ja |Ja. Kräver program att federera med en lokal AD FS-instanser. |

## <a name="topologies"></a>Topologies
I följande avsnitt beskrivs de olika identity-topologier som du kan använda.

### <a name="azure-ad-single-tenant-topology"></a>Azure AD: enda klient-topologi 
Som standard använder Azure Stack en enda klient-topologi när du installerar Azure Stack och använda Azure AD. 

En enda klient-topologi är praktiskt när:
- Alla användare är en del av samma klient.
- En tjänstleverantör har en Azure Stack-instans för en organisation. 

![Azure Stack enda klient topologi med Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Den här topologin har följande egenskaper:
- Azure Stack registrerar alla program och tjänster med samma Azure AD directory-klient. 
- Azure Stack autentiserar användare och program från katalogen, inklusive token. 
- Identiteter för administratörer (molnoperatörer) och klientanvändare finns i samma directory-klient. 
- Om du vill aktivera en användare från en annan katalog att få åtkomst till den här Azure Stack-miljön, måste du [bjuda in användaren som gäst](azure-stack-identity-overview.md#guest-users) till klientkatalog. 

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD: topologi för flera innehavare
Molnoperatörer kan konfigurera Azure Stack för att tillåta åtkomst till program av klienter från en eller flera organisationer. Användare använder program via användarportalen. I den här konfigurationen är Admin portal (används av operatorn molnet) begränsad till användare från en enskild katalog. 

En topologi för flera innehavare är praktiskt när:
- En tjänstleverantör vill ge användare från flera organisationer åtkomst till Azure Stack.

![Azure Stack flera innehavare topologi med Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Den här topologin har följande egenskaper:
- Åtkomst till resurser måste finnas på basis av per organisation. 
- Användare från en organisation bör inte bevilja åtkomst till resurser till användare utanför organisationen. 
- Identiteter för administratörer (molnoperatörer) kan finnas i en separat katalog innehavare identiteter för användare. Den här separationen ger konto isolering på nivån identity-providern. 
 
### <a name="ad-fs"></a>AD FS  
AD FS-topologi är obligatorisk när något av följande villkor föreligger:
- Azure Stack kan inte anslutas till internet.
- Azure Stack kan ansluta till internet, men du väljer att använda AD FS för din identitetsprovider.
  
![Azure Stack-topologi som använder AD FS](media/azure-stack-identity-architecture/adfs.png)

Den här topologin har följande egenskaper:
- För att stödja användning av den här topologin i produktion, måste du integrera den inbyggda Azure Stack AD FS-instansen med en befintlig AD FS-instans som backas upp av Active Directory via ett federationsförtroende. 
- Du kan integrera Graph-tjänsten i Azure Stack med din befintliga Active Directory-instans. Du kan också använda OData-baserade Graph API-tjänst som stöder API: er som är konsekventa med Azure AD Graph API. 

  För att interagera med din Active Directory-instans, kräver Graph API autentiseringsuppgifter från din Active Directory-instans som har läsbehörighet. 
  - Den inbyggda AD FS-instansen är baserad på Windows Server 2016. 
  - Dina AD FS och Active Directory-instanser måste baseras på Windows Server 2012 eller senare. 
  
  Interaktioner är inte begränsad till OpenID Connect mellan dina Active Directory-instans och den inbyggda AD FS-instansen, och de kan använda alla protokoll som stöds. 
  - Användarkonton skapas och hanteras i din lokala Active Directory-instans.
  - Tjänstens huvudnamn och registreringar för program som hanteras i den inbyggda Active Directory-instansen.



## <a name="next-steps"></a>Nästa steg
- [Identitetsöversikt](azure-stack-identity-overview.md)   
- [Datacenter-integrering - identitet](azure-stack-integrate-identity.md)
