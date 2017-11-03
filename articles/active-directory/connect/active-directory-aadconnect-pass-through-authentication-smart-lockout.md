---
title: "Azure AD Connect: Direkt autentisering - Smart kontoutelåsning | Microsoft Docs"
description: "Den här artikeln beskrivs hur Azure Active Directory (AD Azure) direkt autentisering skyddar dina lokala konton från brute force-attacker lösenord i molnet."
services: active-directory
keywords: "Azure AD Connect direkt-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 771741fd7da8c9b6932851851aaca148f9596643
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>: Azure Active Directory direkt Smart Autentiseringsutelåsning

## <a name="overview"></a>Översikt

Azure AD skyddar mot brute force-attacker lösenord och förhindrar att äkta användare som låsts ute från sina Office 365 och SaaS-program. Den här funktionen kallas **Smart kontoutelåsning**, stöds när du använder direkt-autentisering som din metod för att logga in. Smart kontoutelåsning är aktiverad som standard för alla klienter och skyddar dina användarkonton hela tiden. Det finns inget behov av att den aktiveras.

Smart kontoutelåsning fungerar genom att misslyckade inloggningsförsök och efter ett visst **tröskelvärde för kontoutelåsning**början en **Utelåsningstiden**. Alla försök från angripare under kontoutelåsning avvisas. Om angrepp kvarstår inloggningsförsök den efterföljande misslyckade när den Utelåsningstiden slutar resultatet i längre varaktighet för kontoutelåsning.

>[!NOTE]
>Tröskelvärde för kontoutelåsning standardvärdet är 10 misslyckade försök och standardvärdet Utelåsningstiden är 60 sekunder.

Smart kontoutelåsning skiljer också inloggningar från äkta användare och från angripare och endast utelåses angripare i de flesta fall. Den här funktionen förhindrar att angripare medvetet låsa ute äkta användare. Vi använder tidigare inloggning beteende, användarnas enheter & webbläsare och andra signaler för att skilja mellan äkta användare och angripare. Vi ständigt förbättra våra algoritmer.

Eftersom direkt autentisering vidarebefordrar lösenord validering begäranden till din lokala Active Directory (AD), måste du hindra eventuella angripare från att låsa ute användarnas AD-konton. Eftersom du har AD kontoutelåsning principer (särskilt [ **tröskelvärde för kontoutelåsning** ](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) och [ **Återställ konto kontoutelåsning räknaren efter principer**](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx)), måste du konfigurera Azure AD tröskelvärde för kontoutelåsning och kontoutelåsning Duration-värden på lämpligt sätt för att filtrera ut attacker i molnet innan de når dina lokala AD.

>[!NOTE]
>Funktionen för Smart kontoutelåsning är ledig och är _på_ som standard för alla kunder. Ändra Azure AD tröskelvärde för kontoutelåsning och kontoutelåsning Duration-värden med hjälp av Graph API måste emellertid din klient har minst en Azure AD Premium P2-licens. Du behöver en Azure AD Premium P2-licens _per användare_ att hämta funktionen smarta kontoutelåsning med direkt-autentisering.

Att säkerställa att dina användare lokala AD-konton är väl skyddade, måste du se till att:

1.  Tröskelvärde för kontoutelåsning för Azure AD är _mindre_ än Annonsens tröskelvärde för kontoutelåsning. Vi rekommenderar att du har angett värden så att Annonsens tröskelvärde för kontoutelåsning är minst två eller tre gånger tröskelvärde för kontoutelåsning för Azure AD.
2.  Azure AD-Utelåsningstiden (som visas i sekunder) är _längre_ än Annonsens Återställ konto kontoutelåsning räknaren efter (angiven i minuter).

>[!IMPORTANT]
>En administratör kan inte låser upp användarens molnet konton om de har låsts ute av Smart kontoutelåsning kapaciteten. De måste vänta tills Utelåsningstiden ska upphöra att gälla.

## <a name="verify-your-ad-account-lockout-policies"></a>Kontrollera dina principer för kontoutelåsning för AD

Använd följande instruktioner för att verifiera dina principer för kontoutelåsning AD:

