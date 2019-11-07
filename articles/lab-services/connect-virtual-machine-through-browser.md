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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 1bb5d979ff7c9fe6e2afec4c7ebd81649b4488e3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581233"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Ansluta till dina virtuella datorer via en webbläsare 

DevTest Labs integreras med [Azure skydds](https://docs.microsoft.com/azure/bastion/), vilket gör att du kan ansluta till dina virtuella datorer via en webbläsare. Information om hur du aktiverar den här funktionen i DevTest Labs finns i [aktivera webb läsar anslutning på virtuella labb datorer](enable-browser-connection-lab-virtual-machines.md).

När *webbläsaren Connect* är aktive rad kan Lab-användare komma åt virtuella datorer via en webbläsare.  


## <a name="create-a-lab-virtual-machine"></a>Skapa en virtuell labb dator

Du måste först skapa den virtuella labb datorn i ett VNet som har skydds konfigurerat. Du kan välja ett VNet när du skapar en virtuell dator genom att gå till fliken **Avancerade inställningar** .

![Skapa en virtuell dator](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Starta virtuell dator i en webbläsare

När den virtuella datorn har skapats kan du starta den i en webbläsare genom att klicka på knappen *Browser Connect* och ange ditt användar namn och lösen ord för datorn.  

![Starta i en webbläsare](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Nästa steg

[Lägga till en virtuell dator i ett labb i Azure DevTest Labs](devtest-lab-add-vm.md)
