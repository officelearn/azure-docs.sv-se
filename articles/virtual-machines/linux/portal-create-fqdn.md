---
title: Skapa FQDN för en virtuell dator i Azure-portalen
description: Lär dig hur du skapar ett fullständigt kvalificerat domännamn, eller FQDN, för en Resource Manager-baserad virtuell dator i Azure-portalen.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f51b9ecf8e300af6b1e3f11d8431de7a282ab342
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759346"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Skapa ett fullständigt kvalificerat domännamn i Azure-portalen för en Virtuell Linux-dator

När du skapar en virtuell dator (VM) i [Azure-portalen](https://portal.azure.com)skapas en offentlig IP-resurs för den virtuella datorn automatiskt. Du använder den här IP-adressen för att fjärransluta till den virtuella datorn. Även om portalen inte skapar ett [fullständigt kvalificerat domännamn](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)eller FQDN kan du lägga till ett när den virtuella datorn har skapats. Den här artikeln visar stegen för att skapa ett DNS-namn eller FQDN.

## <a name="create-a-fqdn"></a>Skapa ett FQDN
Den här artikeln förutsätter att du redan har skapat en virtuell dator. Om det behövs kan du [skapa en virtuell dator i portalen](quick-create-portal.md) eller med Azure [CLI](quick-create-cli.md). Följ dessa steg när den virtuella datorn är igång:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Du kan nu fjärransluta till den virtuella `ssh azureuser@mydns.westus.cloudapp.azure.com`datorn med det här DNS-namnet, till exempel med .

## <a name="next-steps"></a>Nästa steg
Nu när den virtuella datorn har ett offentligt IP- och DNS-namn kan du distribuera vanliga programramverk eller -tjänster som nginx, MongoDB, Docker osv.

Du kan också läsa mer om [hur du använder Resource Manager](../../azure-resource-manager/management/overview.md) för tips om hur du skapar dina Azure-distributioner.

