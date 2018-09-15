---
title: Hantera program och kontroller för Azure AD-åtkomstgranskningar | Microsoft Docs
description: Du kan skapa ytterligare program för varje styrning, riskhantering och regelefterlevnad initiativ i din organisation kan samla och ordna Azure Active Directory-åtkomstgranskningar som kontroller.
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
ms.component: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: b62a150160daa1d6708dbf5edaa6772688e2ffa1
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45607817"
---
# <a name="manage-programs-and-their-controls"></a>Hantera program och deras kontroller 

Azure Active Directory (Azure AD) innehåller åtkomstgranskningar för gruppmedlemmar och programåtkomst. Dessa exempel på kontroller Kontrollera tillsyn för vem som har åtkomst till din organisations gruppmedlemskap och program. Organisationer kan använda dessa kontroller för att effektivt hantera sina styrning, riskhantering och efterlevnadskrav.

## <a name="create-and-manage-programs-and-their-controls"></a>Skapa och hantera program och deras kontroller
Du kan förenkla spåra och samla in åtkomstgranskningar för olika ändamål genom att ordna dem i program. Varje åtkomstgranskning kan länkas till ett program. Sedan när du förbereder rapporter för en granskare kan du fokusera på åtkomstgranskningar inom omfånget för en viss initiativ.  Program och åtkomstgranskningsresultaten är synliga för användare i rollen Global administratör, användarkonto-administratör, säkerhetsadministratör eller Säkerhetsläsare.

Om du vill se en lista över program går du till den [sidan med åtkomstgranskningar](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) och välj **program**.

**Standardprogrammet** finns alltid. Om du arbetar i en global administratör eller användarrollen kontot Administratör, kan du skapa ytterligare program. Du kan till exempel välja att ha ett program för varje efterlevnad eller företagsmål.

Om du inte längre behöver ett program och den inte redan har alla kontroller som är länkad till den, kan du ta bort den.

## <a name="next-steps"></a>Nästa steg

- [Skapa en åtkomstgranskning för medlemmar i en grupp eller åtkomst till ett program](create-access-review.md)
- [Hämta resultaten för en åtkomstgranskning](retrieve-access-review.md)
