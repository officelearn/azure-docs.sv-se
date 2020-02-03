---
title: Status för program etablering för karantän | Microsoft Docs
description: När du har konfigurerat ett program för automatisk användar etablering, lär du dig vad en etablerings status för karantän innebär och hur du avmarkerar den.
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
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 933217b2ad86535d45f6674165ee162f263a8cd7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712168"
---
# <a name="application-provisioning-in-quarantine-status"></a>Program etablering i karantän status

Azure AD Provisioning-tjänsten övervakar hälso tillståndet för din konfiguration och placerar appar utan hälso tillstånd i karantän. Om de flesta eller alla anrop som görs mot mål systemet fungerar konsekvent på grund av ett fel, till exempel ogiltiga administratörsautentiseringsuppgifter, markeras etablerings jobbet som i karantän.

I karantän sänks frekvensen för stegvisa cykler gradvis till en gång per dag. Etablerings jobbet tas bort från karantän när alla fel har åtgärd ATS och nästa synkronisering startar. Om etablerings jobbet finns kvar i karantän i mer än fyra veckor, är etablerings jobbet inaktiverat (slutar att köras).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Hur gör jag för att vet du om mitt program finns i karantän?

Det finns tre sätt att kontrol lera om ett program finns i karantän:
  
- I Azure Portal navigerar du till **Azure Active Directory** > **företags program** > &lt;*program namn*&gt;- **etablering** och bläddra till förlopps indikatorn längst ned. >   

  ![Status fältet för etablering visar karantän status](media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Använd Microsoft Graph begäran [Hämta synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) för att program mässigt Hämta status för etablerings jobbet:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Kontrol lera din e-postadress. När ett program placeras i karantän skickas ett e-postmeddelande med en Time-avisering. Om karantäns orsaken ändras skickas en uppdaterad e-postadress som visar den nya orsaken till karantänen. Om du inte ser något e-postmeddelande:

  - Kontrol lera att du har angett en giltig **e-postadress för aviseringar** i etablerings konfigurationen för programmet.
  - Se till att det inte finns någon skräp filtrering på Inkorgen för e-postavisering.
  - Se till att du inte har avbrutit prenumerationen på e-postmeddelanden.

## <a name="why-is-my-application-in-quarantine"></a>Varför är mitt program i karantän?

En Microsoft Graph begäran om att hämta status för etablerings jobbet visar följande orsak för karantänen:

- `EncounteredQuarantineException` anger att ogiltiga autentiseringsuppgifter har angetts. Etablerings tjänsten kan inte upprätta en anslutning mellan käll systemet och mål systemet.

- `EncounteredEscrowProportionThreshold` anger att etableringen överskred tröskelvärdet för depositions. Det här tillståndet inträffar när mer än 60% av etablerings händelserna misslyckades.

- `QuarantineOnDemand` innebär att vi har identifierat ett problem med ditt program och har ställt in det manuellt på karantän.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Hur gör jag för att hämta mitt program från karantänen?

Först löser du problemet som gjorde att programmet placerades i karantän.

- Kontrol lera programmets etablerings inställningar för att kontrol lera att du har [angett giltiga autentiseringsuppgifter för admin](configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD måste kunna upprätta ett förtroende med mål programmet. Kontrol lera att du har angett giltiga autentiseringsuppgifter och att ditt konto har de behörigheter som krävs.

- Granska [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md) för att ytterligare undersöka vilka fel som orsakar karantän och åtgärda felet. Få åtkomst till etablerings loggarna i Azure Portal genom att gå till **Azure Active Directory** &gt; **Enterprise-appar** &gt; **etablerings loggar (för hands version)** i avsnittet **aktivitet** .

När du har löst problemet startar du om etablerings jobbet. Vissa ändringar av programmets etablerings inställningar, till exempel attribut mappningar eller omfångs filter, startar automatiskt om etablering. Förlopps indikatorn på programmets **etablerings** sida anger när etableringen senast startades. Om du behöver starta om etablerings jobbet manuellt kan du använda någon av följande metoder:  

- Använd Azure Portal för att starta om etablerings jobbet. På programmets **etablerings** sida under **Inställningar**väljer du **Rensa tillstånd och startar om synkronisering** och ställer in **etablerings status** på **på**. Den här åtgärden startar om etablerings tjänsten helt, vilket kan ta lite tid. En fullständig första cykel körs igen, vilket rensar escrows, tar bort appen från karantänen och tar bort alla vattenstämplar.

- Använd Microsoft Graph för att [starta om etablerings jobbet](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Du har fullständig kontroll över vad du vill starta om. Du kan välja att rensa escrows (om du vill starta om depositions-räknaren som påförs i karantäns status), rensa karantän (för att ta bort programmet från karantän) eller rensa vattenstämplar. Använd följande begäran:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`