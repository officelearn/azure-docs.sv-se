---
title: Skaffa en Azure AD-klient | Microsoft Docs
description: "Hur du skaffar en Azure Active Directory-klient för registrering och utveckling av program."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/19/2017
ms.author: bryanla
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: b4232f2d27624e44a720047ad44991059316413e
ms.contentlocale: sv-se
ms.lasthandoff: 04/14/2017

---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Skaffa en Azure Active Directory-klient
I Azure Active Directory (Azure AD) representerar en [klient](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) en organisation.  Det är en dedikerad instans av Azure AD-tjänsten som en organisation tilldelas och äger när den registrerar sig för en Microsoft-molntjänst som Azure, Microsoft Intune och Office 365.  Varje Azure AD-klient är separat och åtskild från andra Azure AD-klienter.  

En klient inrymmer användarna på ett företag och informationen om dem – deras lösenord, användarprofildata, behörigheter och så vidare.  Den innehåller också grupper, program och annan information som hör till en organisation och dess säkerhet.

Om du vill tillåta att Azure AD-användare loggar in till ditt program måste du registrera programmet i en klient som du äger.  Det är **helt kostnadsfritt** att publicera ett program i en Azure AD-klient.  De flesta utvecklare skapar i själva verket flera klienter och program för experimentering, utveckling, mellanlagring och testning.  Organisationer som registrera sig för och använder ditt program kan välja att köpa licenser om de vill utnyttja avancerade katalogfunktioner.

Så, hur skaffar du en Azure AD-klient?  Hur processen ser ut beror delvis på om:

* [du har en befintlig Office 365-prenumeration](#use-an-existing-office-365-subscription)
* [du har en befintlig Azure-prenumeration som är associerad med ett Microsoft-konto](#use-an-msa-azure-subscription)
* [du har en befintlig Azure-prenumeration som är associerad med ett organisationskonto](#use-an-organizational-azure-subscription)
* [du inte har något av ovanstående och vill börja från början.](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>Använda en befintlig Office 365-prenumeration
Om du har en befintlig prenumeration på Office 365 kan har du redan en Azure AD-klientorganisation! Du kan logga in på [Azure Portal](https://portal.azure.com) med ditt O365-konto och börja använda Azure AD.

## <a name="use-an-msa-azure-subscription"></a>Använda en MSA Azure-prenumeration
Om du redan har registrerat dig för en Azure-prenumeration med ditt individuella Microsoft-konto har du redan en klient!  När du loggar in på [Azure Portal](https://portal.azure.com), kommer du automatiskt att loggas in till din standard-klientorganisation. Du kan använda den här innehavaren om det är lämpligt, men du kanske vill skapa ett administratörskonto för din organisation.

I så fall följer du dessa steg.  Alternativt kanske du vill skapa en ny klient och skapa en administratör i den klienten med hjälp av en liknande process.

1. Logga in på [Azure Portal](https://portal.azure.com) med ditt individuella konto
2. Navigera till “Azure Active Directory”-avsnittet i portalen (finns i vänster navigationsfält, under **Fler tjänster**)
3. Du borde automatiskt vara inloggade på ”Standardkatalogen”, annars kan du växla kataloger genom att klicka på ditt kontonamn i det övre högra hörnet.
4. Från avsnittet **Snabbuppgifter**, väljer du **lägg till en användare**.
5. I formuläret Lägg till en användare, anger du följande information:

   * Namn: (välj ett lämpligt värde)
   * Användarnamn: (välj ett användarnamn för den här administratören)
   * Profil: (Fyll i lämpliga värden för förnamn, efternamn, jobbtitel och avdelning)
   * Roll: Global administratör
6. När du har fyllt i formuläret Lägg till användare och fått det tillfälliga lösenordet för den nya administrativa användaren är det viktigt att du kommer ihåg lösenordet eftersom du måste logga in med den nya användaren för att kunna ändra lösenordet. Du kan också skicka lösenordet direkt till användaren med hjälp av en alternativ e-postadress.
7. Klicka på **Skapa** för att skapa den nya användaren.
8. För att ändra det tillfälliga lösenordet, loggar du in på [https://login.microsoftonline.com](https://login.microsoftonline.com) med det här nya användarkontot och ändrar lösenordet när du tillfrågas.

## <a name="use-an-organizational-azure-subscription"></a>Använda en Azure-prenumeration för en organisation
Om du redan har registrerat dig för en Azure-prenumeration med ditt organisationskonto har du redan en klient.  I [Azure Portal](https://portal.azure.com), borde du hitta en klientorganisation när du navigerar till "Fler tjänster" och "Azure Active Directory".  Du kan använda den här klienten om du vill.

## <a name="start-from-scratch"></a>Börja från början
Oroa dig inte om inget av alternativen ovan passar dig.  Gå bara till [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) och registrera dig för Azure med en ny organisation.  När du har slutfört processen har du din egen Azure AD-klient med det domännamn som du valde när du registrerade dig.  I [Azure Portal](https://portal.azure.com), hittar du din klientorganisation genom att navigera till ”Azure Active Directory” i det vänstra navigeringsfältet.

Som en del av registreringen i Azure måste du ange kreditkortsinformation.  Oroa dig inte: du debiteras inte när du publicerar program i Azure AD eller skapar nya klienter.

