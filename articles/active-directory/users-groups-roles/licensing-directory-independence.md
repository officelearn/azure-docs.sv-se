---
title: Egenskaper för flera klientinteraktioner – Azure AD | Microsoft-dokument
description: Förstå dina Azure Active Directory-klienter som helt oberoende organisationer
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/07/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175d9ce7db1657e0e654f46adaf8a8d8ef28c25e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878127"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Förstå hur flera Azure Active Directory-organisationer interagerar

I Azure Active Directory (Azure AD) är varje klient en helt oberoende organisation: en peer som logiskt är oberoende av de andra Azure AD-organisationer som du hanterar. Detta oberoende mellan organisationer inkluderar resursoberoende, administrativt oberoende och synkroniseringsoberoende. Det finns ingen överordnad och underordnad relation mellan organisationer.

## <a name="resource-independence"></a>Resursoberoende

* Om du skapar eller tar bort en Azure AD-resurs i en organisation har den ingen inverkan på någon resurs i en annan organisation, med partiellt undantag för externa användare.
* Om du registrerar ett av dina domännamn hos en organisation kan det inte användas av någon annan organisation.

## <a name="administrative-independence"></a>Administrativt oberoende

Om en icke-administrativ användare av organisationen "Contoso" skapar en testorganisation "Test" så:

* Som standard läggs användaren som skapar en organisation till som en extern användare i den nya organisationen och tilldelas den globala administratörsrollen i den organisationen.
* Administratörerna i organisationen "Contoso" har inga direkta administrativa privilegier till organisationen "Test", om inte en administratör för "Test" specifikt ger dem dessa privilegier. Administratörer av "Contoso" kan dock styra åtkomsten till organisationen "Test" om de styr användarkontot som skapade "Test".
* Om du lägger till eller tar bort en Azure AD-roll för en användare i en organisation påverkar ändringen inte de roller som användaren tilldelas i någon annan Azure AD-organisation.

## <a name="synchronization-independence"></a>Oberoende för synkronisering

Du kan konfigurera varje Azure AD-organisation oberoende av varandra för att synkronisera data från en enda instans av antingen:

* Azure AD Connect-verktyget för att synkronisera data med en enda AD-skog.
* Azure Active Directory Connector för Forefront Identity Manager för att synkronisera data med en eller flera lokala skogar och/eller icke-Azure AD-datakällor.

## <a name="add-an-azure-ad-organization"></a>Lägga till en Azure AD-organisation

Om du vill lägga till en Azure AD-organisation i Azure-portalen loggar du in [på Azure-portalen](https://portal.azure.com) med ett konto som är en global Azure AD-administratör och väljer **Ny**.

> [!NOTE]
> Till skillnad från andra Azure-resurser är dina Azure AD-organisationer inte underordnade resurser för en Azure-prenumeration. Om din Azure-prenumeration har avbrutits eller upphört att gälla kan du fortfarande komma åt Azure AD-organisationens data med Azure PowerShell, Microsoft Graph API eller Microsoft 365-administrationscentret. Du kan också [associera en annan prenumeration med organisationen](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Nästa steg

Information om Azure AD-licensiering och metodtips finns i [Vad är Azure Active Directory-licensiering?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
