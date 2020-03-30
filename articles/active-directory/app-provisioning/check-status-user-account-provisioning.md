---
title: Rapportera automatisk etablering av användarkonton till SaaS-program
description: Lär dig hur du kontrollerar statusen för automatiska jobb för etablering av användarkonton och hur du felsöker etablering av enskilda användare.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19d76f69669ffa13d1d55ffa807e6c4818b8840c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282196"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Självstudiekurs: Rapportering om automatisk etablering av användarkonton

Azure Active Directory (Azure AD) innehåller en tjänst för etablering av [användarkonton](user-provisioning.md) som hjälper till att automatisera etableringen av deetableringar av användarkonton i SaaS-appar och andra system, i syfte att hantera identitetslivscykel från sluten till. Azure AD stöder förintegrerade användaretableringskopplingar för alla program och system med självstudier för användaretablering [här](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list).

I den här artikeln beskrivs hur du kontrollerar statusen för etableringsjobb när de har konfigurerats och hur du felsöker etableringen av enskilda användare och grupper.

## <a name="overview"></a>Översikt

Etableringsappar konfigureras och konfigureras med hjälp av [Azure-portalen](https://portal.azure.com)genom att följa den [dokumentation som finns](../saas-apps/tutorial-list.md) för det program som stöds. När etableringsjobben har konfigurerats och körs kan de rapporteras med en av två metoder:

* **Azure portal** - Den här artikeln beskriver främst hämtar rapportinformation från [Azure-portalen](https://portal.azure.com), som innehåller både en etableringssammanfattningsrapport samt detaljerade granskningsloggar för ett visst program.
* **Gransknings-API** - Azure Active Directory tillhandahåller också ett gransknings-API som möjliggör programmatisk hämtning av detaljerade granskningsloggar för etablering. Se [Azure Active Directory audit API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) för dokumentation som är specifik för att använda det här API:et. Den här artikeln täcker inte specifikt hur API:et ska användas, men beskrivs i detalj vilka typer av etableringshändelser som registreras i granskningsloggen.

### <a name="definitions"></a>Definitioner

I den här artikeln används följande termer, som definieras nedan:

* **Source System** - Lagringsplatsen för användare som Azure AD-etableringstjänsten synkroniserar från. Azure Active Directory är källsystemet för de flesta förintegrerade etableringsappar, men det finns några undantag (exempel: Arbetsdags inkommande synkronisering).
* **Target System** - Lagringsplatsen för användare som Azure AD-etableringstjänsten synkroniserar med. Detta är vanligtvis ett SaaS-program (exempel: Salesforce, ServiceNow, G Suite, Dropbox för företag), men i vissa fall kan vara ett lokalt system som Active Directory (exempel: Arbetsdags inkommande synkronisering till Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Hämta etableringsrapporter från Azure-portalen

Om du vill hämta etableringsrapportinformation för ett visst program börjar du med att starta [Azure-portalen](https://portal.azure.com) och **Azure Active Directory** &gt; **Enterprise AppsTabler** &gt; **(förhandsversion)** i avsnittet **Aktivitet.** Du kan också bläddra till det företagsprogram som etableringen är konfigurerad för. Om du till exempel etablerar användare till LinkedIn Elevate är navigeringssökvägen till programinformationen:

**Azure Active Directory > Enterprise-program > alla program > LinkedIn Elevate**

Härifrån kan du komma åt både etableringsförloppet och etableringsloggarna, som beskrivs nedan.

## <a name="provisioning-progress-bar"></a>Etablera förloppsindikator

[Förloppsindikatorn för etablering](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) visas på fliken **Etablering** för ett visst program. Den finns i avsnittet **Aktuell status** under **Inställningar**och visar status för den aktuella inledande eller inkrementella cykeln. I det här avsnittet visas också:

* Det totala antalet användare och/grupper som har synkroniserats och som för närvarande är i omfång för etablering mellan källsystemet och målsystemet.
* Sista gången synkroniseringen kördes. Synkroniseringar sker vanligtvis var 20-40 minuter, efter att en [inledande cykel](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) har slutförts.
* Om en [inledande cykel](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) har slutförts eller inte.
* Om etableringsprocessen har placerats i karantän eller inte och vad orsaken till karantänstatusen är (till exempel underlåtenhet att kommunicera med målsystemet på grund av ogiltiga administratörsautentiseringsuppgifter).

Aktuell **status** bör vara den första platsen admins leta efter att kontrollera den operativa hälsan för etablering jobbet.

 ![Sammanfattande rapport](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Etablera loggar (förhandsgranskning)

Alla aktiviteter som utförs av etableringstjänsten registreras i Azure [AD-etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Du kan komma åt etableringsloggarna i Azure-portalen genom att välja **Azure Active Directory** &gt; **Enterprise Apps** &gt; **Provisioning loggar (förhandsversion)** i avsnittet **Aktivitet.** Du kan söka i etableringsdata baserat på namnet på användaren eller identifieraren i antingen källsystemet eller målsystemet. Mer information finns i [Etableringsloggar (förhandsgranskning)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Händelsetyper för loggade aktiviteter omfattar:

## <a name="troubleshooting"></a>Felsökning

Etableringssammanfattningsrapporten och etableringsloggarna spelar en viktig roll för att hjälpa administratörer att felsöka olika problem med etablering av användarkonton.

Scenariobaserad vägledning om hur du felsöker automatisk användaretablering finns i [Problem med att konfigurera och etablera användare i ett program](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
