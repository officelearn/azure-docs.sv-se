---
title: Appens synlighet och kontroll med Microsoft Cloud App Security
description: Lär dig olika sätt att identifiera apprisknivåer, stoppa överträdelser och läckor i realtid och använda appanslutningar för att dra nytta av leverantörens API:er för synlighet och styrning.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 02/03/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206e1a06acddae0973d5dbc7e64212026149f217
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77069744"
---
# <a name="cloud-app-visibility-and-control"></a>Synlighet och kontroll för molnapp

För att få full nytta av molnappar och molntjänster måste ett IT-team hitta rätt balans mellan att stödja åtkomst samtidigt som du behåller kontrollen för att skydda kritiska data. Microsoft Cloud App Security ger omfattande synlighet, kontroll över dataresor och sofistikerade analyser för att identifiera och bekämpa cyberhot i alla dina Microsoft- och tredjepartsmolntjänster.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Identifiera och hantera skugg-IT i ditt nätverk

När IT-administratörer tillfrågas om hur många molnappar de tror att deras anställda använder, i genomsnitt säger de 30 eller 40, när genomsnittet i själva verket är över 1 000 separata appar som används av anställda i organisationen. Shadow IT hjälper dig att veta och identifiera vilka appar som används och vilken risknivå du har. Åttio procent av de anställda använder oordnade appar som ingen har granskat och kanske inte överensstämmer med dina säkerhets- och efterlevnadsprinciper. Och eftersom dina anställda kan komma åt dina resurser och appar utanför företagets nätverk räcker det inte längre med regler och principer i brandväggarna.

Använd Microsoft Cloud App Discovery (en Azure Active Directory Premium P1-funktion) för att ta reda på vilka appar som används, utforska risken för dessa appar, konfigurera principer för att identifiera nya riskfyllda appar och avregistrera dessa appar för att blockera dem inbyggt med hjälp av proxy- eller brandväggsapparaten.

- Upptäcka och identifiera skugg-IT
- Utvärdera och analysera
- Hantera dina appar
- Avancerad skugg-IT-identifieringsrapportering
- Kontrollera sanktionerade appar
 
### <a name="learn-more"></a>Läs mer

- [Upptäck och hantera skugg-IT i nätverket](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Identifierade appar med Cloud App Security](https://docs.microsoft.com/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Synlighet och kontroll för användarsession 

På dagens arbetsplats är det ofta inte tillräckligt att veta vad som händer i din molnmiljö i efterhand. Du vill stoppa överträdelser och läckor i realtid innan anställda avsiktligt eller oavsiktligt utsätter dina data och din organisation för risker. Tillsammans med Azure Active Directory (Azure AD) levererar Microsoft Cloud App Security dessa funktioner i en holistisk och integrerad upplevelse med Appkontroll för villkorlig åtkomst. 

Sessionskontrollen använder en omvänd proxyarkitektur och är unikt integrerad med Azure AD Conditional Access. Med Azure AD-villkorlig åtkomst kan du tillämpa åtkomstkontroller för organisationens appar baserat på vissa villkor. Villkoren definierar vem (användare eller grupp av användare) och vilka (vilka molnappar) och var (vilka platser och nätverk) en princip för villkorlig åtkomst tillämpas på. När du har bestämt villkoren kan du dirigera användare till Cloud App Security där du kan skydda data i realtid.  

Med denna kontroll kan du:  
- Hämtningar av kontrollfiler
- Övervaka B2B-scenarier  
- Kontrollera åtkomsten till filer  
- Skydda dokument vid nedladdning  
 
### <a name="learn-more"></a>Läs mer

- [Skydda appar med Sessionskontroll i Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Avancerad appsynlighet och kontroller 

Appkopplingar använder API:er för appleverantörer för att möjliggöra större synlighet och kontroll av Microsoft Cloud App Security över de appar du ansluter till. Cloud App Security utnyttjar API:erna som tillhandahålls av molnleverantören. Varje tjänst har sina egna ramverk och API-begränsningar som begränsning, API-gränser, dynamiska tidsskiftande API-fönster och andra. Product team för Cloud App Security arbetade med dessa tjänster för att optimera användningen av API:er och ge bästa prestanda. Med hänsyn till olika begränsningar tjänster införa på sina API: er, Cloud App Security motorer använder sin högsta tillåtna kapacitet. Vissa åtgärder, till exempel genomsökning av alla filer i klienten, kräver många API-anrop så att de sprids över en längre period. Räkna med att vissa principer ska köras i flera timmar eller dagar. 
 
### <a name="learn-more"></a>Läs mer  

- [Ansluta appar i Cloud App Security](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Nästa steg

- [Upptäck och hantera skugg-IT i nätverket](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Identifierade appar med Cloud App Security](https://docs.microsoft.com/cloud-app-security/discovered-apps)
- [Skydda appar med Sessionskontroll i Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Ansluta appar i Cloud App Security](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)
