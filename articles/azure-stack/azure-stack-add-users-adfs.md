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
ms.date: 10/15/2018
ms.author: patricka
ms.reviewer: unknown
ms.openlocfilehash: ee571ec14a93653b524401d1d304021178ecd794
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120820"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Lägg till Azure Stack-användare i AD FS
Du kan använda den **Active Directory-användare och datorer** snapin-modulen lägga till ytterligare användare till en Azure Stack-miljö som utnyttjar AD FS som identitetsleverantör.

## <a name="add-windows-server-active-directory-users"></a>Lägga till Windows Server Active Directory-användare
> [!TIP]
> Det här exemplet använder standard azurestack.local ASDK active directory. 

1.  Logga in på en dator med ett konto som ger åtkomst till Windows Administrationsverktyg och öppna en ny Microsoft Management Console (MMC).
2.  Klicka på **fil > Lägg till eller ta bort snapin-modul**.
3.  Välj **Active Directory-användare och datorer** > **AzureStack.local** > **användare**.
4.  Klicka på **åtgärd** > **nya** > **användaren**.
5.  I nytt objekt – användare och ange och bekräfta ett lösenord
6.  Klicka på **nästa** att slutföra värdena och klicka på Slutför för att skapa användaren.


## <a name="next-steps"></a>Nästa steg
[Skapa tjänsthuvudnamn](azure-stack-create-service-principals.md)