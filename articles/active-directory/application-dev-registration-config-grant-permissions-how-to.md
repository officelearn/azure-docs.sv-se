---
title: "Ge behörigheter till ett anpassat utvecklade program | Microsoft Docs"
description: "Ge behörigheter till ditt anpassade utvecklade program med hjälp av Azure AD-portalen eller en URL-parameter"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: c42dc18580359fc82359710258d9b87d1c4524ba
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>Ge behörigheter till ett anpassat utvecklade program

Försök stegen nedan om du vill ge medgivande förebyggande syfte på din app eller körs i ett fel som du inte har samtyckt till en app.

## <a name="how-to-perform-admin-consent-for-your-application"></a>Hur du utför Admin medgivande för ditt program

Effekten av att godkänna programmet för alla användare i din organisation har.

1. Navigera till den **App registreringar** bladet som en **global administratör**, Välj appen.

2. Välj **nödvändiga behörigheter**, och slutligen nådde den **bevilja med** längst upp på bladet.

Alternativt kan du skapa en begäran om att *login.microsoftonline.com* med konfigurationerna för appen och Lägg till på *& fråga = admin\_medgivande*. Appen har beviljats godkännande för alla användare för när de har loggat in med administratörsautentiseringsuppgifter.

## <a name="how-to-force-user-consent-for-your-application"></a>Tvinga användaren medgivande för ditt program

* Lägga till auth-begäranden *& fråga = medgivande* som kräver tillstånd varje gång de autentiserar användare.

## <a name="next-steps"></a>Nästa steg

[Medgivande och integrera appar till AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

[Medgivande och Permissioning för AzureAD v2.0 konvergerat appar](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
