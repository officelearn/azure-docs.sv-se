---
title: Egenskaper för flera klientinteraktioner – Azure AD | Microsoft-dokument
description: Hantera dina Azure Active-klientklienter genom att förstå dina klienter som helt oberoende resurser
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562266"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Förstå hur flera Azure Active Directory-klienter interagerar

I Azure Active Directory (Azure AD) är varje klient en helt oberoende resurs: en peer som logiskt är oberoende av de andra klienter som du hanterar. Det finns ingen relation mellan klienter som är överordnade och underordnade. Detta oberoende mellan klienter inkluderar resursoberoende, administrativt oberoende och synkroniseringsoberoende.

## <a name="resource-independence"></a>Resursoberoende
* Om du skapar eller tar bort en resurs i en klient har den ingen inverkan på någon resurs i en annan klient, med partiellt undantag för externa användare. 
* Om du använder ett av dina domännamn hos en klient kan det inte användas med någon annan klient.

## <a name="administrative-independence"></a>Administrativt oberoende
Om en icke-administrativ användare av klienten "Contoso" skapar en testklientens testklient

* Som standard läggs användaren som skapar en klient till som en extern användare i den nya klienten och tilldelas den globala administratörsrollen i den klienten.
* Administratörerna av klienten "Contoso" har inga direkta administrativa privilegier till klienten "Test", om inte en administratör för "Test" specifikt ger dem dessa privilegier. Administratörer av "Contoso" kan dock styra åtkomsten till klientens "Test" om de styr användarkontot som skapade "Test".
* Om du lägger till/tar bort en administratörsroll för en användare i en klient påverkar ändringen inte de administratörsroller som användaren har i en annan klientorganisation.

## <a name="synchronization-independence"></a>Oberoende för synkronisering
Du kan konfigurera varje Azure AD-klient oberoende av varandra för att få data synkroniserade från en enda instans av antingen:

* Azure AD Connect-verktyget för att synkronisera data med en enda AD-skog.
* Azure Active tenant Connector for Forefront Identity Manager för att synkronisera data med en eller flera lokala skogar och/eller icke-Azure AD-datakällor.

## <a name="add-an-azure-ad-tenant"></a>Lägga till en Azure AD-klient
Om du vill lägga till en Azure AD-klient i Azure-portalen loggar du in på [Azure-portalen](https://portal.azure.com) med ett konto som är en global Azure AD-administratör och till vänster väljer **Ny**.

> [!NOTE]
> Till skillnad från andra Azure-resurser är dina klienter inte underordnade resurser för en Azure-prenumeration. Om din Azure-prenumeration har avbrutits eller upphört att gälla kan du fortfarande komma åt dina klientdata med Azure PowerShell, Microsoft Graph API eller Microsoft 365 admincenter. Du kan också [associera en annan prenumeration med klienten](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Nästa steg
En bred översikt över Azure AD-licensieringsproblem och metodtips finns i [Vad är Azure Active-klientlicensiering?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
