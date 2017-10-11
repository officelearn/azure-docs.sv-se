---
title: "Företagsroaming | Microsoft Docs"
description: "Innehåller information om Enterprise tillstånd centrala inställningar i Windows-enheter. Enterprise tillstånd centrala ger användarna en enhetlig miljö på sina Windows-enheter och minskar den tid som krävs för att konfigurera en ny enhet."
services: active-directory
keywords: "Vad är Enterprise tillstånd växling enterprise synkronisering molnet för windows"
documentationcenter: 
author: tanning
manager: femila
editor: curtand
ms.assetid: 83b3b58f-94c1-4ab0-be05-20e01f5ae3f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: b3c01f8d332d26e92dc3052681a4b2c95142d440
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="enterprise-state-roaming-overview"></a>Översikt över enterprise tillståndsväxling
Med Windows 10 [Azure Active Directory (AD Azure)](active-directory-whatis.md) användare få möjlighet att på ett säkert sätt synkronisera sina användarinställningar och inställningar för programdata till molnet. Enterprise tillstånd centrala ger användarna en enhetlig miljö på sina Windows-enheter och minskar den tid som krävs för att konfigurera en ny enhet. Enterprise tillstånd centrala fungerar liknar vanlig [synkronisera inställningar för konsumenten](http://windows.microsoft.com/en-US/windows-8/sync-settings-pcs) som introducerades i Windows 8. Dessutom erbjuder Enterprise tillstånd centrala:

* **Uppdelning av företagets och konsumentdata** – organisationer har kontroll över sina data och det finns ingen blandning av företagets data i ett moln konsumentkonto eller konsumentdata i ett moln enterprise-konto.
* **Förbättrad säkerhet** – Data krypteras automatiskt innan de lämnar användarens Windows 10-enhet med hjälp av Azure Rights Management (Azure RMS) och data förblir krypterade i vila i molnet. Allt innehåll förblir krypterade i vila i molnet, förutom namnområden som namn på inställningar och Windows-app.  
* **Bättre hantering och övervakning av** – ger kontroll och synlighet över som synkroniseras inställningarna i din organisation och på vilka enheter via Azure AD portal-integrering. 

Enterprise tillstånd växling är tillgängliga i Azure-regioner. Du hittar den uppdaterade listan med tillgängliga regioner på den [Azure-tjänster efter regioner](https://azure.microsoft.com/regions/#services) sidan under Azure Active Directory.

| Artikel | Beskrivning |
| --- | --- |
| [Aktivera Företagsroaming i Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md) |Enterprise tillstånd växling är tillgängliga för en organisation med en prenumeration för Premium Azure Active Directory (AD Azure). Mer information om hur du hämtar en Azure AD-prenumeration, finns det [Azure AD-produkten](https://azure.microsoft.com/services/active-directory) sidan. |
| [Inställningar och dataroaming vanliga frågor och svar](active-directory-windows-enterprise-state-roaming-faqs.md) |Det här avsnittet besvarar några frågor som IT-administratörer kan ha om inställningar och data appsynkronisering. |
| [Gruppen principer och MDM-inställningar för synkronisering av inställningar](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) |Windows 10 innehåller Grupprincip och hantering av mobila enheter (MDM) principinställningar om du vill begränsa synkronisera inställningar. |
| [Centrala referens för Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) |Följande är en fullständig lista över alla inställningar som kommer att flyttade och/eller säkerhetskopierade i Windows 10. |
| [Felsökning](active-directory-windows-enterprise-state-roaming-troubleshooting.md) |Det här avsnittet går igenom några enkla steg för felsökning och innehåller en lista över kända problem. |

