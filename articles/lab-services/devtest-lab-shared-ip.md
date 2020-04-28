---
title: Förstå delade IP-adresser i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur Azure DevTest Labs använder delade IP-adresser för att minimera de offentliga IP-adresser som krävs för att komma åt dina virtuella labb datorer.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "65236894"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Förstå delade IP-adresser i Azure DevTest Labs

Med Azure DevTest Labs kan virtuella labb datorer dela samma offentliga IP-adress för att minimera antalet offentliga IP-adresser som krävs för att få åtkomst till dina virtuella datorer i labbet.  Den här artikeln beskriver hur delade IP-adresser fungerar och deras relaterade konfigurations alternativ.

## <a name="shared-ip-setting"></a>Delad IP-inställning

När du skapar ett labb skapas det i ett undernät i ett virtuellt nätverk.  Som standard skapas det här under nätet med **alternativet Aktivera delad offentlig IP** till *Ja*.  Den här konfigurationen skapar en offentlig IP-adress för hela under nätet.  Mer information om hur du konfigurerar virtuella nätverk och undernät finns i [Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nytt labb under nät](media/devtest-lab-shared-ip/lab-subnet.png)

För befintliga labb kan du aktivera det här alternativet genom att välja **konfiguration och principer > virtuella nätverk**. Välj sedan ett virtuellt nätverk i listan och välj **aktivera delad offentlig IP** för ett valt undernät. Du kan också inaktivera det här alternativet i alla labb om du inte vill dela en offentlig IP-adress för virtuella labb datorer.

Alla virtuella datorer som skapats i den här labb standarden använder en delad IP-adress.  När du skapar den virtuella datorn kan den här inställningen observeras på sidan **Avancerade inställningar** under **IP-adresskonfiguration**.

![Ny virtuell dator](media/devtest-lab-shared-ip/new-vm.png)

- **Delad:** Alla virtuella datorer som har skapats som **delade** placeras i en resurs grupp (RG). En enskild IP-adress tilldelas för den RG och alla virtuella datorer i RG kommer att använda den IP-adressen.
- **Offentlig:** Varje virtuell dator som du skapar har sin egen IP-adress och skapas i en egen resurs grupp.
- **Privat:** Varje virtuell dator som du skapar använder en privat IP-adress. Du kan inte ansluta till den här virtuella datorn direkt från Internet med fjärr skrivbord.

När en virtuell dator med delade IP-adresser läggs till i under nätet lägger DevTest Labs automatiskt till den virtuella datorn i en belastningsutjämnare och tilldelar ett TCP-portnummer på den offentliga IP-adressen, vidarebefordrar till RDP-porten på den virtuella datorn.  

## <a name="using-the-shared-ip"></a>Använda den delade IP-adressen

- **Linux-användare:** SSH till den virtuella datorn med hjälp av IP-adressen eller det fullständigt kvalificerade domän namnet följt av ett kolon följt av porten. I bilden nedan är `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661`t. ex. RDP-adressen för att ansluta till den virtuella datorn.

  ![VM-exempel](media/devtest-lab-shared-ip/vm-info.png)

- **Windows-användare:** Välj knappen **Anslut** på Azure Portal för att ladda ned en förkonfigurerad RDP-fil och komma åt den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* [Definiera labb principer i Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md)





