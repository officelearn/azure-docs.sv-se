---
title: Aktivera webbläsaranslutning på virtuella Azure DevTest Labs-datorer | Microsoft-dokument
description: DevTest Labs integreras nu med Azure Bastion, som ägare av labbet kan du aktivera åtkomst till alla virtuella labbdatorer via en webbläsare.
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
ms.openlocfilehash: 86304fc7776f49c999924b8609f2d26120cee372
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549087"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Aktivera webbläsaranslutning på virtuella labbdatorer 

DevTest Labs integreras med [Azure Bastion](https://docs.microsoft.com/azure/bastion/), vilket gör att du kan ansluta till dina virtuella datorer via en webbläsare. Du måste först aktivera webbläsaranslutning på virtuella labbdatorer.

Som ägare av ett labb kan du aktivera åtkomst till alla virtuella labbdatorer via en webbläsare. Du behöver inte ytterligare en klient, agent eller programvara. Azure Bastion tillhandahåller säker och sömlös RDP/SSH-anslutning till dina virtuella datorer direkt i Azure-portalen via TLS. När du ansluter via Azure Bastion behöver dina virtuella datorer inte en offentlig IP-adress. Mer information finns i [Vad är Azure Bastion?](../bastion/bastion-overview.md)

> [!NOTE]
> Aktivera webbläsaranslutning på virtuella labbdatorer är i förhandsversion.

Den här artikeln visar hur du aktiverar webbläsaranslutning på virtuella labbdatorer.

## <a name="prerequisites"></a>Krav 
Antingen distribuera en Bastion-värd i det befintliga labbets virtuella nätverk **(OR)** ansluter du labbet med ett bastionkonfigurerat virtuellt nätverk. 

Mer information om hur du distribuerar en Bastion-värd i ett virtuella nätverk finns i [Skapa en Azure Bastion-värd (förhandsversion).](../bastion/bastion-create-host-portal.md) När du skapar Bastion-värden väljer du labbets virtuella nätverk. 

Mer information om hur du ansluter ditt labb till ett bastionkonfigurerat virtuellt nätverk finns [i Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md). Välj det virtuella nätverk som har bastionvärden distribuerat och **AzureBastionSubnet** i det. Här är de detaljerade stegen: 

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster** på menyn till vänster. 
1. Välj **DevTest Labs** från listan. 
1. Välj *ditt labb*i listan över labb. 

    > [!NOTE]
    > Azure Bastion är för närvarande i förhandsversion. Det är begränsat till följande regioner: Västra USA, Östra USA, Västeuropa, Södra centrala USA, Australien East och Japan East. Så, skapa ett labb i en av dessa regioner om ditt labb inte är i en av dem. 
1. Välj **Konfiguration och principer** i avsnittet **Inställningar** på den vänstra menyn. 
1. Välj **Virtuella nätverk**.
1. Välj **Lägg till** i verktygsfältet. 
1. Välj det **virtuella nätverk** som har bastionvärden distribuerat. 
1. Välj undernät: **AzureBastionSubnet**. 

    ![Undernät](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Välj **Alternativet Använd i skapande av virtuella datorer.** 
1. Välj **Spara** i verktygsfältet. 
1. Om du har ett gammalt VNet för labbet, ta bort den genom att välja **...*  och **Ta bort**. 

## <a name="enable-browser-connection"></a>Aktivera webbläsaranslutning 

När du har ett Bastion-konfigurerat virtuellt nätverk i labbet, som labbägare, kan du aktivera webbläsaranslutning på virtuella labbdatorer.

Så här aktiverar du webbläsaranslutning på virtuella labbdatorer:

1. Navigera till *ditt labb*i Azure-portalen .
1. Välj **Konfiguration och principer**.
1. Välj Browser **connect (Förhandsgranska)** i **Inställningar**.

![Aktivera webbläsaranslutning](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Efterföljande moment
Se följande artikel om du vill lära dig hur du ansluter till dina virtuella datorer med hjälp av en webbläsare: [Anslut till dina virtuella datorer via en webbläsare](connect-virtual-machine-through-browser.md)