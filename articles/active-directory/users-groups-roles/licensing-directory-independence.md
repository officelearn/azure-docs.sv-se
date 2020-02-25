---
title: Egenskaper för flera klient interaktioner – Azure AD | Microsoft Docs
description: Hantera dina klient organisationer för Azure Active-klienter genom att förstå dina klienter som helt oberoende resurser
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4eb09ab7fa31af5edf14b113a6a88e08df2d115
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562266"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Förstå hur flera Azure Active Directory klienter interagerar

I Azure Active Directory (Azure AD) är varje klient är en helt oberoende resurs: en peer som är logiskt oberoende av de andra klienter som du hanterar. Det finns ingen överordnad-underordnad relation mellan klienter. Detta oberoende mellan klienterna omfattar resurs oberoende, administrativt oberoende och oberoende oberoende.

## <a name="resource-independence"></a>Resurs oberoende
* Om du skapar eller tar bort en resurs i en klient har den ingen påverkan på någon resurs i en annan klient organisation, med undantag för externa användare. 
* Om du använder ett av dina domän namn med en klient kan den inte användas med någon annan klient.

## <a name="administrative-independence"></a>Administrativt oberoende
Om en icke-administrativ användare av klienten contoso skapar en test klient "test":

* Som standard läggs den användare som skapar en klient organisation till som en extern användare i den nya klienten och tilldelas rollen global administratör i klienten.
* Administratörerna för klient organisationen contoso har ingen direkt administratörs behörighet till klientens "test", såvida inte en administratör av "test" specifikt tilldelar dem dessa privilegier. Administratörer av contoso kan dock kontrol lera åtkomsten till klientens "test" om de styr det användar konto som skapade "test".
* Om du lägger till/tar bort en administratörs roll för en användare i en klient, påverkar inte ändringen administratörs rollerna som användaren har i en annan klient.

## <a name="synchronization-independence"></a>Oberoende synkronisering
Du kan konfigurera varje Azure AD-klient oberoende för att hämta data som synkroniseras från en enda instans av antingen:

* Azure AD Connect-verktyget, för att synkronisera data med en enda AD-skog.
* Azure Active Tenant Connector för Forefront Identity Manager, för att synkronisera data med en eller flera lokala skogar och/eller icke-Azure AD-datakällor.

## <a name="add-an-azure-ad-tenant"></a>Lägg till en Azure AD-klient
Om du vill lägga till en Azure AD-klient i Azure Portal loggar du in på [Azure Portal](https://portal.azure.com) med ett konto som är en global Azure AD-administratör och väljer sedan **nytt**till vänster.

> [!NOTE]
> Till skillnad från andra Azure-resurser är klient organisationerna inte underordnade resurser till en Azure-prenumeration. Om din Azure-prenumeration har avbrutits eller har upphört att gälla, kan du fortfarande komma åt dina klient data med hjälp av Azure PowerShell, Microsoft Graph API eller Microsoft 365 administrations Center. Du kan också [associera en annan prenumeration med klienten](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Nästa steg
En översikt över Azure AD-licensierings problem och bästa praxis finns i [Vad är Azure Active Tenant Licensing?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
