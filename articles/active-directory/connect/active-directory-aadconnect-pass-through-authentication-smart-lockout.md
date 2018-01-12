---
title: "Azure AD Connect: Direkt autentisering - Smart kontoutelåsning | Microsoft Docs"
description: "Den här artikeln beskrivs hur Azure Active Directory (AD Azure) direkt autentisering skyddar dina lokala konton från brute force-attacker lösenord i molnet"
services: active-directory
keywords: "Azure AD Connect direkt-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: billmath
ms.openlocfilehash: fc46fe1d68538757ba5a8c5aa1acb4b51f8a171b
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>: Azure Active Directory direkt Smart Autentiseringsutelåsning

## <a name="overview"></a>Översikt

Azure Active Directory (AD Azure) skyddar mot lösenord brute force-attacker och förhindrar att äkta användare som låsts ute från sina Office 365 och SaaS-program. Den här funktionen kallas *Smart kontoutelåsning*, stöds när du använder direkt-autentisering som din metod för att logga in. Smart kontoutelåsning är aktiverad som standard för alla klienter och det skyddar dina användarkonton kontinuerligt.

Smart kontoutelåsning håller reda på misslyckade inloggningsförsök. Efter en viss *tröskelvärde för kontoutelåsning*, startar en *utelåsningstiden*. Smart kontoutelåsning avvisar försök att logga in från angripare under kontoutelåsning varaktighet. Om angrepp kvarstår inloggningsförsök efterföljande misslyckade när utelåsningstiden slutar resultatet med längre varaktighet för kontoutelåsning.

>[!NOTE]
>Kontoutelåsning standardtröskelvärdet är 10 misslyckade försök. Standardlängden för kontoutelåsning är 60 sekunder.

Smart kontoutelåsning skiljer också inloggningar från äkta användare och inloggningar från angripare. I de flesta fall låses ut endast angripare. Den här funktionen förhindrar att angripare medvetet låsa ute äkta användare. Smart kontoutelåsning använder tidigare inloggning beteende, användarnas enheter och webbläsare och andra signalerar att skilja mellan äkta användare och angripare. Algoritmer som förbättras kontinuerligt.

Direkt-autentisering vidarebefordrar begäranden för verifiering av lösenord till lokala Active Directory, så du behöver förhindra angripare från att låsa ute användarnas Active Directory-konton. Active Directory har sin egen kontoutelåsningsprinciper specifikt [tröskelvärde för kontoutelåsning](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) och [Återställ räknaren för kontoutelåsning efter](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx) principer. Konfigurera Azure AD kontoutelåsning tröskelvärde och kontoutelåsning varaktighetsvärden på lämpligt sätt för att filtrera ut attacker i molnet innan de når lokala Active Directory.

>[!NOTE]
>>Funktionen för Smart kontoutelåsning är ledig och är _på_ som standard för alla kunder. Ändra Azure AD tröskelvärde för kontoutelåsning och kontoutelåsning Duration-värden med hjälp av Graph API måste emellertid din klient som ska aktiveras för Azure AD Premium P2. 

För att säkerställa att skyddas också användarnas lokala Active Directory-konton, måste du se till att:

   * Azure AD-utelåsningströskeln är _mindre_ än tröskelvärde för kontoutelåsning Active Directory. Ange värden så att Active Directory tröskelvärde för kontoutelåsning är minst två eller tre gånger längre än utelåsningströskeln Azure AD.
   * Azure AD-utelåsningstiden (som visas i sekunder) är _längre_ än Active Directory Återställ räknaren för kontoutelåsning efter längd (angiven i minuter).

>[!IMPORTANT]
>En administratör kan inte låser upp användarens molnet konton om de har låsts ute av Smart kontoutelåsning kapaciteten. Administratören måste vänta tills utelåsningstiden ska upphöra att gälla.

## <a name="verify-your-active-directory-account-lockout-policies"></a>Kontrollera principer för kontoutelåsning din Active Directory

Använd följande instruktioner för att verifiera principer för kontoutelåsning din Active Directory:

