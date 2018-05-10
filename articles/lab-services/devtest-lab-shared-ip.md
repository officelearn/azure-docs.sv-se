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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: c62f8808565022371484b936f5a2bdaba1f8900e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Förstå delade IP-adresser i Azure DevTest Labs

Azure DevTest Labs kan lab virtuella datorer som delar samma offentliga IP-adress för att minimera antalet offentliga IP-adresser för att komma åt labbet enskilda virtuella datorer.  Den här artikeln beskriver hur delade IP-adresser arbete och deras relaterade konfigurationsalternativ.

## <a name="shared-ip-setting"></a>Delade IP-inställning

När du skapar ett labb som den finns i ett undernät i ett virtuellt nätverk.  Det här undernätet skapas som standard med **aktivera delad offentlig IP** inställd på *Ja*.  Den här konfigurationen skapas en offentlig IP-adress för hela undernätet.  Mer information om hur du konfigurerar virtuella nätverk och undernät finns [konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nytt labb undernät](media/devtest-lab-shared-ip/lab-subnet.png)

För befintliga testlabb kan du aktivera det här alternativet genom att välja **konfiguration och principer > virtuella nätverk**. Välj ett virtuellt nätverk i listan och väljer sedan **aktivera DELAD offentlig IP-** för valda undernätet. Du kan också inaktivera det här alternativet i en labb om du inte vill dela en offentlig IP-adress i labbet virtuella datorer.

Alla virtuella datorer skapas i den här övningen standard att använda delade IP.  När du skapar den virtuella datorn måste den här inställningen kan observeras i den **avancerade inställningar** bladet under **IP-adresskonfiguration**.

![Ny virtuell dator](media/devtest-lab-shared-ip/new-vm.png)

- **Delade:** alla virtuella datorer skapas som **delade** placeras i en resursgrupp (RG). En IP-adress har tilldelats för som RG och alla virtuella datorer i RG ska använda IP-adressen.
- **Publik:** varje VM som du skapar har sin egen IP-adress och skapas i sin egen resursgruppen.
- **Privat:** varje virtuell dator som du skapar använder en privat IP-adress. Du kan inte ansluta till den här virtuella datorn direkt från internet med fjärrskrivbord.

När en virtuell dator med delade IP är aktiverat läggs till undernätet, lägger till den virtuella datorn till en belastningsutjämnare automatiskt i DevTest Labs och tilldelar TCP-portnummer för den offentliga IP-adressen vidarebefordran till RDP-porten på den virtuella datorn.  

## <a name="using-the-shared-ip"></a>Använda delade IP

- **Linux-användare:** SSH till den virtuella datorn med hjälp av IP-adress eller fullständigt domännamn, följt av ett kolon och porten. I bilden nedan RDP-adress att ansluta till den virtuella datorn är exempelvis `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.

  ![VM-exempel](media/devtest-lab-shared-ip/vm-info.png)

- **Windows-användare:** markerar du den **Anslut** knappen på Azure-portalen att hämta en förkonfigurerad RDP-fil och komma åt den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* [Definiera principer för labbet i Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md)





