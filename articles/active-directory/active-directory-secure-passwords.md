---
title: "Skydda lösenord i Azure AD och återställa lösenord som blockeras av smart lösenordsutelåsning | Microsoft-dokument"
description: "I den här artikeln lär du dig vad en Azure AD-klient är och hur du hanterar Azure via Azure Active Directory"
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 8e625a346c9495d436a99fcf9eadf8ffeffcfdff
ms.lasthandoff: 03/28/2017


---
# <a name="secure-passwords--in-azure-ad-and-reset-passwords-that-get-blocked-by-smart-password-lockout"></a>Skydda lösenord i Azure AD och återställa lösenord som blockeras av smart lösenordsutelåsning
Den här artikeln beskriver riktlinjer som användare eller administratör kan följa för att skydda Azure Active Directory- (Azure AD) och Microsoft Account Service-konton. 

 >[!NOTE]
 >Azure AD-administratörer kan återställa användarlösenord genom att klicka på katalognamnet. Från [Azure-hanteringsportalen](https://manage.windowsazure.com) väljer du sidan Användare, klickar på namnet på användaren och sedan på Återställ lösenord. 
 >

Azure AD använder följande vanliga metoder för att skydda lösenord:
 *    Krav på lösenordslängd
 *    Krav på lösenordskomplexitet
 *    Regelbundet och periodiskt upphörande av lösenord 

Information om lösenordshanteringsfunktioner finns i [Hantera lösenord i Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-manage-passwords). 

## <a name="azure-ad-password-protection"></a>Azure AD-lösenordsskydd
Azure AD och Microsoft Account System använder beprövade metoder för att säkerställa att lösenord för användare och administratörer skyddas på ett säkert sätt. 

Det här avsnittet beskriver hur Azure AD skyddar lösenord med hjälp av följande metoder:
 *    Lösenord som förbjuds dynamiskt
 *    Smart lösenordsutelåsning

Information om lösenordshantering baserat på den senaste forskningen finns i faktabladet [Password Guidance](http://aka.ms/passwordguidance) (Riktlinjer för lösenord). 

### <a name="dynamically-banned-passwords"></a>Lösenord som förbjuds dynamiskt
Azure AD och Microsoft Account System upprätthåller lösenordskyddet genom att dynamiskt förbjuda vanliga lösenord. Azure ID Identity Protection-teamet analyserar rutinmässigt listor med förbjudna lösenord, och hindrar användare från att välja vanliga lösenord. Den här tjänsten är tillgänglig för Azure AD- och Microsoft Account Service-kunder. 

När lösenord skapas är det viktigt att administratören uppmanar användarna att välja ovanliga lösenord som innehåller en unik kombination av bokstäver, siffror och tecken. På så sätt minimeras risken för att användarlösenord komprometteras. 

**Angreppslistor**

Azure AD försöker alltid ligga steget före cyberbrottslingarna. Ett sätt att göra det är att förhindra att användare skapar lösenord som finns med på den senaste angreppslistan.

Azure AD Identity Protection-teamet analyserar kontinuerligt lösenord som används ofta. Cyberbrottslingar använder också liknande strategier för sina attacker, t.ex. genom att skapa en [regnbågstabell](https://en.wikipedia.org/wiki/Rainbow_table) för att knäcka lösenordshashar. 

Microsoft analyserar kontinuerligt [dataöverträdelser](https://www.privacyrights.org/data-breaches) och använder en dynamiskt uppdaterad lista med förbjudna lösenord, som gör att sårbara lösenord förbjuds innan de utgör ett verkligt hot för Azure AD-kunder. Mer information om vårt säkerhetsarbete finns i [Microsofts säkerhetsinformationsrapport](https://www.microsoft.com/security/sir/default.aspx). 

### <a name="smart-password-lockout"></a>Smart lösenordsutelåsning

Om Azure AD upptäcker att en potentiell cyberbrottsling försöker hacka ett användarlösenord låser vi användarkontot med hjälp av smart lösenordsutelåsning. Azure AD har utformats för att bedöma riskerna med specifika inloggningssessioner. 

Vi använder uppdaterade säkerhetsdata och utelåsningssemantik för att förhindra cyberhot. Den här metoden gör att användarna inte blir utelåsta om en cyberbrottsling har hackat användarlösenord i nätverket.

Om Azure AD låser ut en användare visas en skärm liknande följande:

  ![Utelåst från Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)
  
Och för andra Microsoft-konton ser skärmen ut som den nedan:

  ![Utelåst från ett Microsoft-konto](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Mer information om lösenordshantering i Azure Active Directory finns i [How password management works](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-how-it-works) (Så här fungerar lösenordshantering).

  >Obs! Om du är en Azure AD-administratör kan du använda [Windows Hello](https://www.microsoft.com/en-us/windows/windows-hello) för att helt och hållet förhindra att användare skapar traditionella lösenord.
  >

## <a name="next-steps"></a>Nästa steg
[Uppdatera ditt eget lösenord](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-update-your-own-password)<br>
[Grunderna i Azures identitetshantering](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>
[Få åtgärdsinformation med lösenordshanteringsrapporter](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-get-insights#view-password-reset-activity)



