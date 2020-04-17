---
title: Skapa FQDN för en Virtuell Windows-dator i Azure-portalen
description: Lär dig hur du skapar ett fullständigt kvalificerat domännamn, eller FQDN, för en Resource Manager-baserad virtuell dator i Azure-portalen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c7bfa510068d71afb7701ab8964f06053d38ac70
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458904"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Skapa ett fullständigt kvalificerat domännamn i Azure-portalen för en Windows VM

När du skapar en virtuell dator (VM) i [Azure-portalen](https://portal.azure.com)skapas en offentlig IP-resurs för den virtuella datorn automatiskt. Du använder den här IP-adressen för att fjärransluta till den virtuella datorn. Även om portalen inte skapar ett [fullständigt kvalificerat domännamn](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)eller FQDN kan du skapa ett när den virtuella datorn har skapats. Den här artikeln visar stegen för att skapa ett DNS-namn eller FQDN.

## <a name="create-a-fqdn"></a>Skapa ett FQDN
Den här artikeln förutsätter att du redan har skapat en virtuell dator. Om det behövs kan du [skapa en virtuell dator i portalen](quick-create-portal.md) eller med Azure [PowerShell](quick-create-powershell.md). Följ dessa steg när den virtuella datorn är igång:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Du kan nu fjärransluta till den virtuella datorn med det här DNS-namnet, till exempel för RDP (Remote Desktop Protocol).

## <a name="next-steps"></a>Nästa steg
Nu när den virtuella datorn har ett offentligt IP- och DNS-namn kan du distribuera vanliga programramverk eller -tjänster som IIS, SQL eller SharePoint.

Du kan också läsa mer om [hur du använder Resource Manager](../../azure-resource-manager/management/overview.md) för tips om hur du skapar dina Azure-distributioner.

