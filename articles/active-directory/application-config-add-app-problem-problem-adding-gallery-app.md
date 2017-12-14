---
title: "Lägga till ett program för Azure AD-galleriet problemet | Microsoft Docs"
description: "Förstå de vanliga problem personer står inför när du lägger till Azure AD-galleriet program och vad du kan göra för att lösa dem."
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 79713cd343e1b876cd7a1b6653bbe00b43272a55
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>Problem att lägga till ett program för Azure AD-galleriet

Den här artikeln hjälper dig att förstå de vanliga problem personer står inför när du lägger till Azure AD-galleriet program och vad du kan göra för att lösa problemen.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Jag har klickat på knappen ”Lägg till” och mitt program tog lång tid ska visas

I vissa fall kan det ta 1 till 2 minuter (och ibland längre) för ett program ska visas när du lägger till den i din katalog. Även om detta inte är normal prestanda ser programmet tillägg pågår genom att klicka på den **meddelanden** ikonen (sal) i övre högra hörnet på den [Azure Portal](https://portal.azure.com/) och letar efter en **pågår** eller **slutförd** meddelande med etiketten **skapa program.**

Om ditt program aldrig har lagts till, eller det uppstår ett fel när du klickar på den **Lägg till** knappen, visas en **meddelande** i en **fel** tillstånd. Om du vill ha mer information om felet för att lära dig mer om hur du eller dela med en stöd engingeer visas mer information om felet genom att följa stegen i den [så visas detaljerad information om en portalmeddelandet](#how-to-see-the-details-of-a-portal-notification) avsnitt.

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Jag har klickat på knappen ”Lägg till” och mitt program visas inte

Ibland på grund av temporära problem nätverksproblem eller ett programfel kan lägga till ett program misslyckas. Du ser detta händer när du klickar på den **meddelanden** ikonen (sal) i övre högra hörnet i Azure Portal och du ser ikonen red (!) bredvid ditt **skapa program** meddelande. Detta anger att ett fel uppstod när du skapar programmet.

Om det uppstår ett fel när du klickar på den **Lägg till** knappen, visas en **meddelande** i en **fel** tillstånd. Om du vill ha mer information om felet för att lära dig mer om hur du eller dela med en stöd engingeer visas mer information om felet genom att följa stegen i den [så visas detaljerad information om en portalmeddelandet](#how-to-see-the-details-of-a-portal-notification) avsnitt.

 ## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Jag vet inte hur du ställer in Mina program när jag har lagt till

Om du behöver hjälp med att lära dig om program, den [lista över självstudier om hur man integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) artikeln är en bra start.

Utöver detta kan den [dokumentbibliotek för Azure AD-program](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) hjälper dig att lära dig mer om enkel inloggning med Azure AD och hur det fungerar.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Hur du visar information om ett meddelande om portal

Du kan se information om eventuella portalmeddelandet genom att följa stegen nedan:

1.  Klicka på den **meddelanden** ikonen (sal) i övre högra hörnet i Azure Portal

2.  Markera alla meddelanden i en **fel** tillstånd (de med ett rött (!) bredvid dem).

    >[!NOTE]
    >Du kan klicka på meddelanden i en **lyckade** eller **pågår** tillstånd.
    >
    >

3.  Den här öppna den **detaljer** bladet.

4.  Använd den här informationen dig att förstå mer information om problemet.

5.  Om du fortfarande behöver hjälp kan du också dela informationen med en supporttekniker eller produktgruppen för att få hjälp med problemet.

6.  Klickar du på den **kopiera** **ikonen** till höger om den **Kopieringsfel** textruta för att kopiera meddelande allt delar med en support eller produkt grupp tekniker

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Få hjälp genom att skicka meddelandeinformation till en supporttekniker

Det är mycket viktigt att du dela **allt som anges nedan** med en supporttekniker om du behöver hjälp, så att de kan hjälpa dig snabbt. Du kan göra det enkelt av **tar en skärmbild** eller genom att klicka på den **kopiera felikonen**, hittade till höger om den **Kopieringsfel** textruta.

## <a name="notification-details-explained"></a>Meddelandeinformation förklaras

Den nedan beskrivs mer vad varje av meddelandet objekt innebär och innehåller exempel på var och en av dem.

### <a name="essential-notification-items"></a>Viktigt meddelande objekt

-   **Rubrik** – beskrivande rubrik i meddelandet

  * Exempel – **Application proxy-inställningar**

-   **Beskrivning** – beskrivning av vad som hänt på grund av åtgärden

    -   Exempel – **intern url har angett används redan av ett annat program**

-   **Meddelande-Id** – unikt id för meddelandet

    -   Exempel – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **Id för klientbegäran** – specifik begäran-id som din webbläsare

    -   Exempel – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Tid UTC stämpel** – tidsstämpeln då uppstod meddelandet i UTC

    -   Exempel – **2017-03-23T19:50:43.7583681Z**

-   **Internt transaktions-Id** – internt ID vi kan använda för att söka av fel i vårt system

    -   Exempel – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** – användaren som utförde åtgärden

    -   Exempel –**tperkins@f128.info**

-   **Klient-Id** – unikt ID för den klient som användaren som utförde åtgärden var medlem av

    -   Exempel – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Användarobjektet Id** – unikt ID för den användare som utförde åtgärden

    -   Exempel – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Detaljerat meddelande objekt

-   **Visningsnamn** – **(kan vara tom)** en mer detaljerad visningsnamn efter fel

    -   Exempel – **Application proxy-inställningar**

-   **Status för** – specifika status för meddelandet

    -   Exempel – **misslyckades**

-   **Objekt-Id** – **(kan vara tom)** objekt-ID som åtgärden utfördes

    -   Exempel – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Information om** – detaljerad beskrivning av vad som hänt på grund av åtgärden

    -   Exempel – **intern url 'http://bing.com/' är ogiltig eftersom den redan används**

-   **Kopiera fel** – klickar du på den **kopiera-ikonen** till höger om den **Kopieringsfel** textruta för att kopiera meddelande allt delar med en support eller produkt grupp tekniker

    -   Exempel```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](active-directory-enable-sso-scenario.md)
