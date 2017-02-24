---
title: "Hantera katalogen för din Office 365-prenumeration i Azure | Microsoft Docs"
description: Hantera en Office 365-prenumerationskatalog med Azure Active Directory och den klassiska Azure-portalen
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 746987b7-2dfd-4b35-819d-37c1b65c5c81
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: a4a78b92c8bb9e8aff25fd724ed78283de8f2fd8
ms.openlocfilehash: 2fda7dc07d01bcd0629d1fa80ec9262e80af4c5f


---
# <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Hantera katalogen för din Office 365-prenumeration i Azure
I den här artikeln beskrivs hur du hanterar en katalog som har skapats för en Office 365-prenumeration, med hjälp av den klassiska Azure-portalen. Du måste antingen vara tjänstadministratör eller en medadministratör för en Azure-prenumeration för att kunna logga in på den klassiska Azure-portalen. Om du ännu inte har någon Azure-prenumeration kan du registrera dig för en [kostnadsfria 30-dagars utvärderingsversion](https://azure.microsoft.com/trial/get-started-active-directory/) och redan idag distribuera din första molnlösning inom 5 minuter med hjälp av den här länken. Använd det arbets- eller skolkonto som du använder för att logga in till Office 365.

När du har slutfört Azure-prenumerationen kan du logga in på den klassiska Azure-portalen och komma åt Azure-tjänster. Klicka på Active Directory-tillägget om du vill hantera samma katalog som autentiserar dina Office 365-användare.

Om du redan har en Azure-prenumeration är processen lika enkel om du vill hantera ytterligare en katalog. Anta till exempel att Michael Smith har en prenumeration på Office 365 för Contoso.com. Han har också en Azure-prenumeration som han har registrerat sig för med sitt Microsoft-konto, msmith@hotmail.com. I detta fall hanterar han två kataloger.

| Prenumeration | Office 365 | Azure |
| --- | --- | --- |
|   Visningsnamn |Contoso |Azure Active Directory-standardkatalog (Azure AD) |
|   Domännamn |contoso.com |msmithhotmail.onmicrosoft.com |

Han vill hantera användaridentiteterna i katalogen Contoso när han är inloggad i Azure med sitt Microsoft-konto så att han kan använda Azure AD-funktioner som Multi-Factor Authentication. Följande diagram illustrerar processen.

![Diagram för att hantera två oberoende kataloger](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

I det här fallet är de två katalogerna oberoende av varandra.

## <a name="to-manage-two-independent-directories"></a>Så här hanterar du två oberoende kataloger
För att Michael Smith ska kunna hantera båda katalogerna när han är inloggad i Azure som msmith@hotmail.com, måste han utföra följande steg:

> [!NOTE]
> Dessa steg kan endast utföras när en användare är inloggad med ett Microsoft-konto. Om användaren har loggat in med ett arbets eller skolkonto, så är alternativet **Använd befintlig katalog** inte tillgängligt. Ett arbets- eller skolkonto konto kan bara autentiseras genom sin hemkatalog (dvs den katalog där arbets- eller skolkontot lagras, och som företaget eller skolan äger).
>
>

1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com) som msmith@hotmail.com.
2. Klicka på **Nytt** > **Apptjänster** > **Active Directory** > **Katalog** > **Skapa anpassade**.
3. Klicka på Använd befintlig katalog och markera kryssrutan **Jag är redo att bli utloggad nu**.
4. Logga in på den klassiska Azure-portalen som global administratör för Contoso.onmicrosoft.com (till exempel msmith@contoso.com)).
5. När du tillfrågas om du vill **använda Contoso-katalogen med Azure** klickar du på **Fortsätt**.
6. Klicka på **Logga ut nu**.
7. Logga in på den klassiska Azure-portalen som msmith@hotmail.com. Contoso-katalogen och standardkatalogen visas i Active Directory-tillägget.

När dessa steg har slutförts är msmith@hotmail.com en global administratör i Contoso-katalogen.

## <a name="to-administer-resources-as-the-global-admin"></a>Så här administrerar du resurser som den globala administratören
Anta nu att Jane Doe behöver administrera webbplatser och databasresurser som är associerade med Azure-prenumerationen för msmith@hotmail.com. Innan hon kan göra det måste Michael Smith utföra dessa steg:

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) med tjänstadministratörskontot för Azure-prenumerationen (i det här exemplet msmith@hotmail.com)).
2. Överföra prenumerationen till Contoso-katalogen: klicka på **Inställningar** > **Prenumerationer** > välj prenumerationen > **Redigera katalog** > välj **Contoso (Contoso.com)**. Som en del av överföringen tas alla arbets- eller skolkonton som är medadministratörer i prenumerationen bort.
3. Lägga till Jane Doe som medadministratör för prenumerationen: klicka på **Inställningar** > **Administratörer** > välj prenumerationen > **Lägg till** > skriv **JohnDoe@Contoso.com**.

## <a name="next-steps"></a>Nästa steg
Mer information om relationen mellan prenumerationer och kataloger finns i [Hur en prenumeration är associerad med en katalog](active-directory-how-subscriptions-associated-directory.md).



<!--HONumber=Feb17_HO2-->


