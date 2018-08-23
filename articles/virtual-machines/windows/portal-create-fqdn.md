---
title: Skapa fullständigt domännamn för en virtuell Windows-dator i Azure portal | Microsoft Docs
description: Lär dig hur du skapar ett fullständigt kvalificerat domännamn eller FQDN för en Resource Manager-baserad virtuell dator i Azure-portalen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 885003863b8d5a5a81adc7f0310bbf2238edc68e
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42059247"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Skapa ett fullständigt kvalificerat domännamn i Azure portal för en virtuell Windows-dator

När du skapar en virtuell dator (VM) i den [Azure-portalen](https://portal.azure.com), en offentlig IP-resurs för den virtuella datorn skapas automatiskt. Du kan använda den här IP-adress för fjärråtkomst till den virtuella datorn. Även om portalen inte skapar en [fullständigt kvalificerade domännamnet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), eller FQDN, du kan skapa en när den virtuella datorn har skapats. Den här artikeln visar hur du skapar en DNS-namn eller FQDN.

## <a name="create-a-fqdn"></a>Skapa ett fullständigt domännamn
Den här artikeln förutsätter att du redan har skapat en virtuell dator. Om det behövs kan du [skapa en virtuell dator i portalen](quick-create-portal.md) eller [med Azure PowerShell](quick-create-powershell.md). Följ dessa steg när den virtuella datorn är igång:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Nu kan du ansluta via en fjärranslutning till den virtuella datorn använder den här DNS-namn som för Remote Desktop Protocol (RDP).

## <a name="next-steps"></a>Nästa steg
Nu när den virtuella datorn har ett offentligt IP-adress och DNS-namn, kan du distribuera vanliga programramverk eller tjänster som IIS, SQL eller SharePoint.

Du kan också läsa mer om [med Resource Manager](../../azure-resource-manager/resource-group-overview.md) tips om hur du bygger Azure-distributioner.

