---
title: Lösenordslös inloggning med Microsoft Authenticator-appen - Azure Active Directory
description: Aktivera lösenordslös inloggning till Azure AD med hjälp av Microsoft Authenticator-appen (förhandsversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c684d6f1fbd8128ae020b6fd29da928b286aa18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126697"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Aktivera lösenordslös inloggning med Microsoft Authenticator-appen (förhandsgranskning)

Microsoft Authenticator-appen kan användas för att logga in på alla Azure AD-konton utan att använda ett lösenord. I likhet med tekniken i [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification)använder Microsoft Authenticator nyckelbaserad autentisering för att aktivera en användarautentiseringsautentisering som är knuten till en enhet och använder en biometrisk eller PIN-kod. Den här autentiseringsmetoden kan användas på alla enhetsplattformar, inklusive mobila enheter, och med alla appar eller webbplatser som integreras med Microsofts autentiseringsbibliotek. 

![Exempel på en webbläsarloggning som ber användaren att godkänna inloggningen](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

I stället för att se en fråga om ett lösenord efter att ha angett ett användarnamn visas ett meddelande om att en person som har aktiverat telefon inloggning från Microsoft Authenticator-appen kommer att se ett meddelande som talar om för dem att trycka på ett nummer i sin app. I appen måste användaren matcha numret, välja Godkänn och sedan ange sin PIN-kod eller biometriska, sedan slutförs autentiseringen.

> [!NOTE]
> Den här funktionen har funnits i Microsoft Authenticator-appen sedan mars 2017, så det finns en möjlighet att när principen är aktiverad för en katalog kan användare stöta på det här flödet omedelbart och se ett felmeddelande om de inte har aktiverats av principen. Var medveten om och förbered användarna för den här ändringen.

## <a name="prerequisites"></a>Krav

- Azure MultiFaktor-autentisering, med push-meddelanden som tillåts som en verifieringsmetod 
- Den senaste versionen av Microsoft Authenticator installerad på enheter som kör iOS 8.0 eller senare, eller Android 6.0 eller senare.

> [!NOTE]
> Om du aktiverade den tidigare förhandsversionen av Microsoft Authenticator-appenlös inloggning med Azure AD PowerShell aktiverades den för hela katalogen. Om du aktiverar med den här nya metoden ersätts PowerShell-principen. Vi rekommenderar att du aktiverar för alla användare i din klient via de nya autentiseringsmetoderna, annars kan användare som inte finns i den nya principen inte längre logga in lösenordslöst. 

## <a name="enable-passwordless-authentication-methods"></a>Aktivera lösenordslösa autentiseringsmetoder

### <a name="enable-the-combined-registration-experience"></a>Aktivera den kombinerade registreringsupplevelsen

Registreringsfunktioner för lösenordslösa autentiseringsmetoder är beroende av den kombinerade registreringsförhandsgranskningen. Följ stegen i artikeln [Aktivera kombinerad registrering av säkerhetsinformation (förhandsversion)](howto-registration-mfa-sspr-combined.md)för att aktivera den kombinerade registreringsförhandsgranskningen.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Aktivera lösenordslös telefon inloggning autentiseringsmetoder

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Sök efter och välj *Azure Active Directory*. Välj princip**för autentiseringsmetoder för** >  **säkerhetsautentisering** > **(förhandsgranskning)**
1. Under **Lösenordslös telefonsignering**väljer du följande alternativ
   1. **Aktivera** - Ja eller Nej
   1. **Mål** - Alla användare eller Välj användare
1. **Spara** för att ange den nya principen

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registrering och hantering av Microsoft Authenticator-appen

1. Bläddra till[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Logga in om det inte redan är
1. Lägga till en autentiserarapp genom att klicka på **Lägg till metod,** välja **Autentiseringsapp**och klicka på **Lägg till**
1. Följ instruktionerna för att installera och konfigurera Microsoft Authenticator-appen på enheten
1. Klicka på **Klar** för att slutföra flödet för konfiguration av Autentiseringsappar. 
1. I **Microsoft Authenticator**väljer du **Aktivera telefon inloggning** på den rullgardinsmenyn för kontot
1. Följ instruktionerna i appen för att slutföra registreringen för lösenordslös telefoninsignning. 

Organisationer kan peka sina användare på artikeln [Logga in med telefonen, inte ditt lösenord](../user-help/microsoft-authenticator-app-phone-signin-faq.md) för ytterligare hjälp med att konfigurera i Microsoft Authenticator-appen och aktivera telefon inloggning. För att kunna tillämpa dessa inställningar kan du behöva logga ut och logga in på klienten igen. 

## <a name="sign-in-with-passwordless-credential"></a>Logga in med lösenordslös autentiseringsuppgifter

För offentlig förhandsversion finns det inget sätt att tvinga användare att skapa eller använda den här nya autentiseringsversionen. En användare kommer bara att stöta på lösenordslös inloggning när en administratör har aktiverat sin klientorganisation **och** användaren har uppdaterat sin Microsoft Authenticator-app för att aktivera telefonloggning.

När du har skrivit ditt användarnamn på webben och valt **Nästa**visas ett nummer för användarna och uppmanas att välja lämpligt nummer för att autentisera i stället för att använda lösenordet. 

![Exempel på en inloggning i webbläsaren med hjälp av Microsoft Authenticator-appen](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Kända problem

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>Användaren är inte aktiverad av principen men har fortfarande lösenordslös telefon inloggningsmetod i Microsoft Authenticator

Det är möjligt att en användare någon gång har skapat en lösenordslös telefonsigneringsautentiseringsautentisering i sin nuvarande Microsoft Authenticator-app eller på en tidigare enhet. När en administratör aktiverar principen för autentiseringsmetod för lösenordslös telefonloggning börjar alla användare med en registrerad autentiseringsuppgifter uppleva den nya inloggningsprompten, oavsett om de har aktiverats för att använda principen eller inte. Om användaren inte har tillåtits använda autentiseringsuppgifterna efter princip visas ett fel när autentiseringsflödet har slutförts. 

Administratören kan välja att aktivera användaren för att använda lösenordslös telefon inloggning, eller användaren måste ta bort metoden. Om användaren inte längre har den registrerade [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) enheten kan de gå till och ta bort den. Om de fortfarande använder Autentiseraren för MFA kan de välja **Inaktivera telefon inloggning** från Microsoft Authenticator.  

### <a name="ad-fs-integration"></a>AD FS-integrering

När en användare har aktiverat Microsoft Authenticator lösenordslös autentiseringsuppgifter, kommer autentisering för den användaren alltid standard att skicka ett meddelande för godkännande. Den här logiken förhindrar att användare i en hybridklient dirigeras till ADFS för inloggningsverifiering utan att användaren tar ytterligare ett steg för att klicka på "Använd ditt lösenord i stället". Den här processen kommer också att kringgå alla lokala principer för villkorlig åtkomst och direktautentiseringsflöden. 

Om en användare har en obesvarad lösenordslös telefonloggningsverifiering som väntar och försöker logga in igen kan användaren tas till ADFS för att ange ett lösenord i stället.  

### <a name="azure-mfa-server"></a>Azure MFA-server

Slutanvändare som är aktiverade för MFA via en organisations lokala Azure MFA-server kan fortfarande skapa och använda en enda lösenordslös telefoninknägningsautentisering. Om användaren försöker uppgradera flera installationer (5+) av Microsoft Authenticator med autentiseringsuppgifterna kan den här ändringen leda till ett fel.  

### <a name="device-registration"></a>Enhetsregistrering

En av förutsättningarna för att skapa den här nya starka autentiseringsenheten är att enheten, där Microsoft Authenticator-appen är installerad, också måste registreras i Azure AD-klienten till en enskild användare. På grund av aktuella enhetsregistreringsbegränsningar kan en enhet endast registreras i en enda klient. Den här gränsen innebär att endast ett arbets- eller skolkonto i Microsoft Authenticator-appen kan aktiveras för telefon inloggning.

### <a name="intune-mobile-application-management"></a>Hantering av mobilapplikationer 

Slutanvändare som omfattas av en policy som kräver hantering av mobilappar (MAM) kan inte registrera den lösenordslösa autentiseringsuppgifterna i Microsoft Authenticator-appen. 

> [!NOTE]
> Enhetsregistrering är inte samma sak som enhetshantering eller "MDM". Den associerar bara ett enhets-ID och ett användar-ID tillsammans i Azure AD-katalogen.  

## <a name="next-steps"></a>Nästa steg

[Vad är lösenordsfritt?](concept-authentication-passwordless.md)

[Läs mer om enhetsregistrering](../devices/overview.md#getting-devices-in-azure-ad)

[Lär dig mer om Azure Multi Factor-autentisering](../authentication/howto-mfa-getstarted.md)
