---
title: Problem med att lägga till ett Azure AD Gallery-program | Microsoft-dokument
description: Förstå de vanliga problem som personer ställs inför när du lägger till Azure AD Gallery-program och vad du kan göra för att lösa dem
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
ms.date: 09/11/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2b42880f99f3e87d75854166047896860f9eb14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784429"
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>Problem med att lägga till ett Azure AD Gallery-program

Den här artikeln hjälper dig att förstå de vanliga problem som personer ställs inför när du lägger till Azure AD Gallery-program och vad du kan göra för att lösa dem.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Jag klickade på "lägg till"-knappen och min ansökan tog lång tid att visas

Under vissa omständigheter kan det ta 1-2 minuter (och ibland längre) för ett program att visas efter att ha lagt till den i din katalog. Även om detta inte är den normala förväntade prestanda, kan du se programmet tillägg pågår genom att klicka på **ikonen Meddelanden** (klockan) längst upp till höger i [Azure-portalen](https://portal.azure.com/) och letar efter en **Pågår** eller **slutförd** anmälan märkt **Lägga till programmet.**

Om ditt program aldrig läggs till, eller om du stöter på ett fel när du klickar på knappen **Lägg till,** visas ett **meddelande** i ett **feltillstånd.** Om du vill ha mer information om felet för att lära dig mer eller dela med en supporttekniker kan du se mer information om felet genom att följa stegen i avsnittet [Hur du ser information om en portalavisering.](#how-to-see-the-details-of-a-portal-notification)

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Jag klickade på "lägg till"-knappen och min ansökan visades inte

Ibland, på grund av tillfälliga problem, nätverksproblem eller ett fel, misslyckas det att lägga till ett program. Du kan se detta händer när du klickar på ikonen **Meddelanden** (klockan) längst upp till höger i Azure-portalen och du ser en röd (!) ikon bredvid din **Lägga till programavisering.** Detta indikerar att det uppstod ett fel när programmet skapades.

Om du stöter på ett fel när du klickar på knappen **Lägg till** visas ett **meddelande** i ett **feltillstånd.** Om du vill ha mer information om felet för att lära dig mer eller dela med en supporttekniker kan du se mer information om felet genom att följa stegen i avsnittet [Hur du ser information om en portalavisering.](#how-to-see-the-details-of-a-portal-notification)

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Jag vet inte hur jag ska konfigurera min ansökan när jag har lagt till den

Om du behöver hjälp med att lära dig mer om program är [listan över självstudier om hur du integrerar SaaS-appar med Azure Active Directory-artikeln](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) ett bra ställe att börja på.

Utöver detta hjälper [Azure AD Applications-dokumentbiblioteket](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) dig att lära dig mer om enkel inloggning med Azure AD och hur det fungerar.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Så här visar du information om en portalavisering

Du kan se information om alla portalmeddelanden genom att följa stegen nedan:

1.  Välj ikonen **Meddelanden** (klockan) längst upp till höger på Azure-portalen

2.  Markera ett meddelande i **feltillstånd** (de med en röd (!) bredvid dem).

    >[!NOTE]
    >Du kan inte klicka på meddelanden i tillståndet **Lyckad** eller **Pågår.**
    >
    >

4.  Använd informationen under **Meddelandeinformation** för att förstå mer information om problemet.

5.  Om du fortfarande behöver hjälp kan du också dela den här informationen med en supporttekniker eller produktgruppen för att få hjälp med ditt problem.

6.  Klicka på **copy** **kopieringsikonen** till höger om textrutan **Kopiera fel** om du vill kopiera alla meddelandeinformationer som du vill dela med en support- eller produktgruppstekniker.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Så här får du hjälp genom att skicka meddelandeinformation till en supporttekniker

Det är mycket viktigt att du delar **alla detaljer som anges nedan** med en supporttekniker om du behöver hjälp, så att de kan hjälpa dig snabbt. Du kan enkelt göra detta genom **att ta en skärmdump,** eller genom att klicka på **ikonen Kopiera fel**, finns till höger om **textrutan Kopiera fel.**

## <a name="notification-details-explained"></a>Information om meddelanden förklaras

Mer information om meddelandena finns i följande beskrivningar.

### <a name="essential-notification-items"></a>Viktiga meddelandeobjekt

- **Titel** – den beskrivande titeln på anmälan

  * Exempel – **Proxyinställningar för program**

- **Beskrivning** – beskrivningen av vad som hände som ett resultat av operationen

  -   Exempel – **Intern url som anges används redan av ett annat program**

- **Meddelande-ID** – det unika ID:t för meddelandet

  -   Exempel – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

- **Klientbegärande-ID** – det specifika id-förfrågan som görs av din webbläsare

  -   Exempel – **302fd775-3329-4670-a9f3-bea37004f0bc**

- **Tidsstämpel UTC** – den tidsstämpel under vilken anmälan inträffade, i UTC

  -   Exempel – **2017-03-23T19:50:43.7583681Z**

- **Internt transaktions-ID** – det interna ID som vi kan använda för att slå upp felet i våra system

  -   Exempel – **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN** – användaren som utförde åtgärden

  -   Exempel – **tperkins\@f128.info**

- **Klient-ID** – det unika ID:t för klienten som användaren som utförde åtgärden var medlem i

  -   Exempel – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

- **Användarobjekt-ID** – det unika ID:t för den användare som utförde åtgärden

  -   Exempel – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Detaljerade meddelandeobjekt

-   **Visningsnamn** – **(kan vara tomt)** ett mer detaljerat visningsnamn för felet

    -   Exempel – **Proxyinställningar för program**

-   **Status** – anmälans särskilda status

    -   Exempel – **misslyckades**

-   **Objekt-ID** – **(kan vara tomt)** det objekt-ID som åtgärden utfördes mot

    -   Exempel – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Detaljer** – den detaljerade beskrivningen av vad som inträffade till följd av operationen

    -   Exempel – **Intern url `https://bing.com/` är ogiltig eftersom den redan används**

-   **Kopieringsfel** – Klicka på **kopieringsikonen** till höger om textrutan **Kopiera fel** för att kopiera alla meddelandeinformationer att dela med en support eller produktgrupp 
-   tekniker

    -   Exempel```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

