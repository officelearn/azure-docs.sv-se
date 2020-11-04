---
title: Skapa FQDN för en virtuell dator i Azure Portal
description: Lär dig hur du skapar ett fullständigt kvalificerat domän namn (FQDN) för en virtuell dator i Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/03/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08d5e20bf0755a71e70a0e446cf96d33bb42ad59
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351929"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Skapa ett fullständigt kvalificerat domän namn i Azure Portal för en virtuell Linux-dator

När du skapar en virtuell dator (VM) i [Azure Portal](https://portal.azure.com)skapas automatiskt en offentlig IP-resurs för den virtuella datorn. Du använder den här IP-adressen för att fjärrans luta till den virtuella datorn. Även om portalen inte skapar ett [fullständigt kvalificerat domän namn eller fullständigt domän namn](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)kan du lägga till en när den virtuella datorn har skapats. I den här artikeln beskrivs stegen för att skapa ett DNS-namn eller fullständigt domän namn. 

## <a name="create-a-fqdn"></a>Skapa ett fullständigt domän namn
Den här artikeln förutsätter att du redan har skapat en virtuell dator. Om det behövs kan du skapa en virtuell [Linux](./linux/quick-create-portal.md) -eller [Windows](./windows/quick-create-portal.md) -dator i portalen. Följ de här stegen när den virtuella datorn är igång:


1. Välj din virtuella dator i portalen. Under **DNS-namn** väljer du **Konfigurera**.
2. Ange DNS-namnet och välj sedan **Spara** längst upp på sidan.
3. Om du vill gå tillbaka till bladet VM-översikt stänger du **konfigurations** bladet genom att välja **X** i det övre högra hörnet. 
4. Kontrol lera att *DNS-namnet* nu visas korrekt.
   



## <a name="next-steps"></a>Nästa steg
Nu när den virtuella datorn har en offentlig IP-adress och ett DNS-namn kan du distribuera vanliga program ramverk eller tjänster, till exempel nginx, MongoDB och Docker.

Du kan också läsa mer om att [använda Resource Manager](../azure-resource-manager/management/overview.md) för att få tips om hur du skapar dina Azure-distributioner.

