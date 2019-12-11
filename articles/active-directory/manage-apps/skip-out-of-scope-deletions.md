---
title: Hoppa över borttagning av omfångs användare | Microsoft Docs
description: Lär dig hur du åsidosätter standard beteendet för inaktive ring av omfångs användare.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5a40b699c01f50ceb1bedbc36e7f1467772336f
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997079"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Hoppa över borttagning av användar konton som ingår i omfånget

Som standard tar Azure AD Provisioning-motorn bort eller inaktiverar användare som omfattas av omfånget. Men för vissa scenarier som arbets dagar till AD-användare inkommande etablering kan detta vara förväntat och du kanske vill åsidosätta det här standard beteendet.  

I den här guiden beskrivs hur du använder Microsoft Graph API och Microsoft Graph API Explorer för att ange flaggan ***SkipOutOfScopeDeletions*** som styr bearbetningen av konton som omfattas av omfånget. 
* Om ***SkipOutOfScopeDeletions*** är inställt på 0 (falskt) inaktive ras konton som omfattas av omfånget i målet
* Om ***SkipOutOfScopeDeletions*** är inställt på 1 (sant) inaktive ras inte konton som omfattas av omfånget i målet som den här flaggan anges på *etablerings appens* nivå och kan konfigureras med hjälp av Graph API. 

Eftersom den här konfigurationen ofta används med *arbets dagen för att Active Directory användar etablerings* appen, innehåller stegen nedan skärm bilder av programmet Workday. Detta kan dock också användas med **alla andra appar** (ServiceNow, Salesforce, Dropbox osv.).

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Steg 1: Hämta etablerings App Serviceens huvud-ID (objekt-ID)

1. Starta [Azure Portal](https://portal.azure.com)och navigera till avsnittet Egenskaper i ditt etablerings program. T. ex. om du vill exportera en *arbets dag till AD User Provisioning Application* -mappning navigerar du till avsnittet Egenskaper i appen. 
1. I avsnittet Egenskaper i din etablerings app kopierar du det GUID-värde som är associerat med fältet *objekt-ID* . Det här värdet kallas även **ServicePrincipalId** för din app och används i Graph-åtgärder.

   ![Workday App Service huvud-ID](./media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Steg 2: Logga in på Microsoft Graph Explorer

1. Starta [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Klicka på knappen "logga in med Microsoft" och logga in med Azure AD global admin-eller app admin-autentiseringsuppgifter.

    ![Graph-inloggning](./media/export-import-provisioning-mappings/wd_export_02.png)

1. Vid lyckad inloggning visas användar konto informationen i den vänstra rutan.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Steg 3: Hämta befintliga autentiseringsuppgifter för appar och anslutnings information

Kör följande GET-fråga i Microsoft Graph Explorer och Ersätt [servicePrincipalId] med **servicePrincipalId** som extraherats från [steg 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![Hämta jobb fråga](./media/skip-out-of-scope-deletions/skip-03.png)

Kopiera svaret till en textfil. Det ser ut som den JSON-text som visas nedan, med värden som är markerade med gul information för din distribution. Lägg till raderna som marker ATS i grönt till slutet och uppdatera arbets dagarnas anslutnings lösen ord markerade i blått. 

   ![Hämta jobb svar](./media/skip-out-of-scope-deletions/skip-04.png)

Här är JSON-blocket som ska läggas till i mappningen. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Steg 4: uppdatera hemligheter-slutpunkten med flaggan SkipOutOfScopeDeletions

I Graph Explorer kör du kommandot nedan för att uppdatera hemligheter-slutpunkten med flaggan ***SkipOutOfScopeDeletions*** . 

I URL: en nedan ersätter du [servicePrincipalId] med **servicePrincipalId** som extraherades från [steg 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Kopiera den uppdaterade texten från steg 3 till "begär ande texten" och ange huvudet "Content-Type" till "Application/JSON" i "begärandehuvuden". 

   ![SKICKA begäran](./media/skip-out-of-scope-deletions/skip-05.png)

Klicka på Kör fråga. 

Du bör få utdata som "lyckades – status kod 204". 

   ![Skicka svar](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Steg 5: kontrol lera att användare utanför räckvidden inte är inaktiverade

Du kan testa den här flaggan i förväntat beteende genom att uppdatera dina omfångs regler för att hoppa över en särskild användare. I exemplet nedan utesluter vi medarbetaren med ID 21173 (som tidigare var i området) genom att lägga till en ny omfattnings regel: 

   ![Omfångsdefinierande exempel](./media/skip-out-of-scope-deletions/skip-07.png)

I nästa etablerings cykel kommer Azure AD Provisioning-tjänsten att identifiera att användaren 21173 har slut på omfattning och om egenskapen SkipOutOfScopeDeletions har Aktiver ATS visas ett meddelande i synkroniseringsregeln som visas nedan: 

   ![Omfångsdefinierande exempel](./media/skip-out-of-scope-deletions/skip-08.png)


