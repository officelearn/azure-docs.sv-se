---
title: "Felsökning för Azure Active Directory-åtkomstproblem | Microsoft Docs"
description: "Lär dig steg som du kan vidta för att lösa åtkomstproblem med din organisations onlineresurser."
services: active-directory
keywords: "enhetsbaserad villkorlig åtkomst, enhetsregistrering, aktivera enhetsregistrering, enhetsregistrering och MDM"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/24/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: fbabf6f2e1e588ba509c4da84ab1700b1b5d4f87
ms.openlocfilehash: ad9f9a8c5b370ffa916b9089ef3ce523fe0266c7


---
# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Felsökning för Azure Active Directory-åtkomstproblem
Du försöker komma åt SharePoint Online-intranätet och får felmeddelandet "åtkomst nekad". Vad gör du?


Den här artikeln innehåller steg som kan hjälpa dig att lösa åtkomstproblem med din organisations onlineresurser.

För hjälp med att lösa Azure Active Directory (Azure AD)-åtkomstproblem, gå till avsnittet i artikeln som täcker din enhetsplattform:

* Windows-enhet
* iOS-enhet (kom tillbaka senare för hjälp med iPhones och iPads.)
* Android-enhet (kom tillbaka senare för hjälp med Android-telefoner och surfplattor.)

## <a name="access-from-a-windows-device"></a>Åtkomst från en Windows-enhet
Om enheten kör någon av följande plattformar, titta i nästa avsnitt för felmeddelande som visas när du försöker komma åt ett program eller en tjänst:

* Windows 10
* Windows 8.1
* Windows 8
* Windows 7
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2

### <a name="device-is-not-registered"></a>Enheten har inte registrerats
Om enheten inte är registrerad med Azure AD och programmet är skyddat med en enhetsbaserad princip kanske en sida med ett av följande felmeddelanden visas:

![Meddelandet ”Du kan inte ta dig dit härifrån” för enheter som inte har registrerats](./media/active-directory-conditional-access-device-remediation/01.png "Scenario")

Om enheten är domänansluten till Active Directory i din organisation kan du prova följande:

1. Kontrollera att du har loggat in i Windows med ditt arbetskonto (ditt Active Directory-konto).
2. Anslut till företagsnätverket via ett virtuellt privat nätverk (VPN) eller DirectAccess.
3. När du är ansluten, trycker du på Windows-tangenten + L för att låsa Windows-sessionen.
4. Ange autentiseringsuppgifterna för ditt arbetskonto för att låsa upp Windows-sessionen.
5. Vänta en stund och prova sedan att ansluta till programmet eller tjänsten igen.
6. Om samma sida visas, klickar du på länken **Mer information** och kontaktar din administratör och anger informationen.

Om enheten inte är domänansluten och kör Windows 10 kan du välja mellan två alternativ:

* Kör Azure AD Join
* Lägg till ditt arbets- eller skolkonto till Windows

Information om hur de här alternativen skiljer sig åt finns i [Använda Windows 10-enheter i din arbetsplats](active-directory-azureadjoin-windows10-devices.md).

För att köra Azure AD Join, utför du följande steg för plattformen som din enhet körs på. (Azure AD Join är inte tillgänglig på Windows Phone-enheter.)

**Windows 10 Anniversary Update**

1. Öppna appen **Inställningar**.
2. Klicka på **Konton** > **Access work or school** (Åtkomst till arbete eller skola).
3. Klicka på **Anslut**.
4. Klicka på **Anslut denna enhet till Azure AD**.
5. Autentisera dig i din organisation, ange multifaktorautentisering vid uppmaning och följ sedan stegen som visas.
6. Logga ut och logga sedan in igen med ditt arbetskonto.
7. Prova att öppna programmet igen.

**Windows 10 November 2015 Update**

1. Öppna appen **Inställningar**.
2. Klicka på **System** > **Om**.
3. Klicka på **Anslut till Azure AD**.
4. Autentisera dig i din organisation, ange multifaktorautentisering vid uppmaning och följ sedan stegen som visas.
5. Logga ut och logga sedan in igen med ditt arbetskonto (ditt Azure AD-konto).
6. Prova att öppna programmet igen.

Lägg till ditt arbets- eller skolkonto genom att göra följande steg:

**Windows 10 Anniversary Update**

1. Öppna appen **Inställningar**.
2. Klicka på **Konton** > **Access work or school** (Åtkomst till arbete eller skola).
3. Klicka på **Anslut**.
4. Autentisera dig i din organisation, ange multifaktorautentisering vid uppmaning och följ sedan stegen som visas.
5. Prova att öppna programmet igen.

**Windows 10 November 2015 Update**

1. Öppna appen **Inställningar**.
2. Klicka på **Konton** > **Dina konton**.
3. Klicka på **Arbets- eller skolkonto**.
4. Autentisera dig i din organisation, ange multifaktorautentisering vid uppmaning och följ sedan stegen som visas.
5. Prova att öppna programmet igen.

Om enheten inte är domänansluten och kör Windows 8.1 kan du, för att göra en Workplace Join och registrera enheten i Microsoft Intune göra följande steg:

1. Öppna **Datorinställningar**.
2. Klicka på **Nätverk** > **Arbetsplats**.
3. Klicka på **Anslut**.
4. Autentisera dig i din organisation, ange multifaktorautentisering vid uppmaning och följ sedan stegen som visas.
5. Klicka på **Aktivera**.
6. Prova att öppna programmet igen.

### <a name="browser-is-not-supported"></a>Webbläsaren stöds inte
Du kan nekas åtkomst om du försöker komma åt ett program eller en tjänst genom att använda någon av följande webbläsare:

* Chrome, Firefox eller en annan webbläsare som inte är Microsoft Edge eller Microsoft Internet Explorer i Windows 10 eller Windows Server 2016
* Firefox i Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2

Du ser en felsida som ser ut så här:

![Meddelandet ”Du kan inte ta dig dit härifrån” för webbläsare som inte stöds](./media/active-directory-conditional-access-device-remediation/02.png "Scenario")

Det enda du kan göra är att använda en webbläsare som stöds av programmet för din enhetsplattform.

## <a name="next-steps"></a>Nästa steg
[Villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access.md)




<!--HONumber=Jan17_HO4-->


