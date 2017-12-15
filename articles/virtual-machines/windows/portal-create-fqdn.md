---
title: "Skapa FQDN för en virtuell Windows-dator i Azure portal | Microsoft Docs"
description: "Lär dig hur du skapar ett fullständigt domännamn eller FQDN för Resource Manager-baserad virtuell dator i Azure-portalen."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ebc1ef89b24a9aa21f39e5b05051c16351f08cd
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Skapa ett fullständigt kvalificerat domännamn i Azure-portalen för en virtuell Windows-dator

När du skapar en virtuell dator (VM) i den [Azure-portalen](https://portal.azure.com), en offentlig IP-resurs för den virtuella datorn skapas automatiskt. Du kan använda denna IP-adress för att komma åt den virtuella datorn. Även om portalen inte skapar en [fullständigt kvalificerade domännamnet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), eller FQDN, kan du skapa en när den virtuella datorn har skapats. Den här artikeln visar hur du skapar en DNS-namn eller ett FQDN.

## <a name="create-a-fqdn"></a>Skapa ett fullständigt domännamn
Den här artikeln förutsätter att du redan har skapat en virtuell dator. Om det behövs kan du [skapa en virtuell dator i portalen](quick-create-portal.md) eller [med Azure PowerShell](quick-create-powershell.md). Följ dessa steg när den virtuella datorn är igång:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Du kan nu fjärransluta till den virtuella datorn använder den här DNS-namn som för Remote Desktop Protocol (RDP).

## <a name="next-steps"></a>Nästa steg
Nu när den virtuella datorn har ett offentligt IP-adress och DNS-namn, kan du distribuera gemensamt ramverk för programmet eller tjänster som IIS, SQL och SharePoint.

Du kan också läsa mer om [med Resource Manager](../../azure-resource-manager/resource-group-overview.md) tips om hur du skapar dina Azure-distributioner.

