---
title: Förstå delade IP-adresser i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur Azure DevTest Labs använder delade IP-adresser för att minimera de offentliga IP-adresser som krävs för att komma åt dina virtuella labb-datorer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65236894"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Förstå delade IP-adresser i Azure DevTest Labs

Azure DevTest Labs låter virtuella labb-datorer dela samma offentliga IP-adress för att minimera antalet offentliga IP-adresser som krävs för att komma åt dina enskilda virtuella labb-datorer.  I den här artikeln beskrivs hur delade IPs fungerar och deras relaterade konfigurationsalternativ.

## <a name="shared-ip-setting"></a>Delad IP-inställning

När du skapar ett labb skapas det i ett undernät till ett virtuellt nätverk.  Som standard skapas det här undernätet med **Aktivera delad offentlig IP-uppsättning** till *Ja*.  Den här konfigurationen skapar en offentlig IP-adress för hela undernätet.  Mer information om hur du konfigurerar virtuella nätverk och undernät finns [i Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nytt labbundernät](media/devtest-lab-shared-ip/lab-subnet.png)

För befintliga övningar kan du aktivera det här alternativet genom att välja **Konfiguration och principer > virtuella nätverk**. Välj sedan ett virtuellt nätverk i listan och välj **AKTIVERA DELAD OFFENTLIG IP** för ett valt undernät. Du kan också inaktivera det här alternativet i valfritt labb om du inte vill dela en offentlig IP-adress över labb-virtuella datorer.

Alla virtuella datorer som skapas i det här labbet som standard använder en delad IP.  När du skapar den virtuella datorn kan den här inställningen observeras på sidan **Avancerade inställningar** under **IP-adresskonfiguration**.

![Ny virtuell dator](media/devtest-lab-shared-ip/new-vm.png)

- **Delad:** Alla virtuella datorer som **skapas** som delade placeras i en resursgrupp (RG). En enda IP-adress tilldelas för den RG och alla virtuella datorer i RG kommer att använda den IP-adressen.
- **Allmänheten:** Varje virtuell dator som du skapar har sin egen IP-adress och skapas i en egen resursgrupp.
- **Privat:** Varje virtuell dator som du skapar använder en privat IP-adress. Du kan inte ansluta till den här virtuella datorn direkt från internet med Fjärrskrivbord.

När en virtuell dator med delad IP-aktiverad läggs till i undernätet lägger DevTest Labs automatiskt till en belastningsutjämnare och tilldelar ett TCP-portnummer på den offentliga IP-adressen, vidarebefordra till RDP-porten på den virtuella datorn.  

## <a name="using-the-shared-ip"></a>Använda den delade IP-adressen

- **Linux-användare:** SSH till den virtuella datorn med hjälp av IP-adressen eller fullständigt kvalificerat domännamn, följt av ett kolon, följt av porten. I bilden nedan är `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661`rdp-adressen för att ansluta till den virtuella datorn till exempel .

  ![Exempel på virtuell dator](media/devtest-lab-shared-ip/vm-info.png)

- **Windows-användare:** Välj **knappen Anslut** på Azure-portalen om du vill hämta en förkonfigurerad RDP-fil och komma åt den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* [Definiera labbprinciper i Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md)





