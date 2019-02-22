---
title: Lägga till användare för Azure Stack ADFS | Microsoft Docs
description: Lär dig hur du lägger till användare för AD FS-distributioner av Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 1b47739200c79317273ea0c788f21a7ee4a3b818
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648514"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Lägg till Azure Stack-användare i AD FS
Du kan använda den **Active Directory-användare och datorer** snapin-modulen lägga till ytterligare användare till en Azure Stack-miljö som utnyttjar AD FS som identitetsleverantör.

## <a name="add-windows-server-active-directory-users"></a>Lägga till Windows Server Active Directory-användare
> [!TIP]
> Det här exemplet använder standard azurestack.local ASDK active directory. 

1. Logga in på en dator med ett konto som ger åtkomst till Windows Administrationsverktyg och öppna en ny Microsoft Management Console (MMC).
2. Välj **fil > Lägg till eller ta bort snapin-modul**.
3. Välj **Active Directory-användare och datorer** > **AzureStack.local** > **användare**.
4. Välj **åtgärd** > **nya** > **användaren**.
5. I nytt objekt – användare, ger detaljerad information för användaren. Välj **Nästa**.
6. Ange och bekräfta ett lösenord.
7. Välj **nästa** att slutföra värdena. Välj **Slutför** att skapa användaren.


## <a name="next-steps"></a>Nästa steg
[Skapa tjänsthuvudnamn](azure-stack-create-service-principals.md)