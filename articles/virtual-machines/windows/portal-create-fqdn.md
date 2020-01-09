---
title: Skapa ett fullständigt domän namn för en virtuell Windows-dator i Azure Portal
description: Lär dig hur du skapar ett fullständigt kvalificerat domän namn eller fullständigt domän namn för en Resource Manager-baserad virtuell dator i Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa4e4bd7561a7a745e6eb0b70016144b9fb57998
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75371283"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Skapa ett fullständigt kvalificerat domän namn i Azure Portal för en virtuell Windows-dator

När du skapar en virtuell dator (VM) i [Azure Portal](https://portal.azure.com)skapas automatiskt en offentlig IP-resurs för den virtuella datorn. Du använder den här IP-adressen för att fjärrans luta till den virtuella datorn. Även om portalen inte skapar ett [fullständigt kvalificerat domän namn](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), eller ett fullständigt domän namn, kan du skapa en när den virtuella datorn har skapats. I den här artikeln beskrivs stegen för att skapa ett DNS-namn eller fullständigt domän namn.

## <a name="create-a-fqdn"></a>Skapa ett fullständigt domän namn
Den här artikeln förutsätter att du redan har skapat en virtuell dator. Om det behövs kan du [skapa en virtuell dator i portalen](quick-create-portal.md) eller [med Azure PowerShell](quick-create-powershell.md). Följ de här stegen när den virtuella datorn är igång:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Du kan nu fjärrans luta till den virtuella datorn med hjälp av det här DNS-namnet, till exempel för Remote Desktop Protocol (RDP).

## <a name="next-steps"></a>Nästa steg
Nu när den virtuella datorn har en offentlig IP-adress och ett DNS-namn kan du distribuera vanliga program ramverk eller tjänster som IIS, SQL eller SharePoint.

Du kan också läsa mer om att [använda Resource Manager](../../azure-resource-manager/management/overview.md) för att få tips om hur du skapar dina Azure-distributioner.

