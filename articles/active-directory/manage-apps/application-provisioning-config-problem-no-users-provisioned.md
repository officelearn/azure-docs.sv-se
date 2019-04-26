---
title: Inga användare etableras till en Azure AD-galleriprogram | Microsoft Docs
description: Så här felsöker du vanliga problem som kan stöta på när du inte ser användarna som visas i ett Azure AD-Galleriprogram som du har konfigurerat för etableringen av användare med Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e74ad04f10865a830d27c1814be10eeff3ad59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60443223"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Inga användare etableras till en Azure AD-galleriprogram
När automatisk etablering har konfigurerats för ett program (inklusive verifierar att app-autentiseringsuppgifterna till Azure AD för att ansluta till appen är giltiga), som sedan/användare eller grupper tilldelas appen. Etablering bestäms av följande saker:

-   Som användare och grupper har **tilldelade** till programmet. Läs mer på tilldelning [tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](assign-user-or-group-access-portal.md).
-   Huruvida **attributmappningar** är aktiverade och konfigurerade för att synkronisera giltiga attribut från Azure AD till appen. Läs mer på attributmappningar [anpassa etablering attributet Användarmappningar för SaaS-program i Azure Active Directory](customize-application-attributes.md).
-   Oavsett om det finns en **Omfångsfilter** finns som filtrerar användare baserat på specifika attributvärden. Läs mer på Omfångsfilter [attributbaserade programetablering med Omfångsfilter](define-conditional-rules-for-provisioning-user-accounts.md).
  
Om du upptäcker att användare inte har etablerats, läser du granskningsloggarna i Azure AD. Sök efter poster för en viss användare.

Etablering granskningsloggarna är tillgängliga i Azure-portalen i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt; granskningsloggarna** fliken. Filtrera loggarna på den **Kontoetablering** kategori för att bara se händelserna etablering för appen. Du kan söka efter användare baserat på ”matchande ID” som har konfigurerats för dem i attributmappningarna. Exempel: Om du har konfigurerat ”user principal name” eller ”e-postadress” som det matchande attributet på Azure AD-sida och användaren inte etablera har värdet ”audrey@contoso.com”, sedan Sök i granskningsloggarna för ”audrey@contoso.com” och granska posterna returnerade.

Etablering granskningsloggarna post alla åtgärder som utförs av etableringstjänsten, inklusive frågor till Azure AD för tilldelade användare som är inom omfånget för etablering, fråga målappen förekomsten av användarna, jämföra användarobjekt mellan system. Lägg sedan till, uppdatera eller inaktivera användarkontot i målsystemet baserat på en jämförelse.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Allmänna problemområden med etablering att tänka på
Nedan visas en lista över de allmänna problemområden som innehåller detaljer om du har en uppfattning om var du ska börja.

- [Etableringstjänsten visas inte starta](#provisioning-service-does-not-appear-to-start)
- [Granskningsloggar säger användare hoppas över och inte har etablerats, även om de är tilldelade](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Etableringstjänsten visas inte starta
Om du ställer in den **Etableringsstatus** vara **på** i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt;Etablering** på Azure portal. Men utan att andra statusinformation visas på sidan när efterföljande laddar är det troligt att tjänsten körs men har inte slutförts ännu en inledande synkronisering. Kontrollera den **granskningsloggar** som beskrivs ovan för att avgöra vilka åtgärder som tjänsten fungerar, och om det finns några fel.

>[!NOTE]
>En inledande synkronisering kan ta allt från 20 minuter till flera timmar beroende på storleken på Azure AD-katalog och antalet användare inom omfånget för etablering. Efterföljande synkroniseringar efter den inledande synkroniseringen är snabbare, eftersom etableringstjänsten lagrar vattenstämplar som representerar tillståndet för båda systemen efter den inledande synkroniseringen. Den första synkroniseringen förbättrar prestandan för efterföljande synkroniseringar.
>


## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Granskningsloggar säger användare hoppas över och inte har etablerats även om de är tilldelade

När en användare visas som ”skipped” i granskningsloggarna, är det viktigt att läsa utökade informationen i loggmeddelande att fastställa orsaken. Nedan visas vanliga orsaker och lösningar:

- **Ett Omfångsfilter har konfigurerats** **som filtrerar användaren baserat på ett attributvärde**. Läs mer på Omfångsfilter [Omfångsfilter](define-conditional-rules-for-provisioning-user-accounts.md).
- **Användaren har ”inte effektivt rätt”.** Om du ser det aktuella felmeddelandet, beror det på att det finns ett problem med användarposten för tilldelning lagras i Azure AD. Ta bort användare (eller grupp) från appen för att åtgärda problemet och tilldela den igen. Läs mer på tilldelning [tilldela användare eller grupp åtkomst](assign-user-or-group-access-portal.md).
- **Ett obligatoriskt attribut är inte ifyllt för en användare eller saknas.** En viktig sak att tänka på när du konfigurerar etablering är att granska och konfigurera attributmappningar och arbetsflöden som definierar vilka användare (eller grupp) egenskaper flow från Azure AD till programmet. Den här konfigurationen innehåller inställningen ”matchande egenskapen” som används för att identifiera och matcha användare/grupper mellan de två systemen. Läs mer om den här viktiga processen [anpassa etablering attributet Användarmappningar för SaaS-program i Azure Active Directory](customize-application-attributes.md).
- **Attributmappningar för grupper:** Etablering av namn- och gruppinformation, förutom medlemmarna, om det stöds för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera den **mappning** för grupp-objekt som visas i den **etablering** fliken. Om etablering grupper är aktiverad, bör du granska attributmappningar för att säkerställa att ett lämpligt fält som används för ”matchande ID”. Matchande ID: T kan vara Visa namn eller e-postalias. Gruppen och dess medlemmar tillhandahålls inte om egenskapen matchande är tom eller inte fylls i automatiskt för en grupp i Azure AD.

## <a name="next-steps"></a>Nästa steg

[Azure AD Connect-synkronisering: Förstå deklarativ etablering](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
