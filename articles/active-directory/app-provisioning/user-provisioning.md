---
title: Automatiserad SaaS-appanvändaretablering i Azure AD | Microsoft-dokument
description: En introduktion till hur du kan använda Azure AD för att automatiskt etablera, avetablera och kontinuerligt uppdatera användarkonton över flera SaaS-program från tredje part.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e828fd9c2561007c332db67bfd0b20dda9b845f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454541"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Automatisera etablering av användare och avetablering till program med Azure Active Directory

I Azure Active Directory (Azure AD) avser termen **appetablering** att automatiskt skapa användaridentiteter och roller i molnet[(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)program som användarna behöver åtkomst till. Förutom att skapa användaridentiteter, automatisk etablering inkluderar underhåll och borttagning av användaridentiteter som status eller roller förändras. Vanliga scenarier är att etablera en Azure AD-användare i program som [Dropbox,](../saas-apps/dropboxforbusiness-provisioning-tutorial.md) [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)med mera.

![Översiktsdiagram för etablering](./media/user-provisioning/provisioning-overview.png)

Med den här funktionen kan du:

- **Automatisera etablering:** Skapa automatiskt nya konton i rätt system för nya personer när de går med i ditt team eller din organisation.
- **Automatisera avetablering:** Inaktivera konton automatiskt i rätt system när personer lämnar teamet eller organisationen.
- **Synkronisera data mellan system:** Se till att identiteterna i dina appar och system hålls uppdaterade baserat på ändringar i katalogen eller ditt personalsystem.
- **Avsättningsgrupper:** Etablera grupper till program som stöder dem.
- **Styr åtkomst:** Övervaka och granska vem som har etablerats i dina program.
- **Distribuera sömlöst i bruna fältscenarier:** Matcha befintliga identiteter mellan system och möjliggör enkel integrering, även när användare redan finns i målsystemet.
- **Använd omfattande anpassning:** Dra nytta av anpassningsbara attributmappningar som definierar vilka användardata som ska flöda från källsystemet till målsystemet.
- **Få aviseringar för kritiska händelser:** Etableringstjänsten ger aviseringar för kritiska händelser och möjliggör Integrering av Logganalys där du kan definiera anpassade aviseringar för att passa dina affärsbehov.

## <a name="benefits-of-automatic-provisioning"></a>Fördelar med automatisk avsättning

I takt med att antalet program som används i moderna organisationer fortsätter att öka har IT-administratörer i uppdrag att hantera åtkomst i stor skala. Standarder som Säkerhetspåståenden Markup Language (SAML) eller Open ID Connect (OIDC) gör det möjligt för administratörer att snabbt konfigurera enkel inloggning (SSO), men åtkomst kräver också att användare etableras i appen. För många administratörer innebär etablering manuellt att skapa alla användarkonton eller ladda upp CSV-filer varje vecka, men dessa processer är tidskrävande, dyra och felbenägna. Lösningar som SAML just-in-time (JIT) har antagits för att automatisera etablering, men företag behöver också en lösning för att avetablera användare när de lämnar organisationen eller inte längre kräver åtkomst till vissa appar baserat på rolländring.

Några vanliga motiv för att använda automatisk etablering är:

- Maximera effektiviteten och noggrannheten i etableringsprocesser.
- Spara på kostnader i samband med hosting och underhåll av specialutvecklade etableringslösningar och skript.
- Skydda din organisation genom att omedelbart ta bort användarnas identiteter från viktiga SaaS-appar när de lämnar organisationen.
- Importera enkelt ett stort antal användare till ett visst SaaS-program eller system.
- Har en enda uppsättning principer för att avgöra vem som är etablerad och vem som kan logga in på en app.

Azure AD-användare etablering kan hjälpa till att lösa dessa utmaningar. Om du vill veta mer om hur kunder har använt Azure AD-användaretablering kan du läsa [ASOS fallstudie](https://aka.ms/asoscasestudy). Videon nedan ger en översikt över användaretablering i Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Vilka program och system kan jag använda med Azure AD automatisk användaretablering?

Azure AD-funktioner förintegrerat stöd för många populära SaaS-appar och personalsystem och allmänt stöd för appar som implementerar specifika delar av [SCIM 2.0-standarden](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Förintegrerade program (galleri SaaS-appar)**. Du kan hitta alla program för vilka Azure AD stöder en förintegrerad etableringskopplingskoppling i [listan över programsjälvstudier för användaretablering](../saas-apps/tutorial-list.md). De förintegrerade program som anges i galleriet använder i allmänhet SCIM 2.0-baserade API:er för användarhantering för etablering. 

   ![Salesforce-logotyp](./media/user-provisioning/gallery-app-logos.png)

   Om du vill begära ett nytt program för etablering kan du [begära att ditt program integreras med vårt appgalleri](../develop/howto-app-gallery-listing.md). För en begäran om etablering av användare kräver vi att programmet har en SCIM-kompatibel slutpunkt. Vänligen begär att programleverantören följer SCIM-standarden så att vi snabbt kan gå in på appen till vår plattform.

* **Program som stöder SCIM 2.0**. Information om hur du ansluter program som implementerar SCIM 2.0-baserade API:er för användarhantering finns i [Skapa en SCIM-slutpunkt och konfigurera användaretablering](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Vad är SYSTEM för SCIM (Cross-domain Identity Management)?

För att automatisera etablering och avetablering exponerar appar egna användar- och grupp-API:er. Alla som har försökt hantera användare i mer än en app säger dock att varje app försöker utföra samma enkla åtgärder, till exempel att skapa eller uppdatera användare, lägga till användare i grupper eller avetablera användare. Ändå genomförs alla dessa enkla åtgärder bara lite annorlunda, med hjälp av olika slutpunktssökvägar, olika metoder för att ange användarinformation och ett annat schema för att representera varje element av information.

För att hantera dessa utmaningar innehåller SCIM-specifikationen ett gemensamt användarschema som hjälper användare att flytta in, ut och runt appar. SCIM håller på att bli den faktiska standarden för etablering och, när den används tillsammans med federationsstandarder som SAML eller OpenID Connect, ger administratörer en heltäckande standardbaserad lösning för åtkomsthantering.

Detaljerad vägledning om hur du utvecklar en SCIM-slutpunkt för att automatisera etablering och avetablering av användare och grupper i ett program finns i [Skapa en SCIM-slutpunkt och konfigurera användaretablering](use-scim-to-provision-users-and-groups.md). För förintegrerade program i galleriet (Slack, Azure Databricks, Snowflake, etc.) kan du hoppa över utvecklardokumentationen och använda självstudierna [här](../saas-apps/tutorial-list.md).

## <a name="manual-vs-automatic-provisioning"></a>Manuell jämfört med automatisk etablering

Program i Azure AD-galleriet stöder ett av två etableringslägen:

* **Manuell** etablering innebär att det inte finns någon automatisk Azure AD-etableringsappen för appen ännu. Användarkonton måste skapas manuellt, till exempel genom att lägga till användare direkt i appens administrativa portal eller ladda upp ett kalkylblad med användarkontoinformation. Läs dokumentationen som tillhandahålls av appen eller kontakta apputvecklaren för att ta reda på vilka mekanismer som är tillgängliga.

* **Automatisk** innebär att en Azure AD-etableringskoppling har utvecklats för det här programmet. Du bör följa den inställningshandledning som är specifik för att ställa in etablering för programmet. App självstudier finns på [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md).

I Azure AD-galleriet anges program som stöder **Provisioning** automatisk etablering av en etableringsikon. Växla till den nya förhandsgranskningsupplevelsen för galleri för att se dessa ikoner (i banderollen högst upp på **sidan Lägg till ett program**väljer du länken som säger Klicka här för att prova det nya och förbättrade **appgalleriet).**

![Etableringsikon i programgalleriet](./media/user-provisioning/browse-gallery.png)

Etableringsläget som stöds av ett program visas också på fliken **Etablering** när du har lagt till programmet i **Enterprise-apparna**.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hur ställer jag in automatisk etablering till ett program?

För förintegrerade program som anges i galleriet är steg-för-steg-vägledning tillgänglig för att konfigurera automatisk etablering. Se [listan över självstudier för integrerade galleriappar](../saas-apps/tutorial-list.md). Följande video visar hur du ställer in automatisk användaretablering för SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

För andra program som stöder SCIM 2.0 följer du stegen i artikeln [Skapa en SCIM-slutpunkt och konfigurera användaretablering](use-scim-to-provision-users-and-groups.md).


## <a name="related-articles"></a>Relaterade artiklar

- [Lista över självstudier om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)
- [Anpassa attributmappningar för användaretablering](customize-application-attributes.md)
- [Skriva uttryck för attributmappningar](../app-provisioning/functions-for-customizing-application-data.md)
- [Omfångsfilter för användaretablering](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Skapa en SCIM-slutpunkt och konfigurera användaretablering](use-scim-to-provision-users-and-groups.md)
- [Api-översikt för Azure AD-synkronisering](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
