---
title: Logga in med autentisering med ett arbets- eller skolkonto - Azure AD
description: Läs om hur du loggar in på ditt arbets- eller skolkonto med hjälp av de olika tvåfaktorsverifieringsmetoderna.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: 33cf9e284d2206ea497af7a5da7c3cf4a890cc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064095"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Logga in på ditt arbets- eller skolkonto med din tvåfaktorsverifieringsmetod

> [!NOTE]
> Syftet med den här artikeln är att gå igenom en typisk inloggningsupplevelse. Information om hur du loggar in eller felsöker problem finns i [Ha problem med Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Vad blir din inloggningsupplevelse?
Din inloggningsupplevelse varierar beroende på vad du väljer att använda som din andra faktor: ett telefonsamtal, en autentiseringsapp eller sms. Välj det alternativ som bäst beskriver vad du gör:

| Hur loggar du in? |
| --- |
| [Med ett telefonsamtal till min mobil eller kontorstelefon](#signing-in-with-a-phone-call) |
| [Med ett sms till min mobiltelefon](#signing-in-with-a-text-message)
| [Med meddelanden från Microsoft Authenticator-appen](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Med verifieringskoder från Microsoft Authenticator-appen |
| [Med en alternativ metod, eftersom jag inte kan använda min föredragna metod just nu](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Logga in med ett telefonsamtal
Följande information beskriver tvåstegsverifieringsupplevelsen med ett samtal till din mobil- eller kontorstelefon.

1. Logga in på ett program eller en tjänst som Office 365 med ditt användarnamn och lösenord.  
2. Microsoft ringer dig.  
3. Svara i telefonen och tryck på # knappen.  

## <a name="signing-in-with-a-text-message"></a>Logga in med ett textmeddelande
Följande information beskriver tvåstegsverifieringsupplevelsen med ett sms till mobiltelefonen:

1. Logga in på ett program eller en tjänst som Office 365 med ditt användarnamn och lösenord.
2. Microsoft skickar ett textmeddelande som innehåller en nummerkod.
3. Ange koden i rutan som finns på inloggningssidan.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Logga in med Microsoft Authenticator-appen
Följande information beskriver upplevelsen av att använda Microsoft Authenticator-appen för tvåstegsverifieringar. Det finns två olika sätt att använda appen. Du kan få push-meddelanden på din enhet eller öppna appen för att få en verifieringskod.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Så här loggar du in med ett meddelande från Microsoft Authenticator-appen
1. Logga in på ett program eller en tjänst som Office 365 med ditt användarnamn och lösenord.
2. Microsoft skickar ett meddelande till Microsoft Authenticator-appen på enheten.

   ![Microsoft skickar meddelande](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Öppna meddelandet på telefonen och välj **verifiera nyckeln.** Om ditt företag behöver en PIN-kod anger du den här.
4. Du borde nu vara inloggad.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Så här loggar du in med en verifieringskod med Microsoft Authenticator-appen

Om du använder Microsoft Authenticator-appen för att hämta verifieringskoder visas ett nummer under ditt kontonamn när du öppnar appen. Det här numret ändras var 30:e sekund så att du inte använder samma nummer två gånger. När du blir tillfrågad om en verifieringskod öppnar du appen och använder det nummer som för närvarande visas.

1. Logga in på ett program eller en tjänst som Office 365 med ditt användarnamn och lösenord.
2. Microsoft uppmanar dig att ange en verifieringskod.

   ![Ange verifieringskod](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Öppna Microsoft Authenticator-appen på telefonen och ange koden i rutan där du loggar in.

## <a name="signing-in-with-an-alternate-method"></a>Logga in med en alternativ metod
Ibland har du inte den telefon eller enhet som du ställer in som önskad verifieringsmetod. Det är därför vi rekommenderar att du ställer in säkerhetskopieringsmetoder för ditt konto. I följande avsnitt visas hur du loggar in med en alternativ metod när din primära metod kanske inte är tillgänglig.

1. Logga in på ett program eller en tjänst som Office 365 med ditt användarnamn och lösenord.
2. Välj **Använd ett annat verifieringsalternativ**. Du ser olika verifieringsalternativ baserat på hur många du har ställt in.
3. Välj en alternativ metod och logga in.

   ![Använd alternativ metod](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Nästa steg
- Om du har problem med att logga in med tvåstegsverifiering kan du få mer information om [hur du har problem med Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

- Läs om hur du [hanterar verifieringsinställningarna i två steg](multi-factor-authentication-end-user-manage-settings.md).

- Ta reda på hur [du kommer igång med Microsoft Authenticator-appen](user-help-auth-app-download-install.md) så att du kan använda aviseringar för att logga in i stället för sms och telefonsamtal.
