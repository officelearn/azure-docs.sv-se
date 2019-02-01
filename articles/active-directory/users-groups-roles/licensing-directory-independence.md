---
title: Egenskaper för flera interaktion - Azure Active Directory-klient | Microsoft Docs
description: Hantera dina Azure Active-klient-klienter genom att förstå klienterna som helt oberoende resurser
services: active-tenant
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 01/31/2019
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.openlocfilehash: 6dcc02d8d4c1f0629d69460be28a70c470704d75
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55511385"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Förstå hur flera Azure Active Directory-klienter interagerar

I Azure Active Directory (AD Azure), varje klient är en helt oberoende resurser: en peer-dator som är logiskt oberoende av andra klienter som du hanterar. Det finns ingen överordnad-underordnad-relation mellan klienter. Detta oberoende mellan klienter innehåller resursoberoende, administrativt oberoende och synkroniseringsoberoende.

## <a name="resource-independence"></a>Resursoberoende
* Om du skapar eller tar bort en resurs i en klient har ingen inverkan på alla resurser i en annan klient, delvis med undantag av externa användare. 
* Om du använder en av dina domännamn med en klient kan inte användas med andra innehavare.

## <a name="administrative-independence"></a>Administrativt oberoende
Om en icke-administrativa användare för klienten ”Contoso” skapar en Testklient 'Test' sedan:

* Som standard är den användare som skapar en klient har lagts till som en extern användare i den nya klienten och har tilldelats rollen global administratör i den klienten.
* Administratörerna av klient ”Contoso” har inte någon direkt administratörsbehörighet till klient ”Test”, om inte en administratör för 'Test' uttryckligen beviljar dem dessa privilegier. Dock kan ”Contoso”-administratörerna styra åtkomsten till klient ”Test” om de kontrollera att användarkontot som skapade ”Test”.
* Om du lägger till/ta bort en administratörsroll för en användare i en klient, påverkar inte ändringen administratörsroller som användaren har i en annan klient.

## <a name="synchronization-independence"></a>Synkroniseringsoberoende
Du kan konfigurera varje Azure AD-klient oberoende om du vill synkronisera data från en enda instans av antingen:

* Verktyget Azure AD Connect att synkronisera data med en enda AD-skog.
* Azure Active innehavaren Connector för Forefront Identity Manager för att synkronisera data med en eller flera lokala skogar och/eller icke-Azure AD-datakällor.

## <a name="add-an-azure-ad-tenant"></a>Lägg till en Azure AD-klient
Om du vill lägga till en Azure AD-klient i Azure-portalen, logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är en global Azure AD-administratör, och till vänster, Välj **New**.

> [!NOTE]
> Till skillnad från andra Azure-resurser är dina klienter inte underordnade resurser till en Azure-prenumeration. Om din Azure-prenumeration avbryts eller upphört att gälla, kan du fortfarande använda klientorganisationens data med hjälp av Azure PowerShell, Azure Graph API eller Office 365 Admin Center. Du kan också [associera en annan prenumeration med klient](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Nästa steg
En översikt av Azure AD licensproblem och bästa praxis, se [vad är Azure Active-klient-licensiering?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
