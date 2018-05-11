---
title: Problem att lägga till en icke-galleriet program | Microsoft Docs
description: Förstå de vanliga problem personer står inför när du lägger till anpassade program för icke-galleriet
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 3d54a9bccdf3f940959c2cbe3f85bd5fddff68e1
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="problem-adding-a-non-gallery-application"></a>Problem att lägga till en icke-galleriet program

Den här artikeln hjälper dig att förstå de vanliga problem personer står inför när du lägger till **anpassade program för icke-galleriet** och vad du kan göra för att lösa problemen. 

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Jag har klickat på knappen ”Lägg till” och mitt program tog lång tid ska visas

I vissa fall kan det ta 1 till 2 minuter (och ibland längre) för ett program ska visas när du lägger till den i din katalog. Även om detta inte är normal prestanda ser programmet tillägg pågår genom att klicka på den **meddelanden** ikonen (sal) i övre högra hörnet på de [Azure-portalen](https://portal.azure.com/) och titta för en **pågår** eller **slutförd** meddelande med etiketten **skapa program**.

Om ditt program aldrig har lagts till, eller det uppstår ett fel när du klickar på den **Lägg till** knappen, visas en **meddelande** i en **fel** tillstånd. Om du vill ha mer information om felet för att lära dig mer om hur du eller dela med en supporttekniker, kan du se mer information om felet genom att följa stegen i den [så visas detaljerad information om en portalmeddelandet](#how-to-see-the-details-of-a-portal-notification) avsnitt.

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Jag har klickat på knappen ”Lägg till” och mitt program visas inte

Ibland på grund av temporära problem nätverksproblem eller ett programfel kan lägga till ett program misslyckas. Du ser detta händer när du klickar på den **meddelanden** ikonen (sal) i övre högra hörnet i Azure portal och du ser ikonen red (!) bredvid ditt **skapa program** meddelande. Detta anger att ett fel uppstod när du skapar programmet.

Om det uppstår ett fel när du klickar på den **Lägg till** knappen, visas en **meddelande** i en **fel** tillstånd. Om du vill ha mer information om felet för att lära dig mer om hur du eller dela med en supporttekniker, kan du se mer information om felet genom att följa stegen i den [så visas detaljerad information om en portalmeddelandet](#how-to-see-the-details-of-a-portal-notification) avsnitt.

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Jag vet inte hur du ställer in Mina program när jag har lagt till

Om du behöver hjälp med att lära dig om anpassade program i [dokumentbibliotek för Azure AD-program](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) hjälper dig att lära dig mer om enkel inloggning med Azure AD och hur det fungerar.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Hur du visar information om ett meddelande om portal

Du kan se information om eventuella portalmeddelandet genom att följa stegen nedan:

1.  Klicka på den **meddelanden** ikonen (sal) i övre högra hörnet i Azure-portalen

2.  Markera alla meddelanden i en **fel** tillstånd (de med ett rött (!) bredvid dem).

   >[!NOTE]
   >Du kan klicka på meddelanden i en **lyckade** eller **pågår** tillstånd.
   >
   >

4.  Använd informationen under **detaljer** att lära dig mer information om problemet.

5.  Om du fortfarande behöver hjälp kan du också dela informationen med en supporttekniker eller produktgruppen för att få hjälp med problemet.

6.  Klicka på den **kopiera-ikonen** till höger om den **Kopieringsfel** textruta för att kopiera meddelande allt delar med en support eller produkt grupp tekniker.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Få hjälp genom att skicka meddelandeinformation till en supporttekniker

Det är mycket viktigt att du dela **allt som anges nedan** med en supporttekniker om du behöver hjälp, så att de kan hjälpa dig snabbt. Du kan göra det enkelt av **tar en skärmbild** eller genom att klicka på den **kopiera felikonen**, hittade till höger om den **Kopieringsfel** textruta.

## <a name="notification-details-explained"></a>Meddelandeinformation förklaras

Se följande beskrivningar för mer information om meddelanden.

### <a name="essential-notification-items"></a>Viktigt meddelande objekt

-   **Rubrik** – beskrivande rubrik i meddelandet
   *  Exempel – **Application proxy-inställningar**

-   **Beskrivning** – beskrivning av vad som hänt på grund av åtgärden

   *  Exempel – **intern url har angett används redan av ett annat program**

-   **Meddelande-ID** – unikt ID för meddelandet

   *  Exempel – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **ID för klientbegäran** – specifik begäran-ID som din webbläsare

   *  Exempel – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Tid UTC stämpel** – tidsstämpeln då uppstod meddelandet i UTC

   *  Exempel – **2017-03-23T19:50:43.7583681Z**

-   **Internt transaktions-ID** – internt ID vi kan använda för att söka av fel i vårt system

   *  Exempel – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** – användaren som utförde åtgärden

   *  Exempel – **tperkins@f128.info**

-   **Klient-ID** – unikt ID för den klient som användaren som utförde åtgärden var medlem av

   *  Exempel – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Objekt-ID för användaren** – unikt ID för den användare som utförde åtgärden

 *  Exempel – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Detaljerat meddelande objekt

-   **Visningsnamn** – **(kan vara tom)** en mer detaljerad visningsnamn efter fel

  *  Exempel – **Application proxy-inställningar**

-   **Status för** – specifika status för meddelandet

   *  Exempel – **misslyckades**

-   **Objekt-ID** – **(kan vara tom)** objekt-ID som åtgärden utfördes

   *  Exempel – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Information om** – detaljerad beskrivning av vad som hänt på grund av åtgärden

   *  Exempel – **intern url 'http://bing.com/' är ogiltig eftersom den redan används**

-   **Kopiera fel** – Klicka på den **kopiera-ikonen** till höger om den **Kopieringsfel** textruta för att kopiera meddelande allt delar med en grupp för stöd eller produkt 
-   tekniker

   *  Exempel ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](manage-apps/what-is-application-management.md)