1.  Öppna verktyget hantering av Grupprincip.
2.  Redigera en grupprincip som tillämpas på alla användare, till exempel domänens standardprincip.
3.  Gå till datorn Datorkonfiguration\Principer\Windows datorkonfiguration\windowsinställningar\säkerhetsinställningar\avancerad kontoprinciper för kontoutelåsning.
4.  Kontrollera dina tröskelvärde för kontoutelåsning och återställa kontot kontoutelåsning räknaren efter värden.

![Principer för kontoutelåsning för AD](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-needs-premium-license"></a>Använd Graph API för att hantera din klient Smart kontoutelåsning värden (måste Premium-licens)

>[!IMPORTANT]
>Ändra Azure AD tröskelvärde för kontoutelåsning och kontoutelåsning Duration-värden med hjälp av Graph API är en Azure AD Premium P2-funktion. Det måste också att du kan vara en Global administratör för din klient.

Du kan använda [diagram Explorer](https://developer.microsoft.com/graph/graph-explorer) kan läsa, ange och uppdatera Azure AD Smart kontoutelåsning värden. Men du kan också göra dessa åtgärder via programmering.

### <a name="read-smart-lockout-values"></a>Läs Smart kontoutelåsning värden

Följ dessa steg om du vill läsa din klient Smart kontoutelåsning värden:

1. Logga in på diagrammet Explorer som Global administratör för din klient. Om du uppmanas du bevilja åtkomst för de begärda behörigheterna.
2. Klicka på ”Ändra behörigheter” och väljer ”Directory.ReadWrite.All”-behörighet.
3. Konfigurera Graph API-begäran på följande sätt: ange version till ”BETAVERSION” typ ”GET” och URL: en till `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Klicka på ”Kör fråga” om du vill se din klient Smart kontoutelåsning värden. Om du inte har angett din klient värden innan ser du en tom uppsättning.

### <a name="set-smart-lockout-values"></a>Ange värden för Smart kontoutelåsning

Följ dessa steg om du vill ange värden för din klient Smart kontoutelåsning (för första gången endast):

1. Logga in på diagrammet Explorer som Global administratör för din klient. Om du uppmanas du bevilja åtkomst för de begärda behörigheterna.
2. Klicka på ”Ändra behörigheter” och väljer ”Directory.ReadWrite.All”-behörighet.
3. Konfigurera Graph API-begäran på följande sätt: ange version till ”BETA”, begära att ”POST” och URL: en till `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Kopiera och klistra in följande JSON-begäran i fältet ”Begärandetexten”.
5. Klicka på ”Kör frågan” för att ange värden för din klient Smart kontoutelåsning.

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
>Om du inte använder dem, kan du lämna den **BannedPasswordList** och **EnableBannedPasswordCheck** värden som tom (””) och ”false” respektive.

Kontrollera att du har angett din klient Smart kontoutelåsning värden korrekt med [här](#read-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Uppdatera Smart kontoutelåsning värden

Följ dessa steg för att uppdatera din klient Smart kontoutelåsning värden (om du redan har angett dem innan):

1. Logga in på diagrammet Explorer som Global administratör för din klient. Om du uppmanas du bevilja åtkomst för de begärda behörigheterna.
2. Klicka på ”Ändra behörigheter” och väljer ”Directory.ReadWrite.All”-behörighet.
3. [Följ dessa steg om du vill läsa din klient Smart kontoutelåsning värden](#read-smart-lockout-values). Kopiera den `id` värdet (ett GUID) för objektet med ”visningsnamn” som ”PasswordRuleSettings”.
4. Konfigurera Graph API-begäran på följande sätt: ange version till ”BETAVERSION” typ ”uppdatera” och URL: en till `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>` -Använd GUID från steg3 för `<id>`.
5. Kopiera och klistra in följande JSON-begäran i fältet ”Begärandetexten”. Ändra värdena för Smart kontoutelåsning efter behov.
6. Klicka på ”Kör fråga” för att uppdatera din klient Smart kontoutelåsning värden.

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

Kontrollera att du har uppdaterat din klient Smart kontoutelåsning värden korrekt med [här](#read-smart-lockout-values).

## <a name="next-steps"></a>Nästa steg
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för arkivering av nya funktioner som efterfrågas.
