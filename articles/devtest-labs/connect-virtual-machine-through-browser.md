---
title: Ansluta till dina virtuella datorer via en webbläsare – Azure | Microsoft Docs
description: Lär dig hur du ansluter till dina virtuella datorer via en webbläsare.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b1e50a15673199a4bf95956ae66e6b58d6d41d0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483745"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Ansluta till dina virtuella datorer via en webbläsare 

DevTest Labs integreras med [Azure skydds](https://docs.microsoft.com/azure/bastion/), vilket gör att du kan ansluta till dina virtuella datorer via en webbläsare. Information om hur du aktiverar den här funktionen i DevTest Labs finns i [aktivera webb läsar anslutning på virtuella labb datorer](enable-browser-connection-lab-virtual-machines.md).

När *webbläsaren Connect* är aktive rad kan Lab-användare komma åt virtuella datorer via en webbläsare.  

## <a name="create-a-lab-virtual-machine"></a>Skapa en virtuell labb dator

Du måste först skapa den virtuella labb datorn i ett virtuellt nätverk där skydds har kon figurer ATS. Välj det andra **under nätet** som du skapade, inte AzureBastionSubnet. Du kan välja ett virtuellt nätverk när du skapar en virtuell dator genom att gå till fliken **Avancerade inställningar** .

![Skapa en virtuell dator](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Starta virtuell dator i en webbläsare

När den virtuella datorn har skapats kan du starta den i en webbläsare genom att klicka på knappen *Browser Connect* och ange ditt användar namn och lösen ord för datorn.  

![Starta i en webbläsare](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Nästa steg

[Lägga till en virtuell dator i ett labb i Azure DevTest Labs](devtest-lab-add-vm.md)
