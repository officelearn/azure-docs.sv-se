---
title: "Azure AD Connect-synkronisering: Förstå och anpassa synkronisering | Microsoft Docs"
description: "Förklarar hur Azure AD Connect synkronisera fungerar och hur du anpassar."
services: active-directory
documentationcenter: 
author: andkjell
manager: mtillman
editor: 
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.openlocfilehash: c3f5d469850cf06d21821bd2f642666dc5c48644
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect-synkronisering: Förstå och anpassa synkronisering
Azure Active Directory Connect-synkroniseringstjänster (Azure AD Connect sync) är en komponenten i Azure AD Connect. Det hand tar om alla åtgärder som är relaterade till synkronisera identitetsdata mellan din lokala miljö och Azure AD. Azure AD Connect-synkronisering är efterföljaren till DirSync och Azure AD Sync Forefront Identity Manager med Azure Active Directory-kopplingen konfigurerats.

Det här avsnittet är hem för **Azure AD Connect-synkronisering** (kallas även **Synkroniseringsmotorn**) och ger länkar till andra avsnitt relaterade till den. Länkar till Azure AD Connect finns [integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

Synkroniseringsservern består av två komponenter, lokal **Azure AD Connect-synkronisering** komponenten och tjänstsidan i Azure AD kallas **Azure AD Connect-synkroniseringstjänsten**. 

## <a name="azure-ad-connect-sync-topics"></a>Azure AD Connect sync-ämnen
| Avsnitt | Det täcker och när du ska läsa |
| --- | --- |
| **Grunderna i Azure AD Connect-synkronisering** | |
| [Förstå arkitekturen](active-directory-aadconnectsync-understanding-architecture.md) |För de som är nya i Synkroniseringsmotorn och vill veta om arkitektur och de termer som används. |
| [Tekniska begrepp](active-directory-aadconnectsync-technical-concepts.md) |En kort version av avsnittet arkitektur och en kort beskrivs de termer som används. |
| [Topologier för Azure AD Connect](active-directory-aadconnect-topologies.md) |Beskriver olika topologier och scenarier som har stöd för Synkroniseringsmotorn. |
| **Anpassad konfiguration** | |
| [Kör installationsguiden igen](active-directory-aadconnectsync-installation-wizard.md) |Beskriver vilka alternativ som finns tillgängliga när du kör installationsguiden för Azure AD Connect igen. |
| [Förstå deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning.md) |Beskriver Konfigurationsmodell kallas deklarativ etablering. |
| [Förstå uttryck för deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |Beskrivs syntaxen för uttrycket språket som används i deklarativ etablering. |
| [Förstå standardkonfigurationen](active-directory-aadconnectsync-understanding-default-configuration.md) |Beskriver out-of-box-regler och standardkonfigurationen. Beskriver också hur reglerna tillsammans för out-of-box-scenarier ska fungera. |
| [Förstå användare och kontakter](active-directory-aadconnectsync-understanding-users-and-contacts.md) |Fortsätter i föregående avsnitt och beskriver hur konfigurationen för användare och kontakter fungerar tillsammans, särskilt i en miljö med flera skogar. |
| [Hur du gör en ändring i standardkonfigurationen](active-directory-aadconnectsync-change-the-configuration.md) |Vägleder dig igenom hur du ändrar konfigurationen av vanliga till attributflöden. |
| [Metodtips för att ändra standardkonfigurationen](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |Stöd för begränsningar och för att göra ändringar i konfigurationen av out-of-box. |
| [Konfigurera filtrering](active-directory-aadconnectsync-configure-filtering.md) |Beskriver de olika alternativ att begränsa vilka objekt som ska synkroniseras till Azure AD och steg för steg hur du konfigurerar dessa alternativ. |
| **Scenarier och funktioner** | |
| [Förhindra oavsiktliga borttagningar](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |Beskriver de *förhindra oavsiktliga borttagningar* funktion och hur du konfigurerar den. |
| [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) |Beskriver den inbyggda Schemaläggaren som importerar, synkronisering och export av data. |
| [Implementera Lösenordssynkronisering](active-directory-aadconnectsync-implement-password-synchronization.md) |Beskriver så här fungerar Lösenordssynkronisering, hur du implementerar och hur du använder och felsöker. |
| [Tillbakaskrivning av enhet.](active-directory-aadconnect-feature-device-writeback.md) |Beskriver hur tillbakaskrivning av enheter fungerar i Azure AD Connect. |
| [Katalogtillägg](active-directory-aadconnectsync-feature-directory-extensions.md) |Beskriver hur du utökar schemat för Azure AD med dina egna anpassade attribut. |
| **Synkroniseringstjänsten** | |
| [Azure AD Connect sync-tjänsten-funktioner](active-directory-aadconnectsyncservice-features.md) |Beskriver tjänstsidan synkronisering och hur du ändrar synkroniseringsinställningar i Azure AD. |
| [Duplicerat attribut återhämtning](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) |Beskriver hur du aktiverar och använder **userPrincipalName** och **proxyAddresses** dubblettattribut värden återhämtning. |
| **Åtgärder och UI** | |
| [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) |Beskriver Synchronization Service Manager-UI, inklusive [Operations](active-directory-aadconnectsync-service-manager-ui-operations.md), [kopplingar](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md), och [metaversumsökningen](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) flikar. |
| [Åtgärder och överväganden](active-directory-aadconnectsync-operations.md) |Beskriver Driftproblem, till exempel katastrofåterställning. |
| **Hur...** | |
| [Återställning av Azure AD-kontot](active-directory-aadconnectsync-howto-azureadaccount.md) |Så här återställer du autentiseringsuppgifterna för tjänstkontot som används för att ansluta från Azure AD Connect-synkronisering till Azure AD. |
| **Mer information och referenser** | |
| [Portar](active-directory-aadconnect-ports.md) |Visar vilka portar som du behöver öppna mellan Synkroniseringsmotorn och dina lokala kataloger och Azure AD. |
| [Attribut som synkroniseras till Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) |Visar alla attribut som synkroniseras mellan lokala AD och Azure AD. |
| [Referens för funktioner](active-directory-aadconnectsync-functions-reference.md) |Visar en lista över alla funktioner som är tillgängliga i deklarativ etablering. |

## <a name="additional-resources"></a>Ytterligare resurser
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)

