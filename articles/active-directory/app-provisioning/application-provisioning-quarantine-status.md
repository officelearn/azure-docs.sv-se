---
title: Ansökan etablering status karantän | Microsoft-dokument
description: När du har konfigurerat ett program för automatisk användaretablering kan du läsa om vad en etableringsstatus för karantän innebär och hur du rensar den.
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
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563c049bf3d1606e87db54e3b003dac987594610
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154635"
---
# <a name="application-provisioning-in-quarantine-status"></a>Etablering av program i karantänstatus

Azure AD-etableringstjänsten övervakar hälsotillståndet för din konfiguration och placerar felaktiga appar i ett "karantäntillstånd". Om de flesta eller alla anrop som görs mot målsystemet konsekvent misslyckas på grund av ett fel, till exempel ogiltiga administratörsautentiseringsuppgifter, markeras etableringsjobbet som i karantän.

Medan i karantän, frekvensen av inkrementella cykler gradvis minskas till en gång per dag. Etableringsjobbet tas bort från karantänen när alla fel har åtgärdats och nästa synkroniseringscykel startar. Om etableringsjobbet förblir i karantän i mer än fyra veckor inaktiveras etableringsjobbet (slutar köras).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Hur vet jag om min ansökan är i karantän?

Det finns tre sätt att kontrollera om ett program är i karantän:
  
- I Azure-portalen navigerar du till **Azure Active Directory** > **Enterprise-programprogramnamn** > &lt;*application name*&gt; > **Etablering** och bläddrar till förloppsindikatorn längst ned.  

  ![Statusfält för etablering som visar karantänstatus](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- I Azure-portalen navigerar du till **Azure Active Directory** > **Audit Logs** > filter på **Aktivitet: Karantän** och granska karantänhistoriken. Medan vyn i förloppsindikatorn enligt beskrivningen ovan visar om etableringen för närvarande är i karantän, kan du i granskningsloggarna se karantänhistoriken för ett program. 

- Använd Microsoft Graph-begäran [Hämta synkroniseringJobb](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) för att programmässigt hämta status för etableringsjobbet:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Gå igenom din e-post. När ett program placeras i karantän skickas ett e-postmeddelande med en engångsavisering. Om karantänsorsaken ändras skickas ett uppdaterat e-postmeddelande som visar den nya orsaken till karantänen. Om du inte ser något e-postmeddelande:

  - Kontrollera att du har angett ett giltigt **meddelandemeddelandemeddelande** i etableringskonfigurationen för programmet.
  - Kontrollera att det inte finns någon skräppostfiltrering i e-postkorgen för anmälan.
  - Se till att du inte har avslutat prenumerationen på e-postmeddelanden.

## <a name="why-is-my-application-in-quarantine"></a>Varför är min ansökan i karantän?

En Microsoft Graph-begäran om att få status för etableringsjobbet visar följande orsak till karantän:

- `EncounteredQuarantineException`anger att ogiltiga autentiseringsuppgifter har angetts. Etableringstjänsten kan inte upprätta en anslutning mellan källsystemet och målsystemet.

- `EncounteredEscrowProportionThreshold`anger att avsättningen överskred spärrtröskeln. Det här villkoret uppstår när mer än 60 % av etableringshändelserna misslyckades.

- `QuarantineOnDemand`innebär att vi har upptäckt ett problem med ditt program och manuellt har ställt in det till karantän.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Hur får jag min ansökan ur karantän?

Lös först problemet som gjorde att programmet placerades i karantän.

- Kontrollera programmets etableringsinställningar för att kontrollera att du har [angett giltiga administratörsautentiseringsuppgifter](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD måste kunna upprätta ett förtroende med målprogrammet. Kontrollera att du har angett giltiga autentiseringsuppgifter och att ditt konto har de behörigheter som krävs.

- Granska [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att ytterligare undersöka vilka fel som orsakar karantän och åtgärda felet. Få tillgång till etableringsloggarna i Azure-portalen genom att gå till **Azure Active Directory** &gt; **Enterprise AppsTables** &gt; **Provisioning logs (preview)** i avsnittet **Aktivitet.**

När du har löst problemet startar du om etableringsjobbet. Vissa ändringar av programmets etableringsinställningar, till exempel attributmappningar eller omfångsfilter, startas automatiskt om etableringen åt dig. Förloppsindikatorn på programmets **etableringssida** anger när etableringen senast startades. Om du behöver starta om etableringsjobbet manuellt använder du någon av följande metoder:  

- Använd Azure-portalen för att starta om etableringsjobbet. På programmets **etableringssida** under **Inställningar**väljer du **Rensa tillstånd och starta om synkroniseringen** och ange **etableringsstatus** **till På**. Den här åtgärden startar om etableringstjänsten helt, vilket kan ta lite tid. En fullständig inledande cykel körs igen, vilket rensar spärrar, tar bort appen från karantänen och rensar alla vattenstämplar.

- Använd Microsoft Graph för att [starta om etableringsjobbet](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Du har full kontroll över vad du startar om. Du kan välja att rensa spärrade (för att starta om spärrräknaren som ackumuleras mot karantänstatus), rensa karantän (för att ta bort programmet från karantän) eller rensa vattenstämplar. Använd följande begäran:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
