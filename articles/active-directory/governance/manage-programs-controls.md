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
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78f4c978a04a7589e71ba39676e06ca5cc37f579
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170677"
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
