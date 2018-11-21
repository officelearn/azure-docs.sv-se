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
ms.date: 11/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 4cfd3c38631778373e99872fd305d8316cdadb75
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274746"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Hantera åtkomst till Azure-hantering med villkorlig åtkomst

Villkorlig åtkomst i Azure Active Directory (Azure AD) styr åtkomsten till molnet appar baserat på specifika villkor som du anger. Om du vill tillåta åtkomst måste skapa du principer för villkorlig åtkomst som tillåter eller blockerar åtkomst baserat på huruvida kraven i principen är uppfyllda. 

Normalt använder du villkorlig åtkomst för att styra åtkomsten till dina molnappar. Du kan också ställa in principer för att styra åtkomsten till hantering av Azure baserat på vissa villkor (till exempel inloggningsrisk, plats eller enhet) och för att framtvinga krav som multifaktorautentisering.

Om du vill skapa en princip för hantering av Azure kan du välja **Microsoft Azure Management** under **Molnappar** när du väljer den app som principen ska tillämpas på.

![Villkorlig åtkomst för hantering av Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Du skapar principen gäller för alla Azure-hanteringsslutpunkter, inklusive klassiska Azure-portalen, Azure-portalen, Azure Resource Manager-providern, klassiska Service Management API: er och Azure PowerShell. Observera att principen gäller för Azure PowerShell, som anropar API: et för Azure Resource Manager. Den gäller inte för [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), som anropar Microsoft Graph.

> [!CAUTION]
> Kontrollera att du förstår hur villkorlig åtkomst fungerar innan du konfigurerar en princip för att hantera åtkomst till Azure-hantering. Kontrollera att du inte skapa villkor som kan blockera egna åtkomst till portalen.

Mer information om hur du ställer in och använda villkorlig åtkomst finns i [villkorlig åtkomst i Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).