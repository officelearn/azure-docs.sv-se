---
title: 'Tillämpningshantering: Bästa praxis och rekommendationer | Microsoft-dokument'
description: Lär dig metodtips och rekommendationer för att hantera program i Azure Active Directory. Läs mer om hur du använder automatisk etablering och publicering av lokala appar med Programproxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6534efb6fcd07ee3b9f3979cabf2feb77496a8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085296"
---
# <a name="application-management-best-practices"></a>Metodtips för programhantering
Den här artikeln innehåller rekommendationer och metodtips för att hantera program i Azure Active Directory (Azure AD), med automatisk etablering och publicering av lokala appar med Programproxy.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Molnapp och rekommendationer för enkel inloggning
| Rekommendation | Kommentarer |
| --- | --- |
| Kontrollera azure AD-programgalleriet för appar  | Azure AD har ett galleri som innehåller tusentals förintegrerade program som är aktiverade med Enterprise single sign-on (SSO). Instruktioner för appspecifika inställningar finns i [självstudierna för SaaS-appen](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).  | 
| Använd federerade SAML-baserade SSO  | När ett program stöder det använder du Federerade, SAML-baserade SSO med Azure AD i stället för lösenordsbaserad SSO och ADFS.  | 
| Använda SHA-256 för certifikatsignering  | Azure AD använder SHA-256-algoritmen som standard för att signera SAML-svaret. Använd SHA-256 om inte programmet kräver SHA-1 (se [Alternativ för certifikatsignering](certificate-signing-options.md) och [inloggningsproblem för program](application-sign-in-problem-application-error.md).)  | 
| Kräv användartilldelning  | Som standard kan användarna komma åt dina företagsprogram utan att tilldelas dem. Men om programmet exponerar roller, eller om du vill att programmet ska visas på en användares åtkomstpanel, kräver användartilldelning. (Se [utvecklarvägledning för integrering av program](developer-guidance-for-integrating-applications.md).)  | 
| Distribuera åtkomstpanelen Mina appar till användarna | [Åtkomstpanelen](end-user-experiences.md) `https://myapps.microsoft.com` på är en webbaserad portal som ger användarna en enda ingångspunkt för sina tilldelade molnbaserade program. När ytterligare funktioner som grupphantering och återställning av lösenord med självbetjäning läggs till kan användarna hitta dem i åtkomstpanelen. Se [Planera en distribution av åtkomstpanelen](access-panel-deployment-plan.md).
| Använda grupptilldelning  | Om du ingår i prenumerationen tilldelar du grupper till ett program så att du kan delegera löpande åtkomsthantering till gruppägaren. (Se [utvecklarvägledning för integrering av program](developer-guidance-for-integrating-applications.md).)   | 
| Upprätta en process för hantering av certifikat | Den maximala livslängden för ett signeringscertifikat är tre år. Om du vill förhindra eller minimera avbrott på grund av att ett certifikat löper ut använder du roller och e-postdistributionslistor för att säkerställa att certifikatrelaterade ändringsmeddelanden övervakas noggrant. |

## <a name="provisioning-recommendations"></a>Etableringsrekommendationer
| Rekommendation | Kommentarer |
| --- | --- |
| Använda självstudier för att konfigurera etablering med molnappar | Läs [självstudiekursen för SaaS-appen finns](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) stegvis vägledning om hur du konfigurerar etablering för den galleriapp som du vill lägga till. |
| Använda etableringsloggar (förhandsgranskning) för att övervaka status | [Etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) ger information om alla åtgärder som utförs av etableringstjänsten, inklusive status för enskilda användare. |
| Tilldela en distributionsgrupp till e-postmeddelandet för etableringsmeddelande | Om du vill öka synligheten för kritiska aviseringar som skickas av etableringstjänsten tilldelar du en distributionsgrupp till inställningen E-postmeddelanden. |


## <a name="application-proxy-recommendations"></a>Proxyrekommendationer för program
| Rekommendation | Kommentarer |
| --- | --- |
| Använda programproxy för fjärråtkomst till interna resurser | Application Proxy rekommenderas för att ge fjärranvändare tillgång till interna resurser, som ersätter behovet av en VPN eller omvänd proxy. Den är inte avsedd för åtkomst till resurser från företagsnätverket eftersom det kan lägga till svarstid.
| Använda anpassade domäner | Konfigurera anpassade domäner för dina program (se [Konfigurera anpassade domäner)](application-proxy-configure-custom-domain.md)så att webbadresser för användare och mellan program fungerar antingen inom eller utanför nätverket. Du kan också styra ditt varumärke och anpassa webbadresserna.  När du använder anpassade domännamn planerar du att skaffa ett offentligt certifikat från en betrodd certifikatutfärdaren som inte kommer från Microsoft. Azure Application Proxy stöder standard, ([jokertecken](application-proxy-wildcard.md)) eller SAN-baserade certifikat. (Se [Programproxyplanering](application-proxy-deployment-plan.md).) |
| Synkronisera användare innan programproxy distribueras | Innan du distribuerar programproxy synkroniserar du användaridentiteter från en lokal katalog eller skapar dem direkt i Azure AD. Identitetssynkronisering gör att Azure AD kan förberätta användare innan de får åtkomst till appproxy-publicerade program. Den innehåller också nödvändig användaridentifierare för att utföra enkel inloggning (SSO). (Se [Programproxyplanering](application-proxy-deployment-plan.md).) |
| Följ våra tips för hög tillgänglighet och belastningsutjämning | Mer information om hur trafiken flödar mellan användare, Application Proxy-kopplingar och backend-appservrar och för att få tips om hur du optimerar prestanda och belastningsutjämning finns i [Hög tillgänglighet och belastningsutjämning av application proxy-kopplingar och program](application-proxy-high-availability-load-balancing.md). |
| Använda flera kopplingar | Använd två eller flera Application Proxy-kopplingar för större återhämtning, tillgänglighet och skala (se [Application Proxy-kopplingar](application-proxy-connectors.md)). Skapa kopplingsgrupper och se till att varje kopplingsgrupp har minst två kopplingar (tre kopplingar är optimala). |
| Hitta anslutningsservrar nära programservrar och se till att de finns i samma domän | För att optimera prestanda, fysiskt hitta anslutningsservern nära programservrarna (se [Nätverkstopologi överväganden](application-proxy-network-topology.md)). Anslutningsservern och webbprogramservrarna bör också tillhöra samma Active Directory-domän, eller så bör de sträcka sig över betrodda domäner. Den här konfigurationen krävs för SSO med integrerad Windows-autentisering (IWA) och Kerberos Constrained Delegation (KCD). Om servrarna finns i olika domäner måste du använda resursbaserad delegering för SSO (se [KCD för enkel inloggning med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)). |
| Aktivera automatiska uppdateringar för kopplingar | Aktivera automatiska uppdateringar för dina kontakter för de senaste funktionerna och buggfixarna. Microsoft ger direkt stöd för den senaste anslutningsversionen och en version tidigare. (Se [versionshistorik för application proxy-versionen](application-proxy-release-version-history.md).) |
| Kringgå din lokala proxy | För enklare underhåll konfigurerar du anslutningen så att den kringgår din lokala proxy så att den direkt ansluter till Azure-tjänsterna. (Se [Application Proxy-kopplingar och proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Använda Azure AD-programproxy över webbprogramproxy | Använd Azure AD Application Proxy för de flesta lokala scenarier. Proxy för webbprogram är bara att föredra i scenarier som kräver en proxyserver för AD FS och där du inte kan använda anpassade domäner i Azure Active Directory. (Se [Migrering av programproxy](application-proxy-migration.md).) |
