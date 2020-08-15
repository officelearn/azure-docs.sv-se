---
title: Status för program etablering för karantän | Microsoft Docs
description: När du har konfigurerat ett program för automatisk användar etablering, lär du dig vad en etablerings status för karantän innebär och hur du avmarkerar den.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/28/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 54d02b3189825d08716b73b7250efd4e3f334aa0
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88234753"
---
# <a name="application-provisioning-in-quarantine-status"></a>Program etablering i karantän status

Azure AD Provisioning-tjänsten övervakar hälso tillståndet för din konfiguration och placerar appar utan hälso tillstånd i karantän. Om de flesta eller alla anrop som görs mot mål systemet fungerar konsekvent på grund av ett fel, till exempel ogiltiga administratörsautentiseringsuppgifter, markeras etablerings jobbet som i karantän.

I karantän sänks frekvensen för stegvisa cykler gradvis till en gång per dag. Etablerings jobbet tas bort från karantän när alla fel har åtgärd ATS och nästa synkronisering startar. Om etablerings jobbet finns kvar i karantän i mer än fyra veckor, är etablerings jobbet inaktiverat (slutar att köras).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Hur gör jag för att vet du om mitt program finns i karantän?

Det finns tre sätt att kontrol lera om ett program finns i karantän:
  
- I Azure Portal navigerar du till **Azure Active Directory**  >  **Enterprise applications**  >  &lt; *program namns* &gt;  >  **etablering** för företags program och granskar förlopps indikatorn för ett karantän meddelande.   

  ![Status fältet för etablering visar karantän status](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- I Azure Portal navigerar du till **Azure Active Directory**  >  **gransknings loggar** > filtrera på **aktivitet: karantän** och granska karantän historiken. Vyn i förlopps indikatorn enligt beskrivningen ovan visar om etableringen för närvarande finns i karantän, men gransknings loggarna gör att du kan se karantän historiken för ett program. 

- Använd Microsoft Graph begäran [Hämta synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta) för att program mässigt Hämta status för etablerings jobbet:

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- Gå igenom din e-post. När ett program placeras i karantän skickas ett e-postmeddelande med en Time-avisering. Om karantäns orsaken ändras skickas en uppdaterad e-postadress som visar den nya orsaken till karantänen. Om du inte ser något e-postmeddelande:

  - Kontrol lera att du har angett en giltig **e-postadress för aviseringar** i etablerings konfigurationen för programmet.
  - Se till att det inte finns någon skräp filtrering på Inkorgen för e-postavisering.
  - Se till att du inte har avbrutit prenumerationen på e-postmeddelanden.

## <a name="why-is-my-application-in-quarantine"></a>Varför är mitt program i karantän?

|Beskrivning|Rekommenderad åtgärd|
|---|---|
|**Problem med scim-kompatibilitet:** Ett HTTP/404-svar som inte hittades returnerades i stället för det förväntade HTTP/200 OK-svaret. I det här fallet har Azure AD Provisioning-tjänsten gjort en begäran till mål programmet och fått ett oväntat svar.|Kontrol lera avsnittet admin credentials för att se om programmet kräver att klient webb adressen anges och att webb adressen är korrekt. Om du inte ser något problem kontaktar du programutvecklaren för att se till att deras tjänster är SCIM-kompatibla. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Ogiltiga autentiseringsuppgifter:** Vid försök att bevilja åtkomst till mål programmet fick vi ett svar från mål programmet som anger att de angivna autentiseringsuppgifterna är ogiltiga.|Gå till avsnittet admin credentials i etablerings konfigurationens gränssnitt och auktorisera åtkomsten igen med giltiga autentiseringsuppgifter. Om programmet finns i galleriet läser du själv studie kursen om program konfiguration för eventuella ytterligare steg som krävs.|
|**Dubbla roller:** Roller som importeras från vissa program som Salesforce och Zendesk måste vara unika. |Navigera till applikations [manifestet](../develop/reference-app-manifest.md) i Azure Portal och ta bort den duplicerade rollen.|

 En Microsoft Graph begäran om att hämta status för etablerings jobbet visar följande orsak för karantänen:

- `EncounteredQuarantineException` indikerar att ogiltiga autentiseringsuppgifter har angetts. Etablerings tjänsten kan inte upprätta en anslutning mellan käll systemet och mål systemet.

- `EncounteredEscrowProportionThreshold` anger att etableringen överskred tröskelvärdet för depositions. Det här tillståndet inträffar när mer än 60% av etablerings händelserna misslyckades.

- `QuarantineOnDemand` innebär att vi har identifierat ett problem med ditt program och har ställt in det manuellt på karantän.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Hur gör jag för att hämta mitt program från karantänen?

Först löser du problemet som gjorde att programmet placerades i karantän.

- Kontrol lera programmets etablerings inställningar för att kontrol lera att du har [angett giltiga autentiseringsuppgifter för admin](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD måste kunna upprätta ett förtroende med mål programmet. Kontrol lera att du har angett giltiga autentiseringsuppgifter och att ditt konto har de behörigheter som krävs.

- Granska [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md) för att ytterligare undersöka vilka fel som orsakar karantän och åtgärda felet. Få åtkomst till etablerings loggarna i Azure Portal genom att gå till **Azure Active Directory** &gt; etablerings loggar för **företags appar** &gt; **(för hands version)** i avsnittet **aktivitet** .

När du har löst problemet startar du om etablerings jobbet. Vissa ändringar av programmets etablerings inställningar, till exempel attribut mappningar eller omfångs filter, startar automatiskt om etablering. Förlopps indikatorn på programmets **etablerings** sida anger när etableringen senast startades. Om du behöver starta om etablerings jobbet manuellt kan du använda någon av följande metoder:  

- Använd Azure Portal för att starta om etablerings jobbet. På programmets **etablerings** sida under **Inställningar**väljer du **Rensa tillstånd och startar om synkronisering** och ställer in **etablerings status** på **på**. Den här åtgärden startar om etablerings tjänsten helt, vilket kan ta lite tid. En fullständig första cykel körs igen, vilket rensar escrows, tar bort appen från karantänen och tar bort alla vattenstämplar.

- Använd Microsoft Graph för att [starta om etablerings jobbet](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). Du har fullständig kontroll över vad du vill starta om. Du kan välja att rensa escrows (om du vill starta om depositions-räknaren som påförs i karantäns status), rensa karantän (för att ta bort programmet från karantän) eller rensa vattenstämplar. Använd följande begäran:
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

Ersätt "{ID}" med värdet för program-ID: t och Ersätt "{jobId}" med [ID: t för synkroniseringsschemat](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta#list-synchronization-jobs-in-the-context-of-the-service-principal).