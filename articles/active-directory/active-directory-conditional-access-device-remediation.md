---
title: "Du kan inte ta dig dit härifrån på Azure Portal från en Windows-enhet | Microsoft Docs"
description: "Läs om ursprunget till Du kan inte ta dig dit härifrån och vad du kan kontrollera för att inte stöta på den här dialogrutan."
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
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: d86e47507f7a578e8a0affb690b6d96673b6b69a
ms.contentlocale: sv-se
ms.lasthandoff: 08/29/2017

---
# <a name="you-cant-get-there-from-here-on-a-windows-device"></a>Du kan inte ta dig dit härifrån på en Windows-enhet

Vid ett försök att komma åt exempelvis organisationens SharePoint Online-intranät kan du stöta på en sida som anger att *du kan inte komma hit härifrån*. Du ser den här sidan eftersom administratören har konfigurerat en villkorlig åtkomstprincip som förhindrar åtkomst till organisationens resurser under vissa förhållanden. Det kan vara nödvändigt att kontakta supportavdelningen eller administratören för att lösa det här problemet, men det finns några saker som du kan prova själv först.

Om du använder en **Windows**-enhet ska du kontrollera följande:

- Använder du en webbläsare som stöds?

- Kör du en version av Windows som stöds på enheten?

- Är din enhet kompatibel?






## <a name="supported-browser"></a>Webbläsare som stöds

Om administratören har konfigurerat en princip för villkorlig åtkomst kan du bara få åtkomst till organisationens resurser genom att använda en webbläsare som stöds. På en Windows-enhet stöds bara **Internet Explorer** och **Edge**.

Du kan enkelt se om du inte åtkomst till en resurs på grund av en webbläsare som inte stöds genom att titta på informationsavsnittet på felsidan:

![Meddelandet ”Du kan inte ta dig dit härifrån” för webbläsare som inte stöds](./media/active-directory-conditional-access-device-remediation/02.png "Scenario")

Det enda du kan göra är att använda en webbläsare som stöds av programmet för din enhetsplattform. En fullständig lista över webbläsare som stöds finns i [webbläsare som stöds](active-directory-conditional-access-supported-apps.md).  


## <a name="supported-versions-of-windows"></a>Versioner av Windows som stöds

Följande villkor måste vara uppfyllda för Windows-operativsystemet på din enhet: 

- Om du kör ett Windows-operativsystem på din enhet måste det vara Windows 7 eller senare.
- Om du kör ett Windows server-operativsystem på din enhet måste det måste vara Windows Server 2008 R2 eller senare. 


## <a name="compliant-device"></a>Kompatibel enhet

Administratören kan ha konfigurerat en villkorlig åtkomstprincip som tillåter åtkomst till organisationens resurser enbart från kompatibla enheter. För att vara kompatibel måste enheten antingen vara ansluten till din lokala Active Directory eller till din Azure Active Directory.

Du kan enkelt se om du inte åtkomst till en resurs på grund av en enhet som inte är kompatibel genom att titta på informationsavsnittet på felsidan:
 
![Meddelandet ”Du kan inte ta dig dit härifrån” för enheter som inte har registrerats](./media/active-directory-conditional-access-device-remediation/01.png "Scenario")


### <a name="is-your-device-joined-to-an-on-premises-active-directory"></a>Är enheten ansluten till en lokal Active Directory?

**Om enheten är ansluten till en lokal Active Directory i din organisation:**

1. Kontrollera att du har loggat in i Windows med ditt arbetskonto (ditt Active Directory-konto).
2. Anslut till företagsnätverket via ett virtuellt privat nätverk (VPN) eller DirectAccess.
3. När du är ansluten, trycker du på Windows-tangenten + L för att låsa Windows-sessionen.
4. Lås upp Windows-sessionen genom att ange autentiseringsuppgifterna för ditt arbetskonto.
5. Vänta en stund och prova sedan att ansluta till programmet eller tjänsten igen.
6. Om samma sida visas, klickar du på länken **Mer information** och kontaktar din administratör och anger informationen.


### <a name="is-your-device-not-joined-to-an-on-premises-active-directory"></a>Är enheten inte ansluten till en lokal Active Directory?

Om enheten inte är ansluten till en lokal Active Directory och kör Windows 10 har du två alternativ:

* Kör Azure AD Join
* Lägg till ditt arbets- eller skolkonto till Windows

Information om hur de här alternativen skiljer sig åt finns i [Använda Windows 10-enheter i din arbetsplats](active-directory-azureadjoin-windows10-devices.md).  
Om enheten:

- Tillhör din organisation bör du köra Azure AD Join.
- Är det en personlig enhet eller en Windows Phone bör du lägga till ditt arbets- eller skolkonto i Windows 



#### <a name="azure-ad-join-on-windows-10"></a>Azure AD Join i Windows 10

Stegen för att ansluta din enhet till Azure AD beror på vilken version av Windows 10 som körs på den. Om du vill kontrollera vilken version av operativsystemet Windows 10 som körs, kör du kommandot **winver**: 

![Windows-version](./media/active-directory-conditional-access-device-remediation/03.png )


**Windows 10 Anniversary Update (version 1607):**

1. Öppna appen **Inställningar**.
2. Klicka på **Konton** > **Access work or school** (Åtkomst till arbete eller skola).
3. Klicka på **Anslut**.
4. Klicka på **Anslut denna enhet till Azure AD**.
5. Autentisera dig i din organisation, ange multifaktorautentisering vid uppmaning och följ sedan stegen som visas.
6. Logga ut och logga sedan in igen med ditt arbetskonto.
7. Prova att öppna programmet igen.

**Windows 10 November 2015 Update (version 1511):**

1. Öppna appen **Inställningar**.
2. Klicka på **System** > **Om**.
3. Klicka på **Anslut till Azure AD**.
4. Autentisera dig i din organisation, ange multifaktorautentisering vid uppmaning och följ sedan stegen som visas.
5. Logga ut och logga sedan in igen med ditt arbetskonto (ditt Azure AD-konto).
6. Prova att öppna programmet igen.


#### <a name="workplace-join-on-windows-81"></a>Anslut till arbetsplats i Windows 8.1

Om enheten inte är domänansluten och kör Windows 8.1 kan du, för att göra en Workplace Join och registrera enheten i Microsoft Intune göra följande steg:

1. Öppna **Datorinställningar**.
2. Klicka på **Nätverk** > **Arbetsplats**.
3. Klicka på **Anslut**.
4. Autentisera dig i din organisation, ange multifaktorautentisering vid uppmaning och följ sedan stegen som visas.
5. Klicka på **Aktivera**.
6. Prova att öppna programmet igen.



#### <a name="add-your-work-or-school-account-to-windows"></a>Lägg till ditt arbets- eller skolkonto till Windows 


**Windows 10 Anniversary Update (version 1607):**

1. Öppna appen **Inställningar**.
2. Klicka på **Konton** > **Access work or school** (Åtkomst till arbete eller skola).
3. Klicka på **Anslut**.
4. Autentisera dig i din organisation, ange multifaktorautentisering vid uppmaning och följ sedan stegen som visas.
5. Prova att öppna programmet igen.


**Windows 10 November 2015 Update (version 1511):**

1. Öppna appen **Inställningar**.
2. Klicka på **Konton** > **Dina konton**.
3. Klicka på **Arbets- eller skolkonto**.
4. Autentisera dig i din organisation, ange multifaktorautentisering vid uppmaning och följ sedan stegen som visas.
5. Prova att öppna programmet igen.





## <a name="next-steps"></a>Nästa steg
[Villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access.md)


