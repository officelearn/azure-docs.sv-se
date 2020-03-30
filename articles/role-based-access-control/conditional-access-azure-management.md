---
title: Hantera åtkomst till Azure-hantering med villkorlig åtkomst i Azure AD
description: Lär dig mer om hur du använder villkorlig åtkomst i Azure AD för att hantera åtkomst till Azure-hantering.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: f3341f1c30a1581b8507652c322c00581e3972aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137416"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Hantera åtkomst till Azure-hantering med villkorad åtkomst

> [!CAUTION]
> Se till att du förstår hur villkorlig åtkomst fungerar innan du konfigurerar en princip för att hantera åtkomst till Azure-hantering. Se till att du inte skapar villkor som kan blockera din egen åtkomst till portalen.

Villkorlig åtkomst i Azure Active Directory (Azure AD) styr åtkomsten till molnappar baserat på specifika villkor som du anger. Om du vill tillåta åtkomst skapar du principer för villkorlig åtkomst som tillåter eller blockerar åtkomst baserat på om kraven i principen är uppfyllda eller inte. 

Vanligtvis använder du villkorlig åtkomst för att styra åtkomsten till dina molnappar. Du kan också ställa in principer för att styra åtkomsten till Azure-hantering baserat på vissa villkor (till exempel inloggningsrisk, plats eller enhet) och för att tillämpa krav som multifaktorautentisering.

Om du vill skapa en princip för Azure-hantering väljer du **Microsoft Azure Management** under **Molnappar** när du väljer den app som principen ska tillämpas på.

![Villkorlig åtkomst för hantering av Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Principen som du skapar gäller för alla Slutpunkter för Azure-hantering, inklusive följande:

- Azure Portal
- Azure Resource Manager-provider
- Api:er för klassisk tjänsthantering
- Azure PowerShell
- Visual Studio-prenumerationsadministratörsportal
- Azure DevOps
- Azure Data Factory-portal

Observera att principen gäller för Azure PowerShell, som anropar Azure Resource Manager API. Det gäller inte [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), som anropar Microsoft Graph.


Mer information om hur du konfigurerar och använder villkorlig åtkomst finns [i Villkorlig åtkomst i Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).
