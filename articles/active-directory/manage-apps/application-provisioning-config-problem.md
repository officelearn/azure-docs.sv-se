---
title: Problem med att konfigurera användaretablering för ett Azure AD-galleriprogram | Microsoft Docs
description: Så här felsöker du vanliga problem som kan stöta på när Konfigurera användaretablering för ett program redan visas i Azure AD-Programgalleriet
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 38075c9216eab955a023fee53aad74f3768d783c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55822280"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problem med att konfigurera användaretablering för ett Azure AD-galleriprogram

Konfigurera [automatisk användaretablering](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) för en app (om det stöds), kräver att specifika anvisningar följas för att förbereda programmet för automatisk etablering. Du kan sedan använda Azure-portalen för att konfigurera etableringstjänsten för att synkronisera användarkonton till programmet.

Du bör alltid börja genom att söka efter installationen självstudien specifika för att skapa etablering för ditt program. Följ dessa steg för att konfigurera både appen och Azure AD för att skapa etablering anslutningen. En lista över appen självstudier finns på [lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Så här att se om etablering fungerar 

När tjänsten är konfigurerad, kan de flesta insikter i driften av tjänsten ska ritas från två platser:

-   **Granskningsloggar** – etablering granskningsloggarna post alla åtgärder som utförts av etableringstjänsten, inklusive frågor till Azure AD för tilldelad användare som är inom omfånget för etablering. Fråga målappen för om användarna kommer att jämföra användarobjekt mellan systemet. Lägg sedan till, uppdatera eller inaktivera användarkontot i målsystemet baserat på en jämförelse. Etablering granskningsloggarna är tillgängliga i Azure-portalen i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt; granskningsloggarna** fliken. Filtrera loggarna på den **Kontoetablering** kategori för att bara se händelserna etablering för appen.

-   **Etableringsstatus –** att köra en sammanfattning av senaste etableringen för en viss app kan ses i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt;Etablering** avsnittet längst ned på skärmen under tjänstinställningarna. Det här avsnittet sammanfattas hur många användare (och/eller grupper) håller på att synkroniseras mellan de två systemen, och om det finns några fel. Information om att i granskningsloggarna. Observera att etableringsstatusen inte ifyllda tills en första fullständig synkronisering har slutförts mellan Azure AD och appen.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Allmän problemområden med etablering för att tänka på

Nedan visas en lista över de allmänna problemområden som innehåller detaljer om du har en uppfattning om var du ska börja.

* [Etableringstjänsten visas inte starta](#provisioning-service-does-not-appear-to-start)
* Det går inte att spara konfigurationen på grund av app autentiseringsuppgifter fungerar inte
* [Granskningsloggar säger användare ”skipped” och inte har etablerats, även om de är tilldelade](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Etableringstjänsten visas inte starta

Om du ställer in den **Etableringsstatus** vara **på** i den **Azure Active Directory &gt; Företagsappar &gt; \[programnamn\] &gt;Etablering** på Azure portal. Men inga andra statusinformation visas på sidan när efterföljande läses in på nytt. Det är troligt att tjänsten körs men har inte slutförts ännu en inledande synkronisering. Kontrollera den **granskningsloggar** som beskrivs ovan för att avgöra vilka åtgärder som tjänsten fungerar, och om det finns några fel.

>[!NOTE]
>En inledande synkronisering kan ta allt från 20 minuter till flera timmar beroende på storleken på Azure AD-katalog och antalet användare inom omfånget för etablering. Efterföljande synkroniseringar efter den första synkroniseringen vara snabbare, som lagrar vattenstämplar som representerar tillståndet för båda systemen efter den inledande synkroniseringen, förbättra prestanda för efterföljande synkroniseringar för etableringstjänsten.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Det går inte att spara konfigurationen på grund av app autentiseringsuppgifter fungerar inte

Azure AD kräver giltiga autentiseringsuppgifter som gör att den kan ansluta till en API som tillhandahålls av appen för användarhantering i ordning för etablering för att fungera. Om autentiseringsuppgifterna inte fungerar eller om du inte vet wat som de är, gå till självstudie för att konfigurera den här appen, som beskrivs ovan.

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Granskningsloggar säger användare hoppas över och inte har etablerats även om de är tilldelade

När en användare visas som ”skipped” i granskningsloggarna, är det mycket viktigt att läsa utökade informationen i loggmeddelande att fastställa orsaken. Nedan visas vanliga orsaker och lösningar:

-   **Ett Omfångsfilter har konfigurerats** **som filtrerar användaren baserat på ett attributvärde**. Läs mer på Omfångsfilter <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **Användaren har ”inte effektivt rätt”.** Om du ser det aktuella felmeddelandet, beror det på att det finns ett problem med användarposten för tilldelning lagras i Azure AD. För att åtgärda det här problemet, tilldela användaren (eller grupp) från appen och tilldela den igen. Läs mer på tilldelning <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Ett obligatoriskt attribut är inte ifyllt för en användare eller saknas.** En viktig sak att tänka på när du konfigurerar etablering är att granska och konfigurera attributmappningar och arbetsflöden som definierar vilka användare (eller grupp) egenskaper flow från Azure AD till programmet. Detta inkluderar egenskapen ”matchande” som används för att identifiera och matcha användare/grupper mellan de två systemen. Läs mer om den här viktiga processen <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

   * **Attributmappningar för grupper:** Etablering av namn- och gruppinformation, förutom medlemmarna, om det stöds för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera den **mappning** för grupp-objekt som visas i den **etablering** fliken. Om etablering grupper är aktiverad, bör du granska attributmappningar för att säkerställa att ett lämpligt fält som används för ”matchande ID”. Det kan vara Visa namn eller e-postalias), som gruppen och dess medlemmar inte etableras om matchning av egenskap är tom eller inte fylls i automatiskt för en grupp i Azure AD.

## <a name="next-steps"></a>Nästa steg
[Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md)
