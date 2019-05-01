---
title: Riktlinjer för Azure Marketplace och AppSource publisher | Azure
description: Riktlinjer för Azure Marketplace och AppSource för appen och tjänsten utgivare
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 05bb531a88677125318ddc23563cd08a3901aa4e
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937936"
---
# <a name="guidelines"></a>Riktlinjer  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Riktlinjer för Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Riktlinjer för att skapa ett Microsoft-ID för att hantera ett marketplace-konto  
Om mer än en person behöver åtkomst till samma Microsoft-ID används för att skapa marketplace-konto, bör du följa dessa riktlinjer om du vill skapa ett företagskonto. 

>[!IMPORTANT]
>Microsoft rekommenderar att du använder Azure Active Directory (Azure AD) för att tilldela roller till enskilda användare för att godkänna flera användare åtkomst till ditt konto i Microsoft Developer Center (Dev Center). Varje användare måste komma åt kontot genom att logga in med enskilda autentiseringsuppgifter för Azure AD. Skapa ditt Microsoft-ID med hjälp av en e-postadress i en domän registrerats för ditt företag föreslår vi att e-postmeddelandet inte tilldelas till en person. Ett exempel är `windowsapps@fabrikam.com`.  
>*   Mer information finns i [problemet: Microsoft-ID i en Azure AD federerad domän](#issue-microsoft-id-in-an-azure-ad-federated-domain) avsnittet.  

*   Begränsa åtkomsten till Microsoft-ID till det minsta antalet utvecklare. 
*   Ställ in en företagets e-post-distributionslista (DL) som innehåller alla som måste komma åt ditt Dev Center-konto. Lägg till Distributionslistans e-postadress till din säkerhetsinformation. I Distributionslistan gör det möjligt för anställda på listan för att ta emot säkerhetskoder vid begäran och hantera säkerhetsinformationen för ditt Microsoft-ID. Om hur du konfigurerar en distributionslista inte är möjligt, måste sedan ägaren av enskilda e-postkontot vara tillgängliga för att komma åt och dela säkerhetskod när du uppmanas.  
    *   Till exempel ägaren uppmanas att ange när nya säkerhetsinformation har lagts till Microsoft-ID eller när Microsoft-ID hämtas från en ny enhet.  
*   Lägg till ett telefonnummer för företag som kräver inte ett tillägg och är tillgänglig för viktiga gruppmedlemmar.  
*   I allmänhet bör du kräva utvecklare kan använda betrodda enheter för att logga in på ditt Dev Center-konto. Alla viktiga teammedlemmar ska ha åtkomst till betrodda enheter. Med betrodda enheter att få åtkomst till minskar kravet för att skicka säkerhetskoder när någon kommer åt Dev Center-konto.  
*   Om du måste bevilja åtkomst till Dev Center-konto från en icke betrodd dator, bör du begränsa åtkomsten till högst fem utvecklare. Utvecklarna bör helst åtkomst till kontot från datorer som delar samma geografiska och nätverksplats.  
*   Ofta granska och kontrollera din säkerhetsinformation.  
    *   Din säkerhetsinformation finns säkerheten inställningssidan finns på [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Dev Center-konto ska nås främst från betrodda datorer. Det är viktigt att du komma åt betrodda datorer eftersom det finns en gräns för hur många koder som genererats per Dev Center-konto per vecka. Med hjälp av betrodda datorer kan du även den mest säkra och konsekvent inloggningsupplevelsen. 
*   Mer information om ytterligare Dev Center-konto riktlinjer och säkerhet finns i den inledande sidan en developer-konto finns på [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Ärende: Microsoft-ID i en federerad Azure AD-domän  
Ditt företagskonto kan vara externa via Azure Active Directory (AD Azure). Om du försöker skapa ett Microsoft-ID med en företagets e-postadress som är federerad med Azure AD, får du ett fel. Om du får ett fel, bör du kontrollera med din IT-avdelningen att bekräfta ditt konto är sammanslaget via Azure AD. Azure AD-federerad e-post är ett känt problem och Microsoft arbetar för att lösa den.  
*   Mer information om Azure AD finns i Azure Active Directory-dokumentationen sidan finns på [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

Microsoft rekommenderar att en lösning. Följ dessa steg om du vill skapa en ny e-postadress i den `outlook.com` domän och skapa en regel för att vidarebefordra kommunikationen.  
1.  Gå till sidan Skapa kontot och klicka på Hämta en ny e-postadress länk. 
    *   Om du vill registrera dig för ditt Microsoft-ID, finns på sidan Skapa kontot i [signup.live.com/signup](https://signup.live.com/signup).  
2.  Skapa ny e-postadress och ange ett lösenord. Ett nytt Microsoft-ID och en e-postlåda i den `outlook.com` domän skapas. Fortsätt registreringen tills kontot skapas.  

    >[!IMPORTANT]
    >Du måste använda en e-postadress eller distribution-lista som registreras som ett Microsoft-ID för att registrera i Dev Center. Microsoft rekommenderar att du använder en distributionslista för att ta bort beroendet från enskilda användare. Om din e-postadress eller en distributionsplats lista inte är registrerad, måste sedan du registrera nu.    

    >[!Important]
    >Om din någon e-postadress finns i den `Microsoft` företagets domän, är du inte kunna använda den för registrering i Dev Center.  

3.  När du har skapat Microsoft-ID med Outlook e-postadress, kan du logga in på din postlåda i Outlook. Skapa ett e-postmeddelande vidarebefordran av regeln. E-postmeddelandet vidarebefordran av regeln ska flytta alla e-postmeddelanden som tas emot i Outlook-postlåda till e-postadressen i domänen som du skapade för att hantera dina marketplace-konto.  
    *   Om du vill logga in på din postlåda i Outlook, finns på sidan Outlook på [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Mer information om vidarebefordran finns i reglerna för användning i Outlook Web App för att automatiskt vidarebefordra meddelanden till en annan kontosidan i [support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  Vidarebefordringsregel för skickar alla e-post och kommunikation som tas emot i Outlook e-postkonto som e-postadress i en domän som har registrerats för ditt företag. Din `outlook.com` e-postadresser måste användas för autentisering i både Dev Center och partnerportalen i molnet.  

## <a name="next-steps"></a>Nästa steg
*   Gå till den [Azure Marketplace och AppSource Publiceringsguiden](./marketplace-publishers-guide.md) sidan.  
 
---
