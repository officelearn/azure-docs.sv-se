---
title: "Inga användare allokeras till ett program för Azure AD-galleriet | Microsoft Docs"
description: "Felsökning av vanliga problem inför när du inte ser användarna som visas i en en Azure AD-galleriet program som du har konfigurerat för användaretablering med Azure AD"
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
ms.date: 05/04/2017
ms.author: asteen
ms.openlocfilehash: 30db71c0706de1dcc0d48fb72fa5d62475a14ba7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Inga användare allokeras till ett program för Azure AD-galleriet

När automatisk etablering har konfigurerats för ett program (inklusive Kontrollera att autentiseringsuppgifterna för app till Azure AD för att ansluta till appen är giltigt). Användare och/eller grupper har etablerats till appen och bestäms av följande saker:

-   Som användare och grupper har **tilldelade** till programmet. Mer information om tilldelning, se [tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

-   Huruvida **attributet mappningar** är aktiverad och konfigurerad för att synkronisera giltiga attribut från Azure AD till appen. Mer information om attributmappning finns [anpassa användaren etablering attributmappning för SaaS-program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

-   Oavsett om det finns en **målgrupp filter** finns som filtrerar användare baserat på specifika attributvärden. Mer information om Omfångsfilter finns [attributbaserad programmet etablering med Omfångsfilter](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

När sett att användarna inte tillhandahålls, finns i granskningsloggarna i Azure AD och Sök efter loggposter för en viss användare.

Etablering granskningsloggarna kan nås i Azure-portalen i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt; granskningsloggarna** fliken. Filtrera loggarna på den **Kontoetablering** kategori bara ser de etablering händelserna för appen. Du kan söka efter användare baserat på ”matchande ID” som konfigurerats för dem i attributet avbildningar. Om du har konfigurerat ”huvudnamn” eller ”e-postadress” som matchar attributet på Azure AD-sida och användaren inte etablering har värdet till exempel ”audrey@contoso.com”. Sök sedan granskningsloggarna för ”audrey@contoso.com” och granska sedan poster returnerades.

Etablering granskningsloggar post alla åtgärder som utförs av etablering tjänsten, inklusive frågor till Azure AD för tilldelade användare som ingår i omfånget för etablering, frågar appen mål förekomsten av användare, genom att jämföra användarobjekt mellan systemet. Lägg sedan till, uppdatera eller inaktivera användarkontot i målsystemet baserat på jämförelsen.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Allmänna problemområden med etablering att tänka på

Nedan visas en lista över allmänna problemområden som du kan detaljerat om du har en uppfattning om var du ska börja.

* [Etablerar tjänsten visas inte att starta](#provisioning-service-does-not-appear-to-start)
* [Granskningsloggar anta att även om de är tilldelade användare hoppas över och inte etablerats](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Etablerar tjänsten visas inte att starta

Om du ställer in den **Status för etablering** ska **på** i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt;etablering** på Azure portal. Men ingen statusinformation visas på sidan när efterföljande laddar är det troligt att tjänsten körs men har inte slutförts ännu en inledande synkronisering. Kontrollera den **granskningsloggar** ovan för att avgöra vilka åtgärder som utförs med tjänsten, och om det finns några fel.

>[!NOTE]
>En inledande synkronisering kan ta allt från 20 minuter till flera timmar, beroende på storleken på Azure AD-katalog och antalet användare i omfånget för etablering. Efterföljande synkroniseringar efter den första synkroniseringen är snabbare, eftersom etablering tjänsten lagrar vattenstämplar som representerar tillståndet för båda systemen efter den första synkroniseringen. Detta förbättrar prestanda för efterföljande synkronisering.
>
>

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Granskningsloggar säger användare hoppas över och inte har etablerats även om de är tilldelade

När en användare visas som ”hoppas över” i granskningsloggarna, är det mycket viktigt att läsa de utökade information i loggmeddelandet för att ta reda på orsaken. Nedan visas vanliga orsaker och lösningar:

-   **En målgrupp filter har konfigurerats** **som filtrera användaren baserat på ett attributvärde**. Mer information om Omfångsfilter finns <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **Användaren har ”inte effektivt rätt”.** Om du ser det aktuella felmeddelandet beror det på att det är problem med tilldelning användarposten lagras i Azure AD. Åtgärda det här problemet, tilldela användaren (eller grupp) från appen och tilldela den igen. Mer information om tilldelning, se <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Ett obligatoriskt attribut är inte ifyllda för en användare eller saknas.** En viktig sak att tänka på när du ställer in etablering att granska och konfigurera attributmappning och arbetsflöden som definierar vilka användare (eller grupp) egenskaper flödar från Azure AD till programmet. Detta inkluderar egenskapen ”matchande” som används för att identifiera och matchar användare eller grupper mellan de två systemen. Mer information om viktiga processen finns [anpassa användaren etablering attributmappning för SaaS-program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

  * **Attributet mappningar för grupper:** etablering av gruppnamn och gruppinformation förutom medlemmar, om stöd för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera den **mappning** för gruppobjekt visas i den **etablering** fliken. Om etablering grupper är aktiverad bör du granska attributmappning för att säkerställa ett lämpligt fält som används för ”matchande ID”. Det kan vara Visa namn eller e-postalias), som gruppen och dess medlemmar inte etableras om matchning av egenskap är tomt eller inte är ifylld för en grupp i Azure AD.

## <a name="next-steps"></a>Nästa steg
[Azure AD Connect-synkronisering: Förstå deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning.md)