1.  Öppna verktyget hantering av Grupprincip.
2.  Redigera en grupprincip som tillämpas på alla användare, till exempel den **Standarddomänprincip**.
3.  Bläddra till **Datorkonfiguration** > **principer** > **Windowsinställningar** > **säkerhetsinställningar**   >  **Konto principer** > **kontoprincip kontoutelåsning**.
4.  Kontrollera din **tröskelvärde för kontoutelåsning** och **Återställ räknaren för kontoutelåsning efter** värden.

![Active Directory principer för kontoutelåsning](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-requires-a-premium-license"></a>Använd Graph API för att hantera din klient Smart kontoutelåsning värden (kräver en Premium-licens)

>[!IMPORTANT]
>Ändra Azure AD kontoutelåsning tröskelvärde och Utelåsningens varaktighet värdena med hjälp av Graph API är en Azure AD Premium P2-funktion. Det måste också att du kan vara en global administratör för din klient.

Du kan använda [diagram Explorer](https://developer.microsoft.com/graph/graph-explorer) kan läsa, ange och uppdatera Azure AD Smart kontoutelåsning-värden. Du kan också göra dessa åtgärder via programmering.

### <a name="view-smart-lockout-values"></a>Visa Smart kontoutelåsning värden

Följ dessa steg om du vill visa din klient Smart kontoutelåsning värden:

1. Logga in på diagrammet Explorer som global administratör för din klient. Om du uppmanas du bevilja åtkomst för de begärda behörigheterna.
2. Välj **ändringsbehörighet**, och välj sedan den **Directory.ReadWrite.All** behörighet.
3. Konfigurera Graph API-begäran på följande sätt: ange version **BETA**, typ av begäran till **hämta**, och URL: en till `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Välj **Kör fråga** se din klient Smart kontoutelåsning. Om du inte har angett din klient värden innan ser du en tom uppsättning.

### <a name="set-smart-lockout-values"></a>Ange värden för Smart kontoutelåsning

Följ dessa steg om du vill ange värden för din klient Smart kontoutelåsning (krävs för första gången):

1. Logga in på diagrammet Explorer som global administratör för din klient. Om du uppmanas du bevilja åtkomst för de begärda behörigheterna.
2. Välj **ändringsbehörighet**, och välj sedan den **Directory.ReadWrite.All** behörighet.
3. Konfigurera Graph API-begäran på följande sätt: ange version **BETA**, typ av begäran till **POST**, och URL: en till `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Kopiera och klistra in följande JSON-begäran till den **Begärandetexten** fältet.
5. Välj **Kör fråga** att ställa in din klient Smart kontoutelåsning värden.

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>Om du inte använder dem, kan du lämna den **BannedPasswordList** och **EnableBannedPasswordCheck** värden som tom (**””**) och **FALSKT** respektive.

Kontrollera att du har angett din klient Smart kontoutelåsning värden på rätt sätt med hjälp av stegen i [visa Smart kontoutelåsning värden](#view-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Uppdatera Smart kontoutelåsning värden

Följ dessa steg för att uppdatera din klient Smart kontoutelåsning värden (om du har angett dem innan):

1. Logga in på diagrammet Explorer som global administratör för din klient. Om du uppmanas du bevilja åtkomst för de begärda behörigheterna.
2. Välj **ändringsbehörighet**, och välj sedan den **Directory.ReadWrite.All** behörighet.
3. [Följ dessa steg om du vill visa värden för din klient Smart kontoutelåsning](#view-smart-lockout-values). Kopiera den `id` värdet (ett GUID) för objektet med **displayName** som **PasswordRuleSettings**.
4. Konfigurera Graph API-begäran på följande sätt: ange version **BETA**, typ av begäran till **korrigering**, och URL: en till `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>`. Använd GUID från steg 3 för `<id>`.
5. Kopiera och klistra in följande JSON-begäran till den **Begärandetexten** fältet. Ändra värdena för Smart kontoutelåsning efter behov.
6. Välj **Kör fråga** att uppdatera din klient Smart kontoutelåsning värden.

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

Kontrollera att du har uppdaterat din klient Smart kontoutelåsning värden på rätt sätt med hjälp av stegen i [visa Smart kontoutelåsning värden](#view-smart-lockout-values).

## <a name="next-steps"></a>Nästa steg
[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): använda Azure Active Directory-forumet till filen nya funktioner som efterfrågas.
