---
title: Förstå delade IP-adresser i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur Azure DevTest Labs använder delade IP-adresser för att minimera de offentliga IP-adresser som krävs för att komma åt ditt labb virtuella datorer.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2019
ms.author: spelluru
ms.openlocfilehash: f7c9feedddab1aea031cb3a8879e868aae04df00
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236894"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Förstå delade IP-adresser i Azure DevTest Labs

Azure DevTest Labs kan lab virtuella datorer som delar samma offentliga IP-adress för att minimera antalet offentliga IP-adresser som krävs för att få åtkomst till labbet enskilda virtuella datorer.  Den här artikeln beskrivs hur delade IP-adresser arbete och deras relaterade konfigurationsalternativ.

## <a name="shared-ip-setting"></a>Delade IP-inställning

När du skapar ett labb, skapas den i ett undernät för ett virtuellt nätverk.  Som standard skapas det här undernätet med **aktivera delad offentlig IP-adress** inställd *Ja*.  Den här konfigurationen skapar en offentlig IP-adress för hela undernätet.  Läs mer om hur du konfigurerar virtuella nätverk och undernät, [konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nytt labb-undernät](media/devtest-lab-shared-ip/lab-subnet.png)

För befintliga labb kan du aktivera det här alternativet genom att välja **konfiguration och principer > virtuella nätverk**. Välj ett virtuellt nätverk i listan och välj sedan **aktivera DELAD offentlig IP-adress** för ett valt undernät. Du kan också inaktivera det här alternativet i alla lab om du inte vill dela en offentlig IP-adress i labbet virtuella datorer.

Virtuella datorer som skapats i den här övningen standard till att använda en delad IP-adress.  När du skapar den virtuella datorn kan den här inställningen kan observeras i den **avancerade inställningar** sidan **IP-adresskonfigurationen**.

![Ny virtuell dator](media/devtest-lab-shared-ip/new-vm.png)

- **Delad:** Alla virtuella datorer som skapas som **delad** placeras i en resursgrupp (RG). En IP-adress tilldelas för som RG och alla virtuella datorer i RG använder IP-adress.
- **Offentlig:** Varje virtuell dator som du skapar har sin egen IP-adress och skapas i en egen resursgrupp.
- **Privata:** Varje virtuell dator skapar du använder en privat IP-adress. Du kan inte ansluta till den här virtuella datorn direkt från internet via ett fjärrskrivbord.

När en virtuell dator med delade IP är aktiverat läggs till i undernätet, lägger till den virtuella datorn till en belastningsutjämnare automatiskt i DevTest Labs och tilldelar en TCP-portnumret på den offentliga IP-adressen, vidarebefordran till RDP-porten på den virtuella datorn.  

## <a name="using-the-shared-ip"></a>Använder den delade IP-Adressen

- **Linux-användare:** SSH till den virtuella datorn med hjälp av IP-adress eller fullständigt kvalificerade domännamn, följt av ett kolon följt av porten. I bilden nedan, RDP-adress att koppla till den virtuella datorn är exempelvis `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661`.

  ![VM-exempel](media/devtest-lab-shared-ip/vm-info.png)

- **Windows-användare:** Välj den **Connect** knappen på Azure portal för att hämta en förkonfigurerad RDP-fil och få åtkomst till den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* [Definiera labbprinciper i Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md)





