---
title: Inga användare tillhandahålls till ett Azure AD Gallery-program | Microsoft Docs
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
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b80539181e6614283b6170b9cd9d4db85f812a5f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879896"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Inga användare tillhandahålls till ett Azure AD Gallery-program
När den automatiska etableringen har kon figurer ATS för ett program (inklusive att kontrol lera att autentiseringsuppgifterna för appen som angetts för Azure AD för att ansluta till appen är giltiga), är användare och/eller grupper etablerade i appen. Etableringen bestäms av följande saker:

-   Vilka användare och grupper som har **tilldelats** till programmet. Observera att etablering av kapslade grupper eller Office 365-grupper inte stöds. Mer information om tilldelning finns i [tilldela en användare eller grupp till en företags app i Azure Active Directory](assign-user-or-group-access-portal.md).
-   Huruvida **attributmappning** är aktiverade och konfigurerade för att synkronisera giltiga attribut från Azure AD till appen. Mer information om mappning av mappar finns i [Anpassa mappningar av användar Provisioning-attribut för SaaS-program i Azure Active Directory](customize-application-attributes.md).
-   Huruvida det finns ett omfångs **filter** som filtrerar användare baserat på vissa attributvärden. Mer information om omfångs filter finns i [attribut-baserad program etablering med omfångs filter](define-conditional-rules-for-provisioning-user-accounts.md).
  
Om du ser att användarna inte är etablerade kan du läsa gransknings loggarna i Azure AD. Sök efter logg poster för en speciell användare.

Du kan få åtkomst till gransknings loggarna i Azure Portal på fliken  **&gt; Azure Active Directory Enterprise Apps &gt; \[program\] namn &gt; gransknings loggar** . Filtrera loggarna på **konto etablerings** kategorin för att endast se etablerings händelser för den appen. Du kan söka efter användare baserat på "matchande ID" som har kon figurer ATS för dem i mappningarna för attribut. Om du till exempel har konfigurerat "User Principal Name" eller "e-postadress" som matchande attribut på Azure AD-sidan och användaren inte har etablerings värdet "audrey@contoso.com", så Sök i gransknings loggarna efter "audrey@contoso.com" och granska posterna returneras.

I gransknings loggarna registreras alla åtgärder som utförs av etablerings tjänsten, inklusive frågor om Azure AD för tilldelade användare som är inom omfånget för etablering, och som frågar mål appen efter förekomsten av dessa användare, jämför användar objekt mellan systemet. Lägg sedan till, uppdatera eller inaktivera användar kontot i mål systemet baserat på jämförelsen.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Allmänna problemområden med etablering att överväga
Nedan visas en lista över allmänna problemområden som du kan detaljgranska i om du har en uppfattning om var du ska börja.

- [Etablerings tjänsten verkar inte starta](#provisioning-service-does-not-appear-to-start)
- [Gransknings loggar säger att användare hoppas över och inte tillhandahålls, även om de tilldelas](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Etablerings tjänsten verkar inte starta
Om du ställer in **etablerings statusen** **på** i avsnittet **Azure Active Directory &gt; företags Apps \[ &gt; \] program &gt;namns etablering** i Azure Portal . Men ingen annan statusinformation visas på den sidan efter efterföljande omladdningar, det är troligt att tjänsten körs men inte har slutfört en första synkronisering ännu. Kontrol lera de **gransknings loggar** som beskrivs ovan för att avgöra vilka åtgärder tjänsten utför och om det finns några fel.

>[!NOTE]
>En inledande synkronisering kan ta var som helst från 20 minuter till flera timmar, beroende på storleken på Azure AD-katalogen och antalet användare i omfånget för etablering. Efterföljande synkroniseringar när den första synkroniseringen är snabbare, eftersom etablerings tjänsten lagrar vattenstämplar som representerar både systemets tillstånd efter den första synkroniseringen. Den inledande synkroniseringen ger bättre prestanda för efterföljande synkroniseringar.
>


## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Gransknings loggar säger att användare hoppas över och inte tillhandahålls trots att de har tilldelats

När en användare visas som "överhoppad" i gransknings loggarna är det viktigt att läsa den utökade informationen i logg meddelandet för att fastställa orsaken. Nedan visas vanliga orsaker och lösningar:

- **Ett omfångs filter har kon figurer ATS** **det här filtrerar användaren utifrån ett attributvärde**. Mer information om omfångs filter finns i definitions områdes [filter](define-conditional-rules-for-provisioning-user-accounts.md).
- **Användaren är "inte korrekt berättigad".** Om du ser det här fel meddelandet beror det på att det finns ett problem med användar tilldelnings posten som är lagrad i Azure AD. Åtgärda problemet genom att ta bort användaren (eller gruppen) från appen och tilldela den igen. Mer information om tilldelning finns i [Tilldela användar-eller grupp åtkomst](assign-user-or-group-access-portal.md).
- **Ett nödvändigt attribut saknas eller har inte fyllts i för en användare.** Ett viktigt att tänka på när du konfigurerar etablering är att granska och konfigurera de mappningar och arbets flöden som definierar vilka användar egenskaper (eller grupp) som flödar från Azure AD till programmet. Den här konfigurationen omfattar att ange "matchande egenskap" som används för att unikt identifiera och matcha användare/grupper mellan de två systemen. Mer information om den här viktiga processen finns i [Anpassa mappningar av användar Provisioning-attribut för SaaS-program i Azure Active Directory](customize-application-attributes.md).
- **Attribut mappningar för grupper:** Etablering av grupp namn och grupp information, förutom medlemmarna, om de stöds för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera mappningen för grupp objekt som visas på fliken **etablering** . Om etablerings grupper har Aktiver ATS, se till att granska mappningar för attribut för att säkerställa att ett lämpligt fält används för "matchande ID". Matchnings-ID: t kan vara visnings namnet eller e-postaliaset. Gruppen och dess medlemmar är inte etablerade om matchande egenskap är tom eller inte har fyllts i för en grupp i Azure AD.

## <a name="next-steps"></a>Nästa steg

[Azure AD Connect-synkronisering: Förstå deklarativ etablering](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
