---
title: Riktlinjer för Azure Marketplace och AppSource publisher | Azure
description: Riktlinjer för Azure Marketplace och AppSource för appen och tjänsten utgivare
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 4da6f5c4513477d1adbf4d7645a66de112eeab23
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307493"
---
# <a name="guidelines"></a>Riktlinjer  

<!--
## Guidelines for AppSource  
-->
---  

## <a name="guidelines-for-azure-marketplace"></a>Riktlinjer för Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Riktlinjer för att skapa ett Microsoft-ID för att hantera en marketplace-konto  
Om mer än en person behöver åtkomst till samma Microsoft-ID används för att skapa marketplace-konto, ska du följa dessa riktlinjer som hjälper dig att skapa ett företag. 

>[!IMPORTANT]
>Microsoft rekommenderar att du använder Azure Active Directory (Azure AD) för att tilldela roller till enskilda användare för att godkänna flera användare att komma åt ditt konto på Microsoft Developer Center (Dev Center). Varje användare måste få åtkomst till kontot genom att logga in med enskilda autentiseringsuppgifter för Azure AD. Skapa ditt Microsoft-ID med hjälp av en e-postadress i en domän registrerats för ditt företag föreslår vi att e-postmeddelandet inte tilldelas till en enskild person. Ett exempel är `windowsapps@fabrikam.com`.  
>*   Mer information finns i [problemet: Microsoft-ID i en Azure AD federerade domänen](#issue-microsoft-id-in-an-azure-ad-federated-domain) avsnitt.  

*   Begränsa åtkomsten till Microsoft-ID till det lägsta antalet utvecklare. 
*   Ställ in en företagets e-postdistributionslista (DL) som innehåller alla användare ha åtkomst till Dev Center-konto. Lägg till DL e-postadress till din säkerhetsinformation. DL gör det möjligt för anställda i listan för att ta emot säkerhetskoder begäran och hantera säkerhetsinformationen för ditt Microsoft-ID. Om hur du konfigurerar en distributionslista inte är möjligt måste ägaren av enskilda e-postkontot att kunna komma åt och dela säkerhetskod när du uppmanas.  
    *   Till exempel uppmanas ägare när nya säkerhetsinformation läggs till Microsoft-ID eller när Microsoft-ID används från en ny enhet.  
*   Lägga till ett telefonnummer för företag som inte kräver ett tillägg och går att komma åt viktiga gruppmedlemmar.  
*   I allmänhet bör du kräva att använda betrodda enheter för att logga in på ditt konto Dev Center utvecklare. Alla nycklar teammedlemmar ska ha åtkomst till betrodda enheter. Använda betrodda enheter att få åtkomst till minskar krav för att skicka säkerhetskoder när någon kommer åt Dev Center-konto.  
*   Om du behöver ge åtkomst till Dev Center-konto från en icke betrodd dator bör du begränsa åtkomsten till högst fem utvecklare. Helst bör utvecklarna få åtkomst till kontot från datorer som delar samma geografiska och nätverksplats.  
*   Ofta granska och kontrollera din säkerhetsinformation.  
    *   Din säkerhetsinformation finns säkerheten inställningssidan finns på [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Dev Center-konto ska användas i första hand från betrodda datorer. Det är viktigt att du åtkomst till från betrodda datorer eftersom det finns en gräns för hur många koder som genererats per Dev Center-konto per vecka. Med hjälp av betrodda datorer kan du även den mest säkra och konsekvent inloggningen. 
*   Mer information om ytterligare Dev Center-konto riktlinjer och säkerhet finns i den inledande sidan en utvecklare konto finns i [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---  

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problem: Microsoft-ID i en federerad Azure AD-domän  
Ditt företagskonto kan bli federerad via Azure Active Directory (AD Azure). Om du försöker skapa ett Microsoft-ID med hjälp av en företagets e-postadress som är federerat med Azure AD, får du ett felmeddelande. Om du får ett felmeddelande, bör du kontrollera med din IT-teamet att bekräfta ditt konto är sammanslaget med Azure AD. Azure AD federerad e-post är ett känt problem och Microsoft arbetar för att lösa den.  
*   Mer information om Azure AD finns i Azure Active Directory-dokumentationen sidan finns på [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

Microsoft rekommenderar en lösning. Följ dessa steg om du vill skapa en ny e-postadress i den `outlook.com` domän och skapa en regel för att vidarebefordra meddelanden.  
1.  Gå till sidan Skapa konto och klicka på Hämta en ny länk i e-postadress. 
    *   Om du vill registrera dig för Microsoft-ID, finns på sidan Skapa konto på [signup.live.com/signup](https://signup.live.com/signup).  
2.  Skapa ny e-postadress och ange ett lösenord. Ett nytt Microsoft-ID och ett e-postlåda i den `outlook.com` domän skapas. Fortsätt registreringsprocessen tills kontot skapas.  

    >[!IMPORTANT]
    >Du måste använda en e-postadress distribution lista eller som är registrerad som ett Microsoft-ID för att registrera i Dev Center. Microsoft rekommenderar att du använder en distributionslista för att ta bort beroendet från personer. Om e-postadress eller distribution listan inte är registrerad, måste sedan du registrera nu.    

    >[!Important]
    >Om din någon e-postadress finns i den `Microsoft` företagets domän, och du inte kan användas för registrering i Dev Center.  

3.  När du har skapat Microsoft-ID med Outlook-e-postadress, logga in på din postlåda i Outlook. Skapa ett e-postmeddelande vidarebefordran av regeln. E-postmeddelandet vidarebefordran av regeln ska flytta alla e-postmeddelanden som tas emot i Outlook-postlådan till e-postadressen i domänen som du skapade för att hantera din marketplace-konto.  
    *   Om du vill logga in på din postlåda i Outlook, finns på sidan Outlook på [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Mer information om vidarebefordran av regler finns i Använd regler i Outlook Web App för att automatiskt vidarebefordra meddelanden till en annan kontosida på [support.office.com/article/ Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  Vidarebefordringsregel för skickar alla e-post och kommunikation som togs emot i Outlook-e-postkonto till den e-postadressen i en domän som har registrerats för ditt företag. Din `outlook.com` e-postadress måste användas för att autentisera i både Dev Center och i molnet Partner-portalen.  

## <a name="next-steps"></a>Nästa steg
*   Besök den [Azure Marketplace och AppSource Publisher Guide](./marketplace-publishers-guide.md) sidan.  
 
---  
