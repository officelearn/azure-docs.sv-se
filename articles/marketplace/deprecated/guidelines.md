---
title: Riktlinjer för Azure Marketplace och AppSource-utgivare | Azure
description: Riktlinjer för Azure Marketplace och AppSource för app- och tjänstutgivare
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: dsindona
ms.openlocfilehash: 9156126ff2a3dbe936dc426c6f5097c6bba7b7b7
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387622"
---
# <a name="guidelines"></a>Riktlinjer  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Riktlinjer för Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Riktlinjer för att skapa ett Microsoft-ID för att hantera ett marketplace-konto  
Om fler än en person behöver åtkomst till samma Microsoft-ID som används för att skapa ditt marketplace-konto bör du följa dessa riktlinjer för att hjälpa dig att skapa ett företagskonto. 

>[!IMPORTANT]
>Om du vill att flera användare ska komma åt ditt Microsoft Developer Center-konto (Dev Center) rekommenderar Microsoft att du använder Azure Active Directory (Azure AD) för att tilldela roller till enskilda användare. Varje användare måste komma åt kontot genom att logga in med enskilda Azure AD-autentiseringsuppgifter. Skapa ditt Microsoft-ID med hjälp av en e-postadress i en domän som är registrerad på ditt företag Microsoft föreslår att e-postmeddelandet inte tilldelas en individ. Ett exempel är `windowsapps@fabrikam.com`.  
>*   Mer information finns i [problemet: Microsoft-ID i ett Azure AD-federerat domänavsnitt.](#issue-microsoft-id-in-an-azure-ad-federated-domain)  

*   Begränsa åtkomsten till Microsoft-ID:et till minsta möjliga antal utvecklare. 
*   Konfigurera en företagsdistributionslista (DL) som innehåller alla som måste komma åt ditt Dev Center-konto. Lägg till DL-e-postadressen i din säkerhetsinformation. DL gör det möjligt för alla anställda i listan att ta emot säkerhetskoder på begäran och hantera säkerhetsinformationen för ditt Microsoft-ID. Om det inte är möjligt att konfigurera en distributionslista måste ägaren till det enskilda e-postkontot vara tillgänglig för åtkomst och delning av säkerhetskoden när du uppmanas att göra det.  
    *   Ägaren uppmanas till exempel när ny säkerhetsinformation läggs till i Microsoft-ID:t eller när Microsoft-ID:t nås från en ny enhet.  
*   Lägg till ett företagstelefonnummer som inte kräver ett tillägg och som är tillgängligt för viktiga gruppmedlemmar.  
*   I allmänhet bör du kräva att utvecklare använder betrodda enheter för att logga in på ditt Dev Center-konto. Alla viktiga gruppmedlemmar bör ha åtkomst till de betrodda enheterna. Om du använder betrodda enheter för åtkomst minskar kravet på att skicka säkerhetskoder när någon ansluter till Dev Center-kontot.  
*   Om du måste bevilja åtkomst till Dev Center-kontot från en icke-betrodd dator bör du begränsa åtkomsten till högst fem utvecklare. Helst bör dina utvecklare komma åt kontot från datorer som delar samma geografiska plats och nätverksplats.  
*   Granska och verifiera ofta din säkerhetsinformation.  
    *   Om du vill visa din säkerhetsinformation besöker du sidan Säkerhetsinställningar som finns på [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Ditt Dev Center-konto bör i första hand nås från betrodda datorer. Det är viktigt att du kommer åt från betrodda datorer, eftersom det finns en gräns för antalet koder som genereras per Dev Center-konto per vecka. Genom att använda betrodda datorer kan du också använda de säkraste och konsekventa inloggningsupplevelsen. 
*   Mer information om ytterligare riktlinjer för Dev Center-kontot och säkerhet finns på sidan Öppna ett utvecklarkonto som finns på [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problem: Microsoft-ID i en Azure AD-federerad domän  
Ditt företagskonto kan federeras via Azure Active Directory (Azure AD). Om du försöker skapa ett Microsoft-ID med en företags-e-postadress som är federerad med Azure AD visas ett felmeddelande. Om du får ett felmeddelande bör du kontrollera med IT-teamet för att bekräfta att ditt konto är federerat via Azure AD. Azure AD federerade e-post är ett känt problem och Microsoft arbetar för att lösa det.  
*   Mer information om Azure AD finns på sidan Azure Active Directory Documentation som finns på [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

Microsoft rekommenderar en lösning. Följ dessa steg för att skapa `outlook.com` en ny e-postadress i domänen och skapa en regel för att vidarebefordra din kommunikation.  
1.  Gå till sidan Skapa konto och klicka på länken Hämta en ny e-postadress. 
    *   Om du vill registrera dig för ditt Microsoft-ID besöker du sidan Skapa konto som finns på [signup.live.com/signup](https://signup.live.com/signup).  
2.  Skapa den nya e-postadressen och ange ett lösenord. Ett nytt Microsoft-ID och `outlook.com` en e-postpostlåda i domänen skapas. Fortsätt registreringsprocessen tills kontot skapas.  

    >[!IMPORTANT]
    >Du måste använda en e-postadress eller distributionslista som är registrerad som ett Microsoft-ID för att registrera dig i Dev Center. Microsoft rekommenderar att du använder en distributionslista för att ta bort beroendet från enskilda personer. Om din e-postadress eller distributionslista inte är registrerad måste du registrera dig nu.    

    >[!Important]
    >Om din e-postadress finns `Microsoft` i företagsdomänen kan du inte använda den för registrering i Dev Center.  

3.  När du har skapat Microsoft-ID:n med e-postadressen i Outlook loggar du in på outlook-postlådan. Skapa en regel för vidarebefordran av e-post. Regeln om vidarebefordran av e-post bör flytta alla e-postmeddelanden som tas emot i Outlook-postlådan till e-postadressen på domänen som du skapade för att hantera ditt marketplace-konto.  
    *   Om du vill logga in på outlook-postlådan besöker du outlook-sidan som finns på [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Mer information om vidarebefordringsregler finns i användningsreglerna i Outlook Web App om du automatiskt vidarebefordrar meddelanden till en annan kontosida som finns på [support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  Vidarebefordringsregeln skickar all e-post och kommunikation som tas emot i Outlook-e-postkontot till e-postadressen i en domän som är registrerad på ditt företag. Din `outlook.com` e-postadress måste användas för att autentisera i både Dev Center och Cloud Partner Portal.  

## <a name="next-steps"></a>Nästa steg

*   Besök sidan [Azure Marketplace och AppSource Publisher Guide.](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) 
 
---
