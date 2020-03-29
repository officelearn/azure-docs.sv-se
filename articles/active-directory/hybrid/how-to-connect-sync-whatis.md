---
title: 'Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering | Microsoft-dokument'
description: Förklarar hur Azure AD Connect-synkronisering fungerar och hur du anpassar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3b87f40d75d4045155e7dd953dc76ffd9de2b34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60348747"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering
Azure Active Directory Connect-synkroniseringstjänster (Azure AD Connect-synkronisering) är en huvudkomponent i Azure AD Connect. Det tar hand om alla åtgärder som är relaterade till att synkronisera identitetsdata mellan din lokala miljö och Azure AD. Azure AD Connect-synkronisering är efterföljaren till DirSync, Azure AD Sync och Forefront Identity Manager med Azure Active Directory Connector konfigurerad.

Det här avsnittet är hemmet för **Azure AD Connect-synkronisering** (kallas även **synkroniseringsmotor)** och innehåller länkar till alla andra ämnen som är relaterade till det. Länkar till Azure AD Connect finns i [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

Synkroniseringstjänsten består av två komponenter, den lokala **Azure AD Connect-synkroniseringskomponenten** och tjänstsidan i Azure AD som kallas **Azure AD Connect-synkroniseringstjänst**.

## <a name="azure-ad-connect-sync-topics"></a>Synkroniseringsämnen för Azure AD Connect
| Hjälpavsnitt | Vad det täcker och när man ska läsa |
| --- | --- |
| **Grunderna i Azure AD Connect-synkronisering** | |
| [Förstå arkitekturen](concept-azure-ad-connect-sync-architecture.md) |För dig som är ny på synkroniseringsmotorn och vill lära dig mer om arkitekturen och de termer som används. |
| [Tekniska koncept](how-to-connect-sync-technical-concepts.md) |En kort version av arkitekturämnet och förklarar kortfattat de termer som används. |
| [Topologier för Azure AD Connect](plan-connect-topologies.md) |Beskriver de olika topologier och scenarier som synkroniseringsmotorn stöder. |
| **Anpassad konfiguration** | |
| [Köra installationsguiden igen](how-to-connect-installation-wizard.md) |I artikeln beskrivs vilka alternativ du har när du kör installationsguiden för Azure AD Connect igen. |
| [Förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md) |Beskriver konfigurationsmodellen som kallas deklarativ etablering. |
| [Förstå uttryck för deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |Beskriver syntaxen för uttrycksspråket som används vid deklarativ etablering. |
| [Förstå standardkonfigurationen](concept-azure-ad-connect-sync-default-configuration.md) |Beskriver out-of-box-reglerna och standardkonfigurationen. Beskriver också hur reglerna fungerar tillsammans för att färdiga scenarier ska fungera. |
| [Förstå användare och kontakter](concept-azure-ad-connect-sync-user-and-contacts.md) |Fortsätter med föregående ämne och beskriver hur konfigurationen för användare och kontakter fungerar tillsammans, särskilt i en flerskogsmiljö. |
| [Så här gör du en ändring av standardkonfigurationen](how-to-connect-sync-change-the-configuration.md) |Lär dig hur du gör en gemensam konfigurationsändring för attributflöden. |
| [Metodtips för att ändra standardkonfigurationen](how-to-connect-sync-best-practices-changing-default-configuration.md) |Stödbegränsningar och för att göra ändringar i den färdiga konfigurationen. |
| [Konfigurera filtrering](how-to-connect-sync-configure-filtering.md) |Beskriver de olika alternativen för hur du begränsar vilka objekt som synkroniseras till Azure AD och steg för steg hur du konfigurerar dessa alternativ. |
| **Funktioner och scenarier** | |
| [Förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md) |Beskriver funktionen *förhindra oavsiktliga borttagningar* och hur du konfigurerar den. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |Beskriver den inbyggda schemaläggaren som importerar, synkroniserar och exporterar data. |
| [Implementera synkronisering av lösenordsh hash](how-to-connect-password-hash-synchronization.md) |Beskriver hur lösenordssynkronisering fungerar, hur du implementerar och hur du använder och felsöker. |
| [Tillbakaskrivning av enheter](how-to-connect-device-writeback.md) |Beskriver hur tillbakaskrivning av enheter fungerar i Azure AD Connect. |
| [Katalogtillägg](how-to-connect-sync-feature-directory-extensions.md) |Beskriver hur du utökar Azure AD-schemat med dina egna anpassade attribut. |
| [Office 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |I artikeln beskrivs hur du placerar användarens Office 365-resurser i samma region som användaren. |
| **Synkronisera tjänst** | |
| [Tjänstfunktioner för Azure AD Connect-synkronisering](how-to-connect-syncservice-features.md) |Beskriver synkroniseringstjänstsidan och hur du ändrar synkroniseringsinställningar i Azure AD. |
| [Återhämtning för dubblettattribut](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |Beskriver hur du aktiverar och använder **userPrincipalName** och **proxyAdresserar** dubblettattributvärden återhämtning. |
| **Operationer och användargränssnitt** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |I artikeln beskrivs flikarna Synkroniseringstjänsthanteraren, inklusive [operations-](how-to-connect-sync-service-manager-ui-operations.md), [connectors,](how-to-connect-sync-service-manager-ui-connectors.md) [metaversedesigner](how-to-connect-sync-service-manager-ui-mvdesigner.md)och [metaversumsökflikar.](how-to-connect-sync-service-manager-ui-mvsearch.md) |
| [Operativa uppgifter och överväganden](how-to-connect-sync-operations.md) |Beskriver operativa problem, till exempel haveriberedskap. |
| **Hur...** | |
| [Återställa Azure AD-kontot](how-to-connect-azureadaccount.md) |Så här återställer du autentiseringsuppgifterna för tjänstkontot som används för att ansluta från Azure AD Connect-synkronisering till Azure AD. |
| **Mer information och referenser** | |
| [Portar](reference-connect-ports.md) |Visar vilka portar du behöver för att öppna mellan synkroniseringsmotorn och dina lokala kataloger och Azure AD. |
| [Attribut som synkroniseras till Azure Active Directory](reference-connect-sync-attributes-synchronized.md) |Visar en lista över alla attribut som synkroniseras mellan lokala AD och Azure AD. |
| [Referens för funktioner](reference-connect-sync-functions-reference.md) |Visar en lista över alla funktioner som är tillgängliga vid deklarativ etablering. |

## <a name="additional-resources"></a>Ytterligare resurser
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
