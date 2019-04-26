---
title: Inaktivera användarinloggningar för en företagsapp i Azure Active Directory | Microsoft Docs
description: Så här inaktiverar du ett företagsprogram så att inga användare kan logga in till det i Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a168c3415996ff97137c4e312348f74a48e9531b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291457"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Inaktivera användarinloggningar för en företagsapp i Azure Active Directory
Det är enkelt att inaktivera ett företagsprogram så att inga användare kan logga in till den i Azure Active Directory (AD Azure). Du behöver behörighet att hantera appen enterprise. Och du måste vara global administratör för katalogen.

## <a name="how-do-i-disable-user-sign-ins"></a>Hur jag för att inaktivera användarinloggningar?
1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
1. Välj **alla tjänster**, ange **Azure Active Directory** i textrutan och välj sedan **RETUR**.
1. På den **Azure Active Directory** -  ***directoryname*** (det vill säga Azure AD fönstret för den katalog som du hanterar), väljer **företagsprogram**.
1. På den **företagsprogram – alla program** fönstret visas en lista över appar som du kan hantera. Välj en app.
1. På den ***appname*** (det vill säga i rutan med namnet på den valda appen i rubriken) väljer **egenskaper**.
1. På den ***appname*** - **egenskaper** väljer **nr** för **aktiverad för användare att logga in?**.
1. Välj den **spara** kommando.

## <a name="next-steps"></a>Nästa steg
* [Se alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företagsapp](assign-user-or-group-access-portal.md)
* [Ta bort en användare eller grupp från en företagsapp](remove-user-or-group-access-portal.md)
* [Ändra namnet eller logotyp i en företagsapp](change-name-or-logo-portal.md)
