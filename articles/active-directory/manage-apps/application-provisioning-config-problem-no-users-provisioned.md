---
title: Inga användare tillhandahålls till ett Azure AD Gallery-program
description: Så här felsöker du vanliga problem som visas när du inte ser användare i ett Azure AD Gallery-program som du har konfigurerat för användar etablering med Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1683f645e1ed81a8a415214fbb83ee80572be3ee
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275846"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Inga användare tillhandahålls till ett Azure AD Gallery-program
När den automatiska etableringen har kon figurer ATS för ett program (inklusive att kontrol lera att autentiseringsuppgifterna för appen som angetts för Azure AD för att ansluta till appen är giltiga), är användare och/eller grupper etablerade i appen. Etableringen bestäms av följande saker:

-   Vilka användare och grupper som har **tilldelats** till programmet. Observera att etablering av kapslade grupper eller Office 365-grupper inte stöds. Mer information om tilldelning finns i [tilldela en användare eller grupp till en företags app i Azure Active Directory](assign-user-or-group-access-portal.md).
-   Huruvida **attributmappning** är aktiverade och konfigurerade för att synkronisera giltiga attribut från Azure AD till appen. Mer information om mappning av mappar finns i [Anpassa mappningar av användar Provisioning-attribut för SaaS-program i Azure Active Directory](customize-application-attributes.md).
-   Huruvida det finns ett **omfångs filter** som filtrerar användare baserat på vissa attributvärden. Mer information om omfångs filter finns i [attribut-baserad program etablering med omfångs filter](define-conditional-rules-for-provisioning-user-accounts.md).
  
Om du upptäcker att användare inte är etablerade, se [etablerings loggarna (för hands version)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) i Azure AD. Sök efter logg poster för en speciell användare.

Du kan komma åt etablerings loggarna i Azure Portal genom att välja **Azure Active Directory** &gt; **Enterprise-appar** &gt; **etablerings loggar (för hands version)** i avsnittet **aktivitet** . Du kan söka i etablerings data baserat på användarens namn eller identifieraren i antingen käll systemet eller mål systemet. Mer information finns i [etablerings loggar (för hands version)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Med etablerings loggarna registreras alla åtgärder som utförs av etablerings tjänsten, inklusive frågor om Azure AD för tilldelade användare som är inom omfånget för etablering, och som frågar mål appen efter förekomsten av dessa användare, jämför användar objekt mellan systemet. Lägg sedan till, uppdatera eller inaktivera användar kontot i mål systemet baserat på jämförelsen.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Allmänna problemområden med etablering att överväga
Nedan visas en lista över allmänna problemområden som du kan detaljgranska i om du har en uppfattning om var du ska börja.

- [Etablerings tjänsten verkar inte starta](#provisioning-service-does-not-appear-to-start)
- [Etablerings loggar innebär att användare hoppas över och inte tillhandahålls, även om de tilldelas](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Etablerings tjänsten verkar inte starta
Om du ställer in **etablerings statusen** **på** i **Azure Active Directory &gt; Enterprise Apps &gt; \[program namn\] etablerings** avsnittet i &gt;. Men ingen annan statusinformation visas på den sidan efter efterföljande åter belastningar, det är troligt att tjänsten körs men inte har slutfört en första cykel ännu. Kontrol lera **etablerings loggarna (för hands versionen)** som beskrivs ovan för att avgöra vilka åtgärder tjänsten utför, och om det finns några fel.

>[!NOTE]
>En första cykel kan ta var som helst från 20 minuter till flera timmar, beroende på storleken på Azure AD-katalogen och antalet användare i omfånget för etablering. Efterföljande synkroniseringar efter den första cykeln är snabbare, eftersom etablerings tjänsten lagrar vattenstämplar som representerar både systemets tillstånd efter den första cykeln. Den första cykeln förbättrar prestanda för efterföljande synkroniseringar.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Etablerings loggar innebär att användare hoppas över och inte tillhandahålls trots att de har tilldelats

När en användare visas som "överhoppad" i etablerings loggarna är det viktigt att granska fliken **steg** i loggen för att fastställa orsaken. Nedan visas vanliga orsaker och lösningar:

- **Ett omfångs filter har kon figurer ATS** **som filtrerar användaren utifrån ett attributvärde**. Mer information om omfångs filter finns i [definitions](define-conditional-rules-for-provisioning-user-accounts.md)områdes filter.
- **Användaren är "inte korrekt berättigad".** Om du ser det här fel meddelandet beror det på att det finns ett problem med användar tilldelnings posten som är lagrad i Azure AD. Åtgärda problemet genom att ta bort användaren (eller gruppen) från appen och tilldela den igen. Mer information om tilldelning finns i [Tilldela användar-eller grupp åtkomst](assign-user-or-group-access-portal.md).
- **Ett nödvändigt attribut saknas eller har inte fyllts i för en användare.** Ett viktigt att tänka på när du konfigurerar etablering är att granska och konfigurera de mappningar och arbets flöden som definierar vilka användar egenskaper (eller grupp) som flödar från Azure AD till programmet. Den här konfigurationen omfattar att ange "matchande egenskap" som används för att unikt identifiera och matcha användare/grupper mellan de två systemen. Mer information om den här viktiga processen finns i [Anpassa mappningar av användar Provisioning-attribut för SaaS-program i Azure Active Directory](customize-application-attributes.md).
- **Attribut mappningar för grupper:** Etablering av grupp namn och grupp information, förutom medlemmarna, om de stöds för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera **mappningen** för grupp objekt som visas på fliken **etablering** . Om etablerings grupper har Aktiver ATS, se till att granska mappningar för attribut för att säkerställa att ett lämpligt fält används för "matchande ID". Matchnings-ID: t kan vara visnings namnet eller e-postaliaset. Gruppen och dess medlemmar är inte etablerade om matchande egenskap är tom eller inte har fyllts i för en grupp i Azure AD.

## <a name="next-steps"></a>Nästa steg

[Azure AD Connect synkronisering: förstå deklarativ etablering](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
