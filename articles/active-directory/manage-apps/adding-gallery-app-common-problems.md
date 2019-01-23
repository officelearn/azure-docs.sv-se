---
title: Problem med att lägga till en Azure AD-galleriprogram | Microsoft Docs
description: Förstå de vanliga problem personer står inför när du lägger till Azure AD-galleriprogram och vad du kan göra för att lösa dem
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: barbkess
ms.openlocfilehash: 29538d4cab4df328ba9cbff2f30c35626b3360dd
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477608"
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>Problem med att lägga till en Azure AD-galleriprogram

Den här artikeln hjälper dig att förstå de vanliga problem personer står inför när du lägger till Azure AD-galleriprogram och vad du kan göra för att lösa dem.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Jag klickade på knappen ”Lägg till” och tog lång tid att visas för mitt program

Under vissa omständigheter kan det ta 1 – 2 minuter (och ibland längre) för ett program ska visas när du lägger till den till din katalog. Även om detta inte är normal prestanda du kan se programmet tillägget håller på att skapas genom att klicka på den **meddelanden** ikonen (klockan) i övre högra hörnet av den [Azure-portalen](https://portal.azure.com/) och söka för en **pågår** eller **slutförd** meddelande som är märkt **skapa program.**

Om programmet aldrig har lagt till, eller om det uppstår ett fel när du klickar på den **Lägg till** knappen, visas en **meddelande** i en **fel** tillstånd. Om du vill ha mer information om felet för att lära dig mer att eller dela med en supporttekniker kan du se mer information om felet genom att följa stegen i den [hur du visar information om ett Portalmeddelande om](#how-to-see-the-details-of-a-portal-notification) avsnittet.

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Jag klickade på knappen ”Lägg till” och förekommer inte mitt program

Ibland, på grund av tillfälliga problem nätverksproblem eller en bugg lägger till ett program misslyckas. Du ser det här inträffar när du klickar på den **meddelanden** ikonen (klockan) uppe till höger i Azure-portalen och du ser en ikon med rött (!) bredvid ditt **skapa program** meddelande. Detta anger att ett fel uppstod när du skapar programmet.

Om det uppstår ett fel när du klickar på den **Lägg till** knappen, visas en **meddelande** i en **fel** tillstånd. Om du vill ha mer information om felet för att lära dig mer att eller dela med en supporttekniker kan du se mer information om felet genom att följa stegen i den [hur du visar information om ett Portalmeddelande om](#how-to-see-the-details-of-a-portal-notification) avsnittet.

 ## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Jag vet inte hur du ställer in mitt program när jag har lagt till den

Om du behöver hjälp med att lära dig om program, den [lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) artikeln är ett bra ställe att börja.

Utöver detta är den [dokumentbibliotek för Azure AD-program](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) hjälper dig att lära dig mer om enkel inloggning med Azure AD och hur det fungerar.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Hur du visar information om en portal-meddelande

Du kan se information om alla portal-meddelande genom att följa stegen nedan:

1.  Klicka på den **meddelanden** ikonen (klockan) uppe till höger på Azure Portal

2.  Välj något meddelande i en **fel** tillstånd (de med ett rött (!) bredvid dem).

    >[!NOTE]
    >Du kan klicka på aviseringar i en **lyckade** eller **pågår** tillstånd.
    >
    >

4.  Använd informationen under **meddelandeinformation** att förstå mer information om problemet.

5.  Om du fortfarande behöver hjälp kan du också dela information med en supporttekniker eller produktgruppen för att få hjälp med problemet.

6.  Klicka på den **kopia** **ikonen** till höger om den **kopiera fel** textrutan att kopiera alla meddelandeinformation att dela med en support eller produkt grupp-tekniker

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Få hjälp genom att skicka information om meddelande till en supporttekniker

Det är mycket viktigt att du dela **all information som anges nedan** med en supporttekniker om du behöver hjälp, så att de kan hjälpa dig snabbt. Du kan göra det enkelt för **tar en skärmbild** eller genom att klicka på den **kopia felikon**, som finns till höger om den **Kopieringsfel** textrutan.

## <a name="notification-details-explained"></a>Meddelandeinformation förklaras

Se följande beskrivningar för mer information om meddelanden.

### <a name="essential-notification-items"></a>Viktigt meddelande objekt

-   **Rubrik** – beskrivande rubrik för meddelanden

  * Exempel – **proxyinställningarna för programmet**

-   **Beskrivning av** – beskrivning av vad som hänt på grund av åtgärden

    -   Exempel – **interna URL: en som angetts används redan av ett annat program**

-   **Meddelande-ID** – unikt ID för meddelandet

    -   Exempel – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **ID för klientbegäran** – specifik begäran-ID som gjorts av din webbläsare

    -   Exempel – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Tid UTC för stämpel** – tidsstämpeln som aviseringen inträffade, i UTC

    -   Exempel – **2017-03-23T19:50:43.7583681Z**

-   **Internt transaktions-ID** – internt ID vi kan använda för att kontrollera felet i våra system

    -   Exempel – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** – den användare som utförde åtgärden

    -   Exempel – **tperkins@f128.info**

-   **Klient-ID** – unikt ID för den klient som den användare som utförde åtgärden var medlem av

    -   Exempel – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Objekt-ID för användaren** – unikt ID för den användare som utförde åtgärden

    -   Exempel – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Detaljerat meddelande objekt

-   **Visningsnamn** – **(kan vara tom)** en mer detaljerad visningsnamn för felet

    -   Exempel – **proxyinställningarna för programmet**

-   **Status för** – specifika status för meddelandet

    -   Exempel – **misslyckades**

-   **Objekt-ID** – **(kan vara tom)** objekt-ID som åtgärden utfördes

    -   Exempel – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Information om** – detaljerad beskrivning av vad som hänt på grund av åtgärden

    -   Exempel – **interna URL: en ”https://bing.com/' är ogiltig eftersom den inte redan används**

-   **Kopiera fel** – klickar du på den **kopieringsikonen** till höger om den **Kopieringsfel** textrutan att kopiera alla meddelandeinformation att dela med en grupp för support eller produkt 
-   tekniker

    -   Exempel ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

