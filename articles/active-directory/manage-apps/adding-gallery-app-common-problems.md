---
title: Problem med att lägga till ett Azure AD Gallery-program | Microsoft Docs
description: Förstå vanliga problem när du lägger till Azure AD Gallery-program och vad du kan göra för att lösa dem
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/11/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4387a3ca02297311cd9ab025c315ca3324cbbbe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760413"
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>Problem med att lägga till ett Azure AD Gallery-program

Den här artikeln hjälper dig att förstå vanliga problem när du lägger till Azure AD Gallery-program och vad du kan göra för att lösa dem.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Jag klickade på knappen Lägg till och det tog lång tid att visa programmet

Under vissa omständigheter kan det ta 1-2 minuter (och ibland längre) för ett program att visas när du har lagt till det i din katalog. Även om det här inte är den normala förväntade prestandan kan du se att programtillägget pågår genom att klicka på **meddelande** ikonen (klock tiden) längst upp till höger i [Azure Portal](https://portal.azure.com/) och leta efter en **pågående** eller **slutförd** avisering med etiketten **att lägga till programmet.**

Om programmet aldrig läggs till eller om du stöter på ett fel när du klickar på knappen **Lägg till** visas ett **meddelande** i **fel** tillstånd. Om du vill ha mer information om felet för att lära dig mer eller dela med en support tekniker kan du se mer information om felet genom att följa stegen i avsnittet [om att se information om ett Portal meddelande](#how-to-see-the-details-of-a-portal-notification) .

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Jag klickade på knappen Lägg till och mitt program visades inte

Ibland kan det hända att det inte går att lägga till ett program på grund av tillfälliga problem, nätverks problem eller fel. Du kan se att detta inträffar när du klickar på ikonen **meddelanden** (klock ikonen) i det övre högra hörnet av Azure Portal och du ser en röd ikon (!) bredvid aviseringen för **att lägga till program** . Detta tyder på ett fel när programmet skapades.

Om du stöter på ett fel när du klickar på knappen **Lägg till** visas ett **meddelande** i **fel** tillstånd. Om du vill ha mer information om felet för att lära dig mer eller dela med en support tekniker kan du se mer information om felet genom att följa stegen i avsnittet [om att se information om ett Portal meddelande](#how-to-see-the-details-of-a-portal-notification) .

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Jag vet inte hur jag ska konfigurera mitt program när jag har lagt till det

Om du behöver hjälp med att lära dig om program är [listan med självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) artikel ett bra ställe att börja.

Förutom detta kan [Azure AD-programbiblioteket för program](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) hjälpa dig att lära dig mer om enkel inloggning med Azure AD och hur det fungerar.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Så här visar du information om ett Portal meddelande

Du kan se information om alla Portal meddelanden genom att följa stegen nedan:

1.  Välj **meddelande** ikonen (klock ikonen) i det övre högra hörnet i Azure Portal

2.  Välj aviseringar i ett **fel** tillstånd (de med röda (!) bredvid dem).

    >[!NOTE]
    >Det går inte att klicka på aviseringar i ett tillstånd som **lyckats** eller **pågår** .
    >
    >

4.  Använd informationen under **meddelande informationen** om du vill veta mer om problemet.

5.  Om du fortfarande behöver hjälp kan du också dela den här informationen med en support tekniker eller produkt gruppen för att få hjälp med problemet.

6.  Klicka på **kopierings** **ikonen** till höger om text rutan **Kopiera fel** om du vill kopiera all meddelande information för att dela med en support-eller produkt grupps tekniker.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Så här får du hjälp genom att skicka meddelande information till en support tekniker

Det är mycket viktigt att du delar **alla uppgifter som anges nedan** med en support tekniker om du behöver hjälp, så att de kan hjälpa dig snabbt. Du kan enkelt göra detta genom att **ta en skärm bild** eller genom att klicka på **ikonen Kopiera fel**som finns till höger om text rutan **Kopiera fel** .

## <a name="notification-details-explained"></a>Förklarad meddelande information

Se följande beskrivningar för mer information om aviseringarna.

### <a name="essential-notification-items"></a>Viktiga meddelande objekt

- **Title** – meddelandets beskrivande rubrik

  * Exempel – **Inställningar för programproxy**

- **Beskrivning** – en beskrivning av vad som har uppstått till följd av åtgärden

  -   Exempel – **en intern URL som anges används redan av ett annat program**

- **Meddelande-ID** – meddelandets unika ID

  -   Exempel – **clientNotification – 2adbfc06-2073-4678-a69f-7eb78d96b068**

- **ID för klientbegäran** – det ID för begäran som gjorts av webbläsaren

  -   Exempel – **302fd775-3329-4670-a9f3-bea37004f0bc**

- **Tidsstämpel UTC** – tidsstämpeln då meddelandet inträffade, i UTC-tid

  -   Exempel – **2017-03-23T19:50:43.7583681 z**

- **Internt transaktions-ID** – det interna ID som vi kan använda för att leta upp felet i våra system

  -   Exempel – **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN** – användaren som utförde åtgärden

  -   Exempel – **tperkins \@ f128.info**

- **Klient-ID** – det unika ID: t för den klient som den användare som utförde åtgärden var medlem i

  -   Exempel – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

- **Användar objekt-ID** – det unika ID: t för den användare som utförde åtgärden

  -   Exempel – **17f84be4-51f8-483a-B533-383791227a99**

### <a name="detailed-notification-items"></a>Detaljerade meddelande objekt

-   **Visnings namn** – **(kan vara tomt)** ett mer detaljerat visnings namn för felet

    -   Exempel – **Inställningar för programproxy**

-   **Status** – meddelandets speciella status

    -   Exempel – **misslyckades**

-   **Objekt-ID** – **(kan vara tomt)** det objekt-ID som åtgärden utfördes mot

    -   Exempel – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Information** – en detaljerad beskrivning av vad som har uppstått till följd av åtgärden

    -   Exempel – **intern URL `https://bing.com/` är ogiltig eftersom den redan används**

-   **Kopierings fel** – Klicka på **kopierings ikonen** till höger om text rutan **Kopiera fel** för att kopiera all meddelande information för att dela med en support-eller produkt grupp 
-   tekniker

    -   Exempel```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

