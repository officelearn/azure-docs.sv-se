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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2017
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: dcf701c15ee20b7d9aeea65f3d06b794006a5202
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203938"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Hantera åtkomst till Azure-hantering med villkorlig åtkomst

Villkorlig åtkomst i Azure Active Directory (AD Azure) kontrollerar åtkomst till molnappar baserat på specifika villkor som du anger. För att tillåta åtkomst kan du skapa principer för villkorlig åtkomst som tillåter eller blockerar åtkomst baserat på om huruvida kraven i principen är uppfyllda. 

Normalt använder du villkorlig åtkomst för att styra åtkomsten till dina molnappar. Du kan också konfigurera principer för att styra åtkomsten till hantering av Azure baserat på vissa villkor (till exempel inloggning risk, plats eller enhet) och att tillämpa krav som multifaktorautentisering.

Om du vill skapa en princip för hantering av Azure kan du välja **Microsoft Azure Management** under **Molnappar** när du väljer den app som du vill tillämpa principen.

![Villkorlig åtkomst för hantering av Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Den princip som du skapar gäller för alla slutpunkter i Azure-hantering, inklusive klassiska Azure-portalen, Azure-portalen, Azure Resource Manager-providern, klassiska Service Management API: er och Azure PowerShell.

> [!CAUTION]
> Kontrollera att du förstår hur villkorlig åtkomst fungerar innan du konfigurerar en princip för att hantera åtkomst till Azure-hantering. Kontrollera att du inte skapa villkor som kan blockera egna åtkomst till portalen.

Mer information om hur du ställer in och använda villkorlig åtkomst finns [villkorlig åtkomst i Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).