---
title: Skapa fullständigt domännamn för en Linux-VM i Azure portal | Microsoft Docs
description: Lär dig hur du skapar ett fullständigt kvalificerat domännamn eller FQDN för en Resource Manager-baserad virtuell dator i Azure-portalen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 54d1f77823b982ee8f49122c46b0a01cb27390f2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129425"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Skapa ett fullständigt kvalificerat domännamn i Azure portal för en Linux VM

När du skapar en virtuell dator (VM) i den [Azure-portalen](https://portal.azure.com), en offentlig IP-resurs för den virtuella datorn skapas automatiskt. Du kan använda den här IP-adress för fjärråtkomst till den virtuella datorn. Även om portalen inte skapar en [fullständigt kvalificerade domännamnet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), eller FQDN, du kan lägga till en när den virtuella datorn har skapats. Den här artikeln visar hur du skapar en DNS-namn eller FQDN.

## <a name="create-a-fqdn"></a>Skapa ett fullständigt domännamn
Den här artikeln förutsätter att du redan har skapat en virtuell dator. Om det behövs kan du [skapa en virtuell dator i portalen](quick-create-portal.md) eller [med Azure CLI](quick-create-cli.md). Följ dessa steg när den virtuella datorn är igång:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Nu kan du ansluta via en fjärranslutning till den virtuella datorn med hjälp av den här DNS-namn som med `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Nästa steg
Nu när den virtuella datorn har ett offentligt IP-adress och DNS-namn, du kan distribuera vanliga programramverk eller tjänster, till exempel nginx, MongoDB, Docker, osv.

Du kan också läsa mer om [med Resource Manager](../../azure-resource-manager/resource-group-overview.md) tips om hur du bygger Azure-distributioner.

