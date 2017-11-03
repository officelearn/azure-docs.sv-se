---
title: "Problemet konfigurering av användarförsörjning till ett program för Azure AD-galleriet | Microsoft Docs"
description: "Felsökning av vanliga problem inför när Konfigurera användaretablering till ett program som redan visas i Azure AD Application Gallery"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 44e344095352f2bc6b27e389fc8be2cdf3e368d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problemet konfigurering av användarförsörjning till ett program för Azure AD-galleriet

Konfigurera [automatisk användaretablering](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) för en app (där de stöds), kräver att specifika anvisningar följas för att förbereda program för automatisk etablering. Du kan sedan använda Azure-portalen för att konfigurera tjänsten etablering för att synkronisera användarkonton till programmet.

Du bör alltid börja med att söka efter installationen kursen specifika för att skapa etablering för ditt program. Följ dessa steg för att konfigurera både appen och Azure AD för att skapa etablering anslutningen. En lista över app självstudier finns på [lista över självstudier om hur man integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Hur man ser om etablering fungerar 

När tjänsten har konfigurerats kan kan de flesta insikter om driften av tjänsten hämtas från två platser:

-   **Granskningsloggar** – etablering loggas post alla åtgärder som utförs av etablering tjänsten, inklusive frågor till Azure AD för tilldelad användare som ingår i omfånget för etablering. Fråga appen mål förekomsten av dessa användare att jämföra användarobjekt mellan systemet. Lägg sedan till, uppdatera eller inaktivera användarkontot i målsystemet baserat på jämförelsen. Etablering granskningsloggarna kan nås i Azure-portalen i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt; granskningsloggarna** fliken. Filtrera loggarna på den **Kontoetablering** kategori bara ser de etablering händelserna för appen.

-   **Etableringsstatus –** en sammanfattning av den senaste etablering kör för en viss app visas i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt;etablering** avsnittet längst ned på skärmen under tjänstinställningarna. Det här avsnittet sammanfattar hur många användare (och/eller grupper) håller på att synkroniseras mellan de två systemen och om det finns några fel. Felinformation vara i granskningsloggarna. Observera att Etableringsstatus inte fyllas tills en första fullständig synkronisering har slutförts mellan Azure AD och appen.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Allmän problemområden etablering att överväga

Nedan visas en lista över allmänna problemområden som du kan detaljerat om du har en uppfattning om var du ska börja.

* [Etablerar tjänsten visas inte att starta](#provisioning-service-does-not-appear-to-start)
* [Det går inte att spara konfigurationen på grund av app autentiseringsuppgifter fungerar inte](#can’t-save-configuration-due-to-app-credentials-not-working)
* [Granskningsloggar anta att även om de är tilldelade användare ”hoppas över” och inte etablerats](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Etablerar tjänsten visas inte att starta

Om du ställer in den **Status för etablering** ska **på** i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt;etablering** på Azure portal. Men ingen annan statusinformation visas på sidan när efterföljande läses in på nytt. Det är troligt att tjänsten körs men har inte slutförts ännu en inledande synkronisering. Kontrollera den **granskningsloggar** ovan för att avgöra vilka åtgärder som utförs med tjänsten, och om det finns några fel.

>[!NOTE]
>En inledande synkronisering kan ta allt från 20 minuter till flera timmar, beroende på storleken på Azure AD-katalog och antalet användare i omfånget för etablering. Efterföljande synkroniseringar efter den inledande synkroniseringen att snabbare, eftersom etablering tjänsten lagrar vattenstämplar som representerar tillståndet för båda systemen efter den första synkroniseringen, förbättra prestanda för efterföljande synkroniseringar.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Det går inte att spara konfigurationen på grund av app autentiseringsuppgifter fungerar inte

Azure AD kräver giltiga autentiseringsuppgifter som gör att den kan anslutas till en Användarhantering API som tillhandahålls av appen i ordning för etablering för att fungera. Om dessa autentiseringsuppgifter fungerar inte eller om du inte vet wat som de är, granska självstudierna för att ställa in den här appen som beskrivs ovan.

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Granskningsloggar säger användare hoppas över och inte har etablerats även om de är tilldelade

När en användare visas som ”hoppas över” i granskningsloggarna, är det mycket viktigt att läsa de utökade information i loggmeddelandet för att ta reda på orsaken. Nedan visas vanliga orsaker och lösningar:

-   **En målgrupp filter har konfigurerats** **som filtrera användaren baserat på ett attributvärde**. Mer information om Omfångsfilter finns <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **Användaren har ”inte effektivt rätt”.** Om du ser det aktuella felmeddelandet beror det på att det är problem med tilldelning användarposten lagras i Azure AD. Åtgärda det här problemet, tilldela användaren (eller grupp) från appen och tilldela den igen. Mer information om tilldelning, se <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Ett obligatoriskt attribut är inte ifyllda för en användare eller saknas.** En viktig sak att tänka på när du ställer in etablering att granska och konfigurera attributmappning och arbetsflöden som definierar vilka användare (eller grupp) egenskaper flödar från Azure AD till programmet. Detta inkluderar egenskapen ”matchande” som används för att identifiera och matchar användare eller grupper mellan de två systemen. Mer information om viktiga processen finns <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

   * **Attributet mappningar för grupper:** etablering av gruppnamn och gruppinformation förutom medlemmar, om stöd för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera den **mappning** för gruppobjekt visas i den **etablering** fliken. Om etablering grupper är aktiverad bör du granska attributmappning för att säkerställa ett lämpligt fält som används för ”matchande ID”. Det kan vara Visa namn eller e-postalias), som gruppen och dess medlemmar inte etableras om matchning av egenskap är tomt eller inte är ifylld för en grupp i Azure AD.

#<a name="next-steps"></a>Nästa steg
[Automatisera användaren etablering och avetablering för SaaS-program med Azure Active Directory](active-directory-saas-app-provisioning.md)
