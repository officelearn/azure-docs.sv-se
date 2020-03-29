---
title: Anslut till dina virtuella datorer via en webbläsare - Azure | Microsoft-dokument
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974313"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Ansluta till dina virtuella datorer via en webbläsare 

DevTest Labs integreras med [Azure Bastion](https://docs.microsoft.com/azure/bastion/), vilket gör att du kan ansluta till dina virtuella datorer via en webbläsare. Information om hur du aktiverar den här funktionen i DevTest Labs finns i [Aktivera webbläsaranslutning på virtuella labbdatorer](enable-browser-connection-lab-virtual-machines.md).

När *webbläsaranslutningen* är aktiverad kan labbanvändare komma åt virtuella datorer via en webbläsare.  

## <a name="create-a-lab-virtual-machine"></a>Skapa en virtuell labbdator

Du måste först skapa den virtuella labbdatorn i ett virtuellt nätverk som har Bastion konfigurerat på den. Välj det andra **undernätet** som du har skapat, inte AzureBastionSubnet. Du kan välja ett virtuellt nätverk när du skapar virtuella datorer genom att gå till fliken **Avancerade inställningar.**

![Skapa en virtuell dator](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Starta virtuell dator i en webbläsare

När den virtuella datorn har skapats kan du starta den i en webbläsare genom att klicka på *knappen Anslut webbläsare* och ange ditt användarnamn och lösenord för maskinen.  

![Starta i en webbläsare](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Efterföljande moment

[Lägga till en virtuell dator i ett labb i Azure DevTest Labs](devtest-lab-add-vm.md)
