---
title: Hoppa över radering av användare utanför räckvidden | Microsoft-dokument
description: Lär dig hur du åsidosätter standardbeteendet för avetablering av scopeanvändare.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1668c022a0f067a381ba09b397c7d38c99ad074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522457"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Hoppa över radering av användarkonton som inte omfattas

Som standard tar Azure AD-etableringsmotorn mjuk bort eller inaktiverar användare som inte omfattas. Men för vissa scenarier som Workday to AD User Inbound Provisioning det här beteendet kanske inte är förväntat och du kanske vill åsidosätta det här standardbeteendet.  

I den här guiden beskrivs hur du använder Microsoft Graph API och Microsoft Graph API explorer för att ange flaggan ***SkipOutOfScopeDeletions*** som styr bearbetningen av konton som inte omfattas. 
* Om ***SkipOutOfScopeDeletions*** är inställt på 0 (falskt), inaktiveras konton som går utanför omfånget i målet
* Om ***SkipOutOfScopeDeletions*** är inställt på 1 (sant) inaktiveras inte konton som går utanför omfånget i målet Den här flaggan anges på *etableringsappnivå* och kan konfigureras med graph-API:et. 

Eftersom den här konfigurationen ofta används med appen *Workday to Active Directory-användare,* innehåller stegen nedan skärmbilder av Workday-programmet. Men detta kan också användas med **alla andra appar** som (ServiceNow, Salesforce, Dropbox, etc.).

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Steg 1: Hämta ditt etableringsapptjänsthuvudhuvuden-ID (object ID)

1. Starta [Azure-portalen](https://portal.azure.com)och navigera till avsnittet Egenskaper i etableringsprogrammet. Om du till exempel vill exportera din *arbetsdag till AD-användare etablering programmappning* navigera till avsnittet Egenskaper i den appen. 
1. Kopiera GUID-värdet som är associerat med fältet *Objekt-ID* i avsnittet Egenskaper i etableringsappen. Det här värdet kallas också **ServicePrincipalId** för din app och används i Graph Explorer-åtgärder.

   ![Huvud-ID för tjänsten Workday-tjänsten](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Steg 2: Logga in på Microsoft Graph Explorer

1. Starta [Utforskaren i Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Klicka på knappen "Logga in med Microsoft" och logga in med Azure AD Global Admin- eller App Admin-autentiseringsuppgifter.

    ![Logga in i diagram](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. När du har loggat in igen visas information om användarkontot i den vänstra rutan.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Steg 3: Få information om befintliga appuppgifter och anslutningsuppgifter

I Utforskaren i Microsoft Graph kör du följande GET-fråga som ersätter [servicePrincipalId] med **ServicePrincipalId** som extraherats från [steg 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![GET-jobbfråga](./media/skip-out-of-scope-deletions/skip-03.png)

Kopiera svaret till en textfil. Den kommer att se ut som JSON-texten som visas nedan, med värden markerade i gult som är specifika för distributionen. Lägg till raderna markerade i grönt till slutet och uppdatera lösenordet för workday-anslutning markerat i blått. 

   ![FÅ jobbsvar](./media/skip-out-of-scope-deletions/skip-04.png)

Här är JSON-blocket som ska läggas till i mappningen. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Steg 4: Uppdatera slutpunkten för hemligheter med flaggan SkipOutOfScopeDeletions

I Graph Explorer kör du kommandot nedan för att uppdatera slutpunkten hemligheter med flaggan ***SkipOutOfScopeDeletions.*** 

I webbadressen nedan ersätter [servicePrincipalId] med **ServicePrincipalId** som extraherats från [steg 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Kopiera den uppdaterade texten från steg 3 till "Request Body" och ange rubriken "Content-Type" till "application/json" i "Request Headers". 

   ![PUT-begäran](./media/skip-out-of-scope-deletions/skip-05.png)

Klicka på "Kör fråga". 

Du bör få utdata som "Framgång - Status Code 204". 

   ![PUT-svar](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Steg 5: Kontrollera att användare utanför scopet inte inaktiveras

Du kan testa den här flaggan resulterar i förväntat beteende genom att uppdatera dina omfångsregler för att hoppa över en viss användare. I exemplet nedan utesluter vi medarbetaren med ID 21173 (som tidigare var i omfång) genom att lägga till en ny omfångsregel: 

   ![Exempel på omfång](./media/skip-out-of-scope-deletions/skip-07.png)

I nästa etableringscykel identifierar Azure AD-etableringstjänsten att användaren 21173 har försvunnit och om egenskapen SkipOutOfScopeDeletions är aktiverad visas ett meddelande enligt nedan synkroniseringsregeln för användaren: 

   ![Exempel på omfång](./media/skip-out-of-scope-deletions/skip-08.png)


