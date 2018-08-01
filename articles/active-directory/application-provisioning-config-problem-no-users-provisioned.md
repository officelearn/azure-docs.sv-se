---
title: Inga användare etableras till en Azure AD-galleriprogram | Microsoft Docs
description: Så här felsöker du vanliga problem kan stöta på när du inte ser användarna som visas i en Azure AD-Galleriprogram du har konfigurerat för etableringen av användare med Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: e3017612fe27ba5efbcdbf66c04d4d15641f216b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364665"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Inga användare etableras till en Azure AD-galleriprogram

När automatisk etablering har konfigurerats för ett program (inklusive verifierar att app-autentiseringsuppgifterna till Azure AD för att ansluta till appen är giltiga). Användare och/eller grupper som tilldelas appen och bestäms av följande saker:

-   Som användare och grupper har **tilldelade** till programmet. Läs mer på tilldelning [tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

-   Huruvida **attributmappningar** är aktiverade och konfigurerade för att synkronisera giltiga attribut från Azure AD till appen. Läs mer på attributmappningar [anpassa etablering attributet Användarmappningar för SaaS-program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

-   Oavsett om det finns en **Omfångsfilter** finns som filtrerar användare baserat på specifika attributvärden. Läs mer på Omfångsfilter [attributbaserade programetablering med Omfångsfilter](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

När du får användare inte har etablerats läser granskningsloggarna i Azure AD och Sök efter loggposter för en viss användare.

Etablering granskningsloggarna är tillgängliga i Azure-portalen i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt; granskningsloggarna** fliken. Filtrera loggarna på den **Kontoetablering** kategori för att bara se händelserna etablering för appen. Du kan söka efter användare baserat på ”matchande ID” som har konfigurerats för dem i attributmappningarna. Exempel: Om du har konfigurerat ”user principal name” eller ”e-postadress” som det matchande attributet på Azure AD-sida och användaren inte etablera har värdet ”audrey@contoso.com”. Sök i granskningsloggarna för ”audrey@contoso.com” och granska sedan antalet returnerade poster.

Etablering granskningsloggarna post alla åtgärder som utförs av etableringstjänsten, inklusive frågor till Azure AD för tilldelade användare som är inom omfånget för etablering, fråga målappen förekomsten av användarna, jämföra användarobjekt mellan system. Lägg sedan till, uppdatera eller inaktivera användarkontot i målsystemet baserat på en jämförelse.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Allmänna problemområden med etablering att tänka på

Nedan visas en lista över de allmänna problemområden som innehåller detaljer om du har en uppfattning om var du ska börja.

* [Etableringstjänsten visas inte starta](#provisioning-service-does-not-appear-to-start)
* [Granskningsloggar säger användare hoppas över och inte har etablerats, även om de är tilldelade](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Etableringstjänsten visas inte starta

Om du ställer in den **Etableringsstatus** vara **på** i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt;Etablering** på Azure portal. Men utan att andra statusinformation visas på sidan när efterföljande laddar är det troligt att tjänsten körs men har inte slutförts ännu en inledande synkronisering. Kontrollera den **granskningsloggar** som beskrivs ovan för att avgöra vilka åtgärder som tjänsten fungerar, och om det finns några fel.

>[!NOTE]
>En inledande synkronisering kan ta allt från 20 minuter till flera timmar beroende på storleken på Azure AD-katalog och antalet användare inom omfånget för etablering. Efterföljande synkroniseringar efter den inledande synkroniseringen är snabbare, eftersom etableringstjänsten lagrar vattenstämplar som representerar tillståndet för båda systemen efter den inledande synkroniseringen. Detta förbättrar prestanda för efterföljande synkroniseringar.
>
>

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Granskningsloggar säger användare hoppas över och inte har etablerats även om de är tilldelade

När en användare visas som ”skipped” i granskningsloggarna, är det mycket viktigt att läsa utökade informationen i loggmeddelande att fastställa orsaken. Nedan visas vanliga orsaker och lösningar:

-   **Ett Omfångsfilter har konfigurerats** **som filtrerar användaren baserat på ett attributvärde**. Läs mer på Omfångsfilter <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **Användaren har ”inte effektivt rätt”.** Om du ser det aktuella felmeddelandet, beror det på att det finns ett problem med användarposten för tilldelning lagras i Azure AD. För att åtgärda det här problemet, tilldela användaren (eller grupp) från appen och tilldela den igen. Läs mer på tilldelning <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Ett obligatoriskt attribut är inte ifyllt för en användare eller saknas.** En viktig sak att tänka på när du konfigurerar etablering är att granska och konfigurera attributmappningar och arbetsflöden som definierar vilka användare (eller grupp) egenskaper flow från Azure AD till programmet. Detta inkluderar egenskapen ”matchande” som används för att identifiera och matcha användare/grupper mellan de två systemen. Läs mer om den här viktiga processen [anpassa etablering attributet Användarmappningar för SaaS-program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

  * **Attributmappningar för grupper:** etablering av namn- och gruppinformation, förutom medlemmar, om det stöds för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera den **mappning** för grupp-objekt som visas i den **etablering** fliken. Om etablering grupper är aktiverad, bör du granska attributmappningar för att säkerställa att ett lämpligt fält som används för ”matchande ID”. Det kan vara Visa namn eller e-postalias), som gruppen och dess medlemmar inte etableras om matchning av egenskap är tom eller inte fylls i automatiskt för en grupp i Azure AD.

## <a name="next-steps"></a>Nästa steg
[Azure AD Connect-synkronisering: Förstå deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning.md)

