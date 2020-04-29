---
title: Rikt linjer för Azure Marketplace och AppSource Publisher | Azure
description: Rikt linjer för Azure Marketplace och AppSource för app-och tjänst utgivare
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: dsindona
ms.openlocfilehash: 9156126ff2a3dbe936dc426c6f5097c6bba7b7b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80387622"
---
# <a name="guidelines"></a>Riktlinjer  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Rikt linjer för Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Rikt linjer för att skapa ett Microsoft-ID för att hantera ett Marketplace-konto  
Om mer än en person behöver åtkomst till samma Microsoft-ID som används för att skapa ett Marketplace-konto bör du följa dessa rikt linjer som hjälper dig att skapa ett företags konto. 

>[!IMPORTANT]
>Om du vill ge flera användare åtkomst till ditt Microsoft Developer Center-konto (dev Center) rekommenderar Microsoft att du använder Azure Active Directory (Azure AD) för att tilldela roller till enskilda användare. Varje användare måste ha åtkomst till kontot genom att logga in med enskilda autentiseringsuppgifter för Azure AD. Skapa ditt Microsoft-ID med hjälp av en e-postadress i en domän som är registrerad på företaget Microsoft rekommenderar att e-postmeddelandet inte tilldelas till en person. Ett exempel är `windowsapps@fabrikam.com`.  
>*   Mer information finns på [problemet: Microsoft ID i en federerad Azure AD-domän](#issue-microsoft-id-in-an-azure-ad-federated-domain) .  

*   Begränsa åtkomsten till Microsoft-ID: t till det minsta möjliga antalet utvecklare. 
*   Konfigurera en distributions lista för företagets e-post (DL) som innehåller alla som måste ha åtkomst till ditt dev Center-konto. Lägg till DL-e-postadressen i din säkerhets information. DL gör det möjligt för alla anställda i listan att ta emot säkerhets koder när de begärs och för att hantera säkerhets informationen för ditt Microsoft-ID. Om det inte är möjligt att konfigurera en distributions lista, måste ägaren till det enskilda e-postkontot vara tillgänglig för att komma åt och dela säkerhets koden när du uppmanas till det.  
    *   Till exempel uppmanas ägaren att ange ny säkerhets information till Microsoft-ID: t eller när Microsoft-ID: t nås från en ny enhet.  
*   Lägg till ett företags telefonnummer som inte kräver ett tillägg och som är tillgängligt för viktiga grupp medlemmar.  
*   I allmänhet bör du kräva att utvecklare använder betrodda enheter för att logga in på ditt dev Center-konto. Alla viktiga grupp medlemmar ska ha åtkomst till de betrodda enheterna. Om du använder betrodda enheter för åtkomst minskar kravet på att skicka säkerhets koder när någon får åtkomst till dev Center-kontot.  
*   Om du måste bevilja åtkomst till dev Center-kontot från en icke betrodd dator bör du begränsa åtkomsten till fler än fem utvecklare. Vi rekommenderar att utvecklarna får åtkomst till kontot från datorer som delar samma geografiska plats och nätverks plats.  
*   Granska och verifiera din säkerhets information.  
    *   Om du vill visa din säkerhets information går du till sidan säkerhets inställningar på [Account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Ditt dev Center-konto bör främst kommas åt från betrodda datorer. Det är viktigt att du ansluter till betrodda datorer, eftersom det finns en gräns för antalet koder som genereras per dev Center-konto per vecka. Att använda betrodda datorer möjliggör även den säkraste och konsekventa inloggnings upplevelsen. 
*   Mer information om ytterligare rikt linjer och säkerhet för dev Center-konto finns på sidan om att öppna ett Developer-konto på [docs.Microsoft.com/Windows/UWP/Publish/Opening-a-Developer-Account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problem: Microsoft ID i en federerad Azure AD-domän  
Ditt företags konto kan vara federerat genom Azure Active Directory (Azure AD). Om du försöker skapa ett Microsoft-ID med en företags-e-postadress som är federerad med Azure AD, får du ett fel meddelande. Om du får ett fel bör du kontakta IT-teamet för att bekräfta att ditt konto är sammanslaget via Azure AD. Azure AD federerad e-post är ett känt problem och Microsoft arbetar med att lösa det.  
*   Mer information om Azure AD finns på sidan Azure Active Directory dokumentation på [docs.Microsoft.com/Azure/Active-Directory](https://docs.microsoft.com/azure/active-directory).

Microsoft rekommenderar en lösning. Följ de här stegen för att skapa en ny e- `outlook.com` postadress i domänen och skapa en regel för att vidarebefordra kommunikationen.  
1.  Gå till sidan Skapa konto och klicka på länken Hämta en ny e-postadress. 
    *   Registrera dig för ditt Microsoft-ID genom att besöka sidan Skapa konto på [signup.live.com/signup](https://signup.live.com/signup).  
2.  Skapa den nya e-postadressen och ange ett lösen ord. Ett nytt Microsoft-ID och en e-postlåda `outlook.com` i domänen skapas. Fortsätt registrerings processen tills kontot har skapats.  

    >[!IMPORTANT]
    >Du måste använda en e-postadress eller distributions lista som har registrerats som ett Microsoft-ID för att registrera dig i dev Center. Microsoft rekommenderar att du använder en distributions lista för att ta bort beroendet från enskilda användare. Om din e-postadress eller distributions lista inte är registrerad, måste du registrera dig nu.    

    >[!Important]
    >Om din e-postadress finns i `Microsoft` företags domänen kan du inte använda den för registrering i dev Center.  

3.  När du har skapat Microsoft-ID: t med Outlook-e-postadressen loggar du in på din Outlook-postlåda. Skapa en regel för vidarebefordran av e-post. Regeln för vidarebefordran av e-post bör flytta alla e-postmeddelanden som tas emot i Outlook-brevlådan till e-postadressen i din domän som du skapade för att hantera ditt Marketplace-konto.  
    *   Om du vill logga in på Outlook-brevlådan går du till Outlook-sidan som finns på [Outlook.live.com/OWA](https://outlook.live.com/owa).  
    *   Mer information om regler för vidarebefordran finns i använda regler i Outlook Web App för att automatiskt vidarebefordra meddelanden till en annan konto sida som finns på [support.Office.com/article/use-Rules-in-Outlook-Web-App-to-automatically-Forward-Messages-to-another-Account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  Vidarebefordrings regeln skickar all e-post och kommunikation som tas emot i Outlook-e-postkontot till e-postadressen i en domän som är registrerad i företaget. Din `outlook.com` e-postadress måste användas för att autentisera i både dev Center och Cloud Partner Portal.  

## <a name="next-steps"></a>Nästa steg

*   Besök [Azure Marketplace-och AppSource Publisher-guide](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) sidan. 
 
---
