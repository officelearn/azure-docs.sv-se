---
title: Ansluta till dina virtuella datorer via en webbläsare – Azure | Microsoft Docs
description: Lär dig hur du ansluter till dina virtuella datorer via en webbläsare.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 4549ee0f50c06040b70307c96b5ca96cd4162214
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84895614"
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

## <a name="next-steps"></a>Efterföljande moment

[Lägga till en virtuell dator i ett labb i Azure DevTest Labs](devtest-lab-add-vm.md)
