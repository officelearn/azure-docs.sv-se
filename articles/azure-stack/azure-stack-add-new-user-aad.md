---
title: Lägg till ett nytt konto i Azure Stack-klient i Azure Active Directory | Microsoft Docs
description: När du har distribuerat Microsoft Azure Stack Development Kit måste du skapa minst ett klient-användarkonto, så du kan utforska klientportalen.
services: azure-stack
documentationcenter: ''
author: patricka
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 40b26429cf4e7f218c2b5a276ae936f60bd6abe4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453243"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Lägg till ett nytt konto i Azure Stack-klient i Azure Active Directory

Efter [distribuera Azure Stack Development Kit](azure-stack-run-powershell-script.md), måste ett användarkonto för innehavare så att du kan utforska klientportalen och testa dina erbjudanden och planer. Du kan skapa ett klientkonto av [med Azure portal](#create-an-azure-stack-tenant-account-using-the-azure-portal) eller med hjälp av PowerShell.

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Skapa ett Azure Stack-klient-konto med Azure portal

Du måste ha en Azure-prenumeration du använder Azure-portalen.

1. Logga in på [Azure](https://portal.azure.com).
2. I det vänstra navigeringsfältet väljer **Active Directory** och växla till den katalog som du vill använda för Azure Stack eller skapa en ny.
3. Välj **Azure Active Directory** > **användare** > **ny användare**.

    ![Användare - sidan för alla användare med nya användaren markerat](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. På den **användaren** fyller du i informationen som krävs.

    ![Lägg till ny användare, användarsidan med användarinformation](media/azure-stack-add-new-user-aad/new-user-user.png)

    - **Namn (krävs).** Första och sista namnet på den nya användaren. Till exempel Mary Parker.
    - **Användarnamn (krävs).** Användarnamn för den nya användaren. Till exempel mary@contoso.com.
        Domändelen av användarnamnet måste använda antingen det initiala standarddomännamnet, <_domännamn_>. onmicrosoft.com eller ett anpassat domännamn, till exempel contoso.com. Läs mer om hur du skapar ett anpassat domännamn, [lägga till ett anpassat domännamn i Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    - **Profil.** Alternativt kan du lägga till mer information om användaren. Du kan också lägga till användarinformation vid ett senare tillfälle. Läs mer om att lägga till användarinformation [lägga till eller ändra information om användarprofiler](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).
    - **Katalogroll.**  Välj **användaren**.

5. Kontrollera **visa lösenord** och kopiera det automatiskt genererade lösenordet som angavs i den **lösenord** box. Du behöver det här lösenordet för den inledande inloggningsprocessen.

6. Välj **Skapa**.

    Användaren skapas och läggs till din Azure AD-klient.

7. Logga in på Microsoft Azure-portalen med det nya kontot. Ändra lösenordet när du uppmanas till detta.
8. Logga in på `https://portal.local.azurestack.external` med det nya kontot att se klientportalen.

## <a name="create-an-azure-stack-user-account-using-powershell"></a>Skapa ett Azure Stack-användarkonto med hjälp av PowerShell

Om du inte har en Azure-prenumeration kan använda du inte Azure-portalen för att lägga till ett användarkonto för innehavare. I det här fallet kan du använda Azure Active Directory-modulen för Windows PowerShell i stället.

> [!NOTE]
> Om du använder Account (Live ID) för att distribuera Azure Stack Development Kit kan du inte använda AAD PowerShell för att skapa klientkonto. 

1. Installera den [Microsoft Online Services-inloggningsassistent för IT-proffs RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Installera den [Azure Active Directory-modulen för Windows PowerShell (64-bitars version)](https://go.microsoft.com/fwlink/p/?linkid=236297) och öppna den.
3. Kör följande cmdlets:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Logga in på Microsoft Azure med det nya kontot. Ändra lösenordet när du uppmanas till detta.
2. Logga in på `https://portal.local.azurestack.external` med det nya kontot att se klientportalen.

## <a name="next-steps"></a>Nästa steg

[Lägg till Azure Stack-användare i AD FS](azure-stack-add-users-adfs.md)
