---
title: Konfigurera automatisk avstängning av virtuella datorer i Azure Lab Services
description: Den här artikeln beskriver hur du konfigurerar automatisk avstängning av virtuella datorer i labb kontot.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 787b9aa3738335ee0261ab94253cf828c232bd44
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84896020"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>Konfigurera automatisk avstängning av virtuella datorer vid från kopplings inställning för ett labb konto
Du kan aktivera eller inaktivera automatisk avstängning av virtuella Windows Lab-datorer (mall eller student) när en anslutning till fjärr skrivbord är frånkopplad. Du kan också ange hur länge labb tjänster ska vänta tills användaren ansluter igen innan den stängs av automatiskt.

## <a name="enable-automatic-shutdown"></a>Aktivera automatisk avstängning

1. På sidan **labb konto** väljer du **labb inställningar** på den vänstra menyn.
2. Välj alternativet **Stäng virtuella datorer automatiskt när användare kopplar från** .
3. Ange hur länge labb tjänster ska vänta på att användaren återansluter innan de virtuella datorerna stängs av automatiskt.

    ![Inställning för automatisk avstängning i labb konto](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    Den här inställningen gäller för alla labb som skapats i labb kontot. En labbs kapare (lärare) kan åsidosätta den här inställningen på labb nivån. Ändringen av den här inställningen på labb kontot påverkar bara labb som skapas när ändringen har gjorts.

    Om du vill inaktivera den här inställningen avmarkerar du kryss rutan för **Automatisk avstängning av virtuella datorer när användaren kopplar från** alternativet på den här sidan. 

## <a name="next-steps"></a>Nästa steg
Information om hur en labb ägare kan konfigurera eller åsidosätta den här inställningen på labb nivån finns i [den här artikeln](how-to-enable-shutdown-disconnect.md)
