---
title: Skapa FQDN för en virtuell dator i Azure Portal
description: Lär dig hur du skapar ett fullständigt kvalificerat domän namn eller fullständigt domän namn för en Resource Manager-baserad virtuell dator i Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f51b9ecf8e300af6b1e3f11d8431de7a282ab342
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759346"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Skapa ett fullständigt kvalificerat domän namn i Azure Portal för en virtuell Linux-dator

När du skapar en virtuell dator (VM) i [Azure Portal](https://portal.azure.com)skapas automatiskt en offentlig IP-resurs för den virtuella datorn. Du använder den här IP-adressen för att fjärrans luta till den virtuella datorn. Även om portalen inte skapar ett [fullständigt kvalificerat domän namn eller fullständigt domän namn](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)kan du lägga till en när den virtuella datorn har skapats. I den här artikeln beskrivs stegen för att skapa ett DNS-namn eller fullständigt domän namn.

## <a name="create-a-fqdn"></a>Skapa ett fullständigt domän namn
Den här artikeln förutsätter att du redan har skapat en virtuell dator. Om det behövs kan du [skapa en virtuell dator i portalen](quick-create-portal.md) eller [med Azure CLI](quick-create-cli.md). Följ de här stegen när den virtuella datorn är igång:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Du kan nu fjärrans luta till den virtuella datorn med hjälp av det här DNS `ssh azureuser@mydns.westus.cloudapp.azure.com`-namnet, till exempel med.

## <a name="next-steps"></a>Nästa steg
Nu när den virtuella datorn har en offentlig IP-adress och ett DNS-namn kan du distribuera vanliga program ramverk eller tjänster, till exempel nginx, MongoDB, Docker osv.

Du kan också läsa mer om att [använda Resource Manager](../../azure-resource-manager/management/overview.md) för att få tips om hur du skapar dina Azure-distributioner.

