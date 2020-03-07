---
title: Snabb start – åtkomst & Skapa ny klient organisation – Azure AD
description: Instruktioner om hur du hittar Azure Active Directory och hur du skapar en ny klient för din organisation.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: de205beaf9800c2061cc41343b4153bd0e2b5dc7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376872"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Snabb start: skapa en ny klient i Azure Active Directory
Du kan utföra alla administrativa aktiviteter med hjälp av Azure Active Directory (Azure AD)-portalen, inklusive att skapa en ny klient för din organisation. 

I den här snabbstarten får du lära dig hur du kommer till Azure-portalen och Azure Active Directory och du får lära dig hur du skapar en grundläggande klient för din organisation.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-new-tenant-for-your-organization"></a>Så här skapar du en ny klient för din organisation
När du har loggat in på Azure-portalen kan du skapa en ny klient för din organisation. Den nya klientorganisationen representerar din organisation och hjälper dig att hantera en specifik instans av Microsoft-molntjänster för dina interna och externa användare.

### <a name="to-create-a-new-tenant"></a>Så här skapar du en ny klient

1. Logga in på din organisations [Azure Portal](https://portal.azure.com/).

1. Från Azure Portal-menyn väljer du **skapa en resurs**.  

    ![Azure Active Directory skapa resoure-sida](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. Välj **identitet**och välj sedan **Azure Active Directory**.

    Sidan **Skapa katalog** visas.

    ![Sidan skapa Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  På sidan **Skapa katalog** anger du följande information:
    
    - Skriv _Contoso_ i rutan **Organisationsnamn**.

    - Skriv _Contoso_ i rutan **Ursprungligt domännamn**.

    - Lämna alternativet _USA_ i rutan **Land eller region**.

1. Välj **Skapa**.

Den nya klientorganisationen skapas med domänen contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte planerar att fortsätta använda det här programmet kan du ta bort klienten med följande steg:

- Se till att du är inloggad på den katalog som du vill ta bort via katalog-och **prenumerations** filtret i Azure Portal och växla till mål katalogen om det behövs.
- Välj **Azure Active Directory** och klicka sedan på sidan **Contoso – översikt** och **Ta bort katalogen**.

    Klienten och dess tillhörande information tas bort.

    ![Översikts sida med markerad ta bort katalog-knapp](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Nästa steg
- Information om att ändra eller lägga till fler domännamn finns i [Lägga till ett anpassat domännamn i Azure Active Directory](add-custom-domain.md)

- Se [Lägga till eller ta bort en ny användare](add-users-azure-active-directory.md) för att lägga till användare

- Se [Skapa en basgrupp och lägga till medlemmar](active-directory-groups-create-azure-portal.md) för att lägga till grupper och medlemmar

- Lär dig mer om [rollbaserad åtkomst med hjälp av Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md) och [villkorlig åtkomst](../../role-based-access-control/conditional-access-azure-management.md) för att hantera organisationens program-och resurs åtkomst.

- Lär dig mer om Azure AD, bland annat [grundläggande licensinformation, terminologi och associerade funktioner](active-directory-whatis.md).
