---
title: Hantera åtkomst till Azure-hantering med villkorlig åtkomst i Azure Active Directory
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
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 4ee58835a5055f5382cefa3a049424ffe97a01c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122949"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Hantera åtkomst till Azure-hantering med villkorlig åtkomst

Villkorlig åtkomst i Azure Active Directory (Azure AD) styr åtkomsten till molnet appar baserat på specifika villkor som du anger. Om du vill tillåta åtkomst måste skapa du principer för villkorlig åtkomst som tillåter eller blockerar åtkomst baserat på huruvida kraven i principen är uppfyllda. 

Normalt använder du villkorlig åtkomst för att kontrollera åtkomsten till dina appar i molnet. Du kan också ställa in principer för att styra åtkomsten till hantering av Azure baserat på vissa villkor (till exempel inloggningsrisk, plats eller enhet) och för att framtvinga krav som multifaktorautentisering.

Om du vill skapa en princip för hantering av Azure kan du välja **Microsoft Azure Management** under **Molnappar** när du väljer den app som principen ska tillämpas på.

![Villkorlig åtkomst för hantering av Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Du skapar principen gäller för alla Azure-hanteringsslutpunkter, inklusive Azure-portalen, Azure Resource Manager-providern, klassiska Service Management API: er, Azure PowerShell och Visual Studio-prenumerationer administratörsportalen. Observera att principen gäller för Azure PowerShell, som anropar API: et för Azure Resource Manager. Den gäller inte för [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), som anropar Microsoft Graph.

> [!CAUTION]
> Kontrollera att du förstår hur villkorlig åtkomst fungerar innan du konfigurerar en princip för att hantera åtkomst till Azure-hantering. Kontrollera att du inte skapa villkor som kan blockera egna åtkomst till portalen.

Mer information om hur du ställer in och använda villkorlig åtkomst finns i [villkorlig åtkomst i Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).