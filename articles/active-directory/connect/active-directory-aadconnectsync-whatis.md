---
title: 'Azure AD Connect-synkronisering: Förstå och anpassa synkronisering | Microsoft Docs'
description: Förklarar hur Azure AD Connect synkroniserar fungerar och hur du anpassar.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 247b7bb5b5dbce94c8f8339a6f06c8ae5dab75d9
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919379"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect-synkronisering: Förstå och anpassa synkronisering
Azure Active Directory Connect-synkroniseringstjänster (Azure AD Connect-synkronisering) är en huvudkomponenten av Azure AD Connect. Det hand tar om alla åtgärder som rör synkronisera identitetsdata mellan din lokala miljö och Azure AD. Azure AD Connect-synkronisering är efterföljaren av DirSync, Azure AD Sync och Forefront Identity Manager med Azure Active Directory Connector konfigurerats.

Det här avsnittet är hem för **Azure AD Connect-synkronisering** (kallas även **Synkroniseringsmotorn**) och ger länkar till alla andra avsnitt relaterade till den. Länkar till Azure AD Connect finns i [integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

Synkroniseringstjänsten består av två komponenter, lokalt **Azure AD Connect-synkronisering** komponent- och serversidan i Azure AD kallas **Azure AD Connect-synkroniseringstjänsten**.

## <a name="azure-ad-connect-sync-topics"></a>Azure AD Connect sync-ämnen
| Avsnitt | Den beskriver hur och när du ska läsa |
| --- | --- |
| **Azure AD Connect sync-grunderna** | |
| [Förstå arkitekturen](active-directory-aadconnectsync-understanding-architecture.md) |För de som är nya i Synkroniseringsmotorn och vill veta om arkitekturen och de termer som används. |
| [Tekniska begrepp](active-directory-aadconnectsync-technical-concepts.md) |En kort version av avsnittet om arkitekturen och kortfattat beskrivs de termer som används. |
| [Topologier för Azure AD Connect](active-directory-aadconnect-topologies.md) |Beskriver olika miljöer och scenarier som har stöd för Synkroniseringsmotorn. |
| **Anpassad konfiguration** | |
| [Köra installationsguiden igen](active-directory-aadconnectsync-installation-wizard.md) |Beskriver vilka alternativ som finns tillgängliga när du kör installationsguiden för Azure AD Connect igen. |
| [Förstå deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning.md) |Beskriver Konfigurationsmodell kallas deklarativ etablering. |
| [Förstå uttryck för deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |Här beskrivs syntaxen för Uttrycksspråk som används i deklarativ etablering. |
| [Förstå standardkonfigurationen](active-directory-aadconnectsync-understanding-default-configuration.md) |Beskriver out-of-box-regler och standardkonfigurationen. Beskriver också hur reglerna fungerar tillsammans för out-of-box-scenarion att fungera. |
| [Förstå användare och kontakter](active-directory-aadconnectsync-understanding-users-and-contacts.md) |Fortsätter om det föregående avsnittet och beskriver hur konfigurationen för användare och kontakter fungerar tillsammans, särskilt i en miljö med flera skogar. |
| [Hur du gör en ändring i standardkonfigurationen](active-directory-aadconnectsync-change-the-configuration.md) |Går igenom hur du gör en vanlig konfiguration ändra till attributflöden. |
| [Metodtips för att ändra standardkonfigurationen](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |Stöd för begränsningar och för att göra ändringar i out-of-box-konfigurationen. |
| [Konfigurera filtrering](active-directory-aadconnectsync-configure-filtering.md) |Beskriver de olika alternativ att begränsa vilka objekt som ska synkroniserats till Azure AD och steg för steg hur du konfigurerar dessa alternativ. |
| **Funktioner och scenarier** | |
| [Förhindra oavsiktliga borttagningar](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |Beskriver de *förhindra oavsiktliga borttagningar* funktion och hur du konfigurerar den. |
| [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) |Beskriver den inbyggda scheduler som importerar, synkronisering och export av data. |
| [Implementera synkronisering av lösenordshash](active-directory-aadconnectsync-implement-password-hash-synchronization.md) |Beskriver så här fungerar Lösenordssynkronisering, hur du implementerar och hur du använder och felsöka. |
| [Tillbakaskrivning av enhet](active-directory-aadconnect-feature-device-writeback.md) |Beskriver hur tillbakaskrivning av enheter fungerar i Azure AD Connect. |
| [Katalogtillägg](active-directory-aadconnectsync-feature-directory-extensions.md) |Beskriver hur du utökar schemat för Azure AD med din egen anpassade attribut. |
| [Office 365 PreferredDataLocation](active-directory-aadconnectsync-feature-preferreddatalocation.md) |Beskriver hur du lägger till användarens Office 365-resurser i samma region som användaren. |
| **Tjänsten för synkronisering** | |
| [Funktioner i tjänsten Azure AD Connect-synkronisering](active-directory-aadconnectsyncservice-features.md) |Beskriver tjänstsidan synkronisering och hur du ändrar synkroniseringsinställningar i Azure AD. |
| [Återhämtning av Duplicerat attribut](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) |Beskriver hur du aktiverar och använder **userPrincipalName** och **proxyAddresses** duplicerad attributåterhämtning för värden. |
| **Åtgärder och användargränssnitt** | |
| [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) |Beskriver Synchronization Service Manager-UI, inklusive [Operations](active-directory-aadconnectsync-service-manager-ui-operations.md), [Anslutningsappar](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Metaversumdesigner](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md), och [Metaversumsökning](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) flikar. |
| [Driftåtgärder och överväganden](active-directory-aadconnectsync-operations.md) |Beskriver Driftproblem, till exempel haveriberedskap. |
| **Hur...** | |
| [Återställ Azure AD-konto](active-directory-aadconnectsync-howto-azureadaccount.md) |Så här att återställa autentiseringsuppgifterna för tjänstkontot som används för att ansluta från Azure AD Connect sync till Azure AD. |
| **Mer information och referenser** | |
| [Portar](active-directory-aadconnect-ports.md) |Visar en lista över vilka portar som du behöver öppna mellan Synkroniseringsmotorn och dina lokala kataloger och Azure AD. |
| [Attribut som synkroniseras till Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) |Visar en lista över alla attribut som synkroniseras mellan lokala AD och Azure AD. |
| [Referens för funktioner](active-directory-aadconnectsync-functions-reference.md) |Visar en lista över alla funktioner som är tillgängliga i deklarativ etablering. |

## <a name="additional-resources"></a>Ytterligare resurser
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
