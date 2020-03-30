---
title: Problem med att konfigurera användaretablering till en Azure AD Gallery-app
description: Felsöka vanliga problem när du konfigurerar användaretablering till ett program som redan finns i Azure AD Application Gallery
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3955b96e4a7edfc79a229d927523bdd4473409d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522772"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problem med att konfigurera användaretablering till ett Azure AD Gallery-program

Konfigurera [automatisk användaretablering](user-provisioning.md) för en app (där det stöds), kräver att specifika instruktioner följs för att förbereda programmet för automatisk etablering. Sedan kan du använda Azure-portalen för att konfigurera etableringstjänsten för att synkronisera användarkonton till programmet.

Du bör alltid börja med att hitta den installationshandledning som är specifik för att konfigurera etablering för ditt program. Följ sedan dessa steg för att konfigurera både appen och Azure AD för att skapa etableringsanslutningen. En lista över app självstudier finns på [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md).

## <a name="how-to-see-if-provisioning-is-working"></a>Så här ser du om etableringen fungerar 

När tjänsten har konfigurerats kan de flesta insikter om tjänstens funktion hämtas från två platser:

-   **Etableringsloggar (förhandsversion)** – [Etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) registrerar alla åtgärder som utförs av etableringstjänsten, inklusive att fråga Azure AD för tilldelade användare som är i omfång för etablering. Fråga målappen för att det finns dessa användare och jämför användarobjekten mellan systemet. Lägg sedan till, uppdatera eller inaktivera användarkontot i målsystemet baserat på jämförelsen. Du kan komma åt etableringsloggarna i Azure-portalen genom att välja **Azure Active Directory** &gt; **Enterprise Apps** &gt; **Provisioning loggar (förhandsversion)** i avsnittet **Aktivitet.**

-   **Aktuell status –** En sammanfattning av den senaste etableringskörningen för en viss app kan ses i avsnittet **Azure Active &gt; Directory Enterprise Apps &gt; \[Application\] &gt;Name Provisioning** längst ned på skärmen under tjänstinställningarna. Avsnittet Aktuell status visar om en etableringscykel har börjat etablera användarkonton. Du kan titta på förloppet för cykeln, se hur många användare och grupper som har etablerats och se hur många roller som skapas. Om det finns några fel finns information i [Etableringsloggarna (.. /reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Allmänna problemområden med etablering att överväga

Nedan finns en lista över de allmänna problemområden som du kan borra i om du har en idé om var du ska börja.

* [Etableringstjänsten verkar inte starta](#provisioning-service-does-not-appear-to-start)
* Det går inte att spara konfigurationen på grund av att appautentiseringsuppgifterna inte fungerar
* [Etableringsloggar säger att användarna "hoppas över" och inte etableras, även om de har tilldelats](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Etableringstjänsten verkar inte starta

Om du anger att **etableringsstatus** ska vara **på** i avsnittet **Azure Active &gt; Directory Enterprise Apps &gt; \[Application Name\] &gt;Provisioning** i Azure-portalen. Men inga andra statusdetaljer visas på den sidan efter efterföljande omladdningar. Det är troligt att tjänsten körs men inte har slutfört en inledande cykel ännu. Kontrollera **de etableringsloggar** som beskrivs ovan för att avgöra vilka åtgärder tjänsten utför och om det finns några fel.

>[!NOTE]
>En inledande cykel kan ta allt från 20 minuter till flera timmar, beroende på storleken på Azure AD-katalogen och antalet användare i omfånget för etablering. Efterföljande synkroniseringar efter den inledande cykeln går snabbare, eftersom etableringstjänsten lagrar vattenstämplar som representerar tillståndet för båda systemen efter den inledande cykeln, vilket förbättrar prestanda för efterföljande synkroniseringar.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Det går inte att spara konfigurationen på grund av att appautentiseringsuppgifterna inte fungerar

För att etableringen ska fungera kräver Azure AD giltiga autentiseringsuppgifter som gör att den kan ansluta till ett API för användarhantering som tillhandahålls av den appen. Om dessa autentiseringsuppgifter inte fungerar, eller om du inte vet vad de är, läser du självstudien för att konfigurera den här appen, som beskrivits tidigare.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Etableringsloggar säger att användare hoppas över och inte etableras trots att de har tilldelats

När en användare visas som "hoppas över" i etableringsloggarna är det mycket viktigt att läsa den utökade informationen i loggmeddelandet för att fastställa orsaken. Nedan finns vanliga orsaker och lösningar:

- **Ett omfångsfilter har konfigurerats** **som filtrerar bort användaren baserat på ett attributvärde**. Mer information finns i [Attributbaserad programetablering med omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

- **Användaren är "inte effektivt rätt".** Om det här specifika felmeddelandet visas beror det på att det finns ett problem med användartilldelningsposten som lagras i Azure AD. Lös problemet genom att av tilldelar användaren (eller gruppen) från appen och tilldelar den igen. Mer information finns i [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md).

- **Ett obligatoriskt attribut saknas eller fylls inte i för en användare.** En viktig sak att tänka på när du ställer in etablering är att granska och konfigurera attributmappningar och arbetsflöden som definierar vilka användaregenskaper (eller gruppegenskaper) som flödar från Azure AD till programmet. Detta inkluderar att ange den "matchande egenskap" som används för att unikt identifiera och matcha användare/grupper mellan de två systemen. Mer information om den här viktiga processen finns i [Anpassa användares etablering av attributmappningar](../app-provisioning/customize-application-attributes.md).

  * **Attributmappningar för grupper:** Etablering av gruppnamn och gruppinformation, förutom medlemmarna, om det stöds för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera **mappning** för gruppobjekt som visas på fliken **Etablering.** Om etableringsgrupper är aktiverade måste du granska attributmappningarna för att säkerställa att ett lämpligt fält används för "matchande ID". Detta kan vara visningsnamnet eller e-postaliaset), eftersom gruppen och dess medlemmar inte etableras om den matchande egenskapen är tom eller inte fylls i för en grupp i Azure AD.

## <a name="next-steps"></a>Nästa steg
[Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md)
