---
title: Hantera program och kontroller för Azure AD-Åtkomstgranskningar | Microsoft Docs
description: Lär dig hur du skapar fler program för varje styrning, riskhantering och regelefterlevnad initiativ i din organisation kan samla och ordna Azure Active Directory-Åtkomstgranskningar som kontroller.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3056976ada6c0a0287eec608dbad230e8dbb2625
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895191"
---
# <a name="manage-programs-and-controls-for-azure-ad-access-reviews"></a>Hantera program och kontroller för Azure AD-Åtkomstgranskningar

Azure Active Directory (Azure AD) innehåller åtkomstgranskningar för gruppmedlemmar och programåtkomst. Dessa exempel på kontroller Kontrollera tillsyn för vem som har åtkomst till din organisations gruppmedlemskap och program. Organisationer kan använda dessa kontroller för att effektivt hantera sina styrning, riskhantering och efterlevnadskrav.

## <a name="create-and-manage-programs-and-their-controls"></a>Skapa och hantera program och deras kontroller
Du kan förenkla spåra och samla in åtkomstgranskningar för olika ändamål genom att ordna dem i program. Varje åtkomstgranskning kan länkas till ett program. Sedan när du förbereder rapporter för en granskare kan du fokusera på åtkomstgranskningar inom omfånget för en viss initiativ.  Program och åtkomstgranskningsresultaten är synliga för användare med Global administratör, Användaradministratör, säkerhetsadministratör eller säkerhetsläsarroll.

Om du vill se en lista över program går du till den [sidan med åtkomstgranskningar](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) och välj **program**.

**Standardprogrammet** finns alltid. Om du arbetar i en global administratör eller användarrollen administratör, kan du skapa ytterligare program. Du kan till exempel välja att ha ett program för varje efterlevnad eller företagsmål.

Om du inte längre behöver ett program och den inte redan har alla kontroller som är länkad till den, kan du ta bort den.

## <a name="next-steps"></a>Nästa steg

- [Skapa en åtkomstgranskning av grupper eller program](create-access-review.md)
- [Hämta åtkomstgranskningsresultat för grupper eller program](retrieve-access-review.md)
