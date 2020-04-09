---
title: Hantera inaktiva användarkonton i Azure AD | Microsoft-dokument
description: Lär dig mer om hur du identifierar och hanterar användarkonton i Azure AD som har blivit föråldrade
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56e44059268037cfd839fc7c877c5d6c972dead8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886049"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Så här: Hantera inaktiva användarkonton i Azure AD

I stora miljöer tas användarkonton inte alltid bort när medarbetare lämnar en organisation. Som IT-administratör vill du identifiera och hantera dessa föråldrade användarkonton eftersom de utgör en säkerhetsrisk.

I den här artikeln beskrivs en metod för att hantera föråldrade användarkonton i Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>Vad är inaktiva användarkonton?

Inaktiva konton är användarkonton som inte längre krävs av medlemmar i organisationen för att få åtkomst till dina resurser. En nyckelidentifierare för inaktiva konton är att de inte har använts *på ett tag för* att logga in på din miljö. Eftersom inaktiva konton är kopplade till inloggningsaktiviteten kan du använda tidsstämpeln för den senaste inloggningen som lyckades identifiera dem. 

Utmaningen med denna metod är att definiera vad *ett tag* betyder när det gäller din miljö. Användare kanske till exempel inte loggar in i en miljö *på ett tag*eftersom de är på semester. När du definierar vad deltat för inaktiva användarkonton är, måste du ta hänsyn till alla legitima skäl för att inte logga in på din miljö. I många organisationer är deltat för inaktiva användarkonton mellan 90 och 180 dagar. 

Den senaste lyckade inloggningen ger potentiella insikter om en användares fortsatta behov av åtkomst till resurser.  Det kan hjälpa dig att avgöra om gruppmedlemskap eller appåtkomst fortfarande behövs eller kan tas bort. För extern användarhantering kan du förstå om en extern användare fortfarande är aktiv i klienten eller bör rensas. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Så här identifierar du inaktiva användarkonton

Du identifierar inaktiva konton genom att utvärdera egenskapen **lastSignInDateTime** som exponeras av **signInActivity-resurstypen** för **Microsoft Graph** API. Med den här egenskapen kan du implementera en lösning för följande scenarier:

- **Användare efter namn**: I det här fallet söker du efter en viss användare efter namn, vilket gör att du kan utvärdera det sistaSignInDate:`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Användare efter datum**: I det här fallet begär du en lista över användare med en sistaSignInDateTime före ett angivet datum:`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>Vad du behöver veta

I det här avsnittet visas vad du behöver veta om egenskapen lastSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Hur kommer jag åt den här egenskapen?

Egenskapen **lastSignInDateTime** visas av [signInActivity-resurstypen](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta) för [Microsoft Graph REST API](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Är egenskapen lastSignInDateTime tillgänglig via cmdlet get-AzureAdUser?

Nej.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Vilken utgåva av Azure AD behöver jag för att komma åt egenskapen?

Du kan komma åt den här egenskapen i alla versioner av Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Vilken behörighet behöver jag för att läsa fastigheten?

För att läsa den här egenskapen måste du bevilja följande rättigheter: 

- AuditLogs.Read.All
- Organisation.Read.Alla  


### <a name="when-does-azure-ad-update-the-property"></a>När uppdaterar Azure AD egenskapen?

Varje interaktiv inloggning som lyckades resulterar i en uppdatering av det underliggande datalagret. Vanligtvis visas lyckade inloggningar i den relaterade inloggningsrapporten inom 10 minuter.
 

### <a name="what-does-a-blank-property-value-mean"></a>Vad betyder ett tomt egenskapsvärde?

Om du vill generera en tidsstämpel för sistaSignInDateTime behöver du en lyckad inloggning. Eftersom egenskapen lastSignInDateTime är en ny funktion kan värdet för egenskapen lastSignInDateTime vara tomt om:

- Den sista lyckade inloggningen för en användare ägde rum innan den här funktionen släpptes (1 december 2019).
- Det berörda användarkontot användes aldrig för en lyckad inloggning.

## <a name="next-steps"></a>Nästa steg

* [Hämta data med hjälp av Azure Active Directory Reporting-API:et med certifikat](tutorial-access-api-with-certificates.md)
* [Referens för gransknings-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [API-referens för inloggningsaktivitetsrapport](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
