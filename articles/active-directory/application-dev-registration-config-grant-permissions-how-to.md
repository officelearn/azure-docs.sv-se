---
title: Ge behörigheter till ett egenutvecklat program | Microsoft Docs
description: Hur du beviljar behörigheter för ditt anpassade program med hjälp av Azure AD-portalen eller en URL-parameter
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 75beeb35b740bb126fff905f4cfa5a0b455e025e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365251"
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>Ge behörigheter till ett egenutvecklat program

Prova de här stegen nedan om du vill ge medgivande förebyggande syfte i din app eller körs på ett fel som du inte har samtyckt till en app.

## <a name="how-to-perform-admin-consent-for-your-application"></a>Hur du utför administratörens godkännande för ditt program

Detta innebär att godkänna att programmet för alla användare i din organisation.

1. Navigera till den **Appregistreringar** bladet som en **global administratör**, och välj app.

2. Välj **nödvändiga behörigheter**, och slutligen når den **bevilja behörigheter** längst upp på bladet.

Alternativt kan du skapa en begäran om att *login.microsoftonline.com* med din app-konfigurationer och lägger till på *& fråga = admin\_godkänna*. Appen har beviljats medgivande för alla användare för när du har loggat in med autentiseringsuppgifter som administratör.

## <a name="how-to-force-user-consent-for-your-application"></a>Hur du tvingar fram användargodkännande för ditt program

* Lägga till autentiseringsbegäranden *& fråga = medgivande* som slutanvändaren måste godkänna varje gång de autentiserar.

## <a name="next-steps"></a>Nästa steg

[Medgivande och integrera appar till AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

[Medgivande och ge behörighet till för AzureAD v2.0 konvergerat appar](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
