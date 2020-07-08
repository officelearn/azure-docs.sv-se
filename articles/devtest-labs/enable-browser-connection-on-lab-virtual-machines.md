---
title: Aktivera webb läsar anslutning på Azure DevTest Labs virtuella datorer | Microsoft Docs
description: DevTest Labs integreras nu med Azure skydds som en ägare till labbet som du kan använda för att få åtkomst till alla virtuella labb datorer via en webbläsare.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 9b901d36907c8d2bbae866c38bbf1c2554e0f1da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482946"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Aktivera webb läsar anslutning på virtuella labb datorer 

DevTest Labs integreras med [Azure skydds](https://docs.microsoft.com/azure/bastion/), vilket gör att du kan ansluta till dina virtuella datorer via en webbläsare. Du måste först aktivera webb läsar anslutning på virtuella labb datorer.

Som ägare till ett labb kan du aktivera åtkomst till alla virtuella labb datorer via en webbläsare. Du behöver inte ytterligare en klient, en agent eller en program varu enhet. Azure skydds ger säker och sömlös RDP/SSH-anslutning till dina virtuella datorer direkt i Azure Portal via TLS. När du ansluter via Azure skydds behöver inte de virtuella datorerna någon offentlig IP-adress. Mer information finns i [Vad är Azure skydds?](../bastion/bastion-overview.md)

> [!NOTE]
> Att aktivera webb läsar anslutning på virtuella labb datorer är i för hands version.

Den här artikeln visar hur du aktiverar webb läsar anslutning på virtuella labb datorer.

## <a name="prerequisites"></a>Krav 
Antingen distribuerar du en skydds-värd i ditt befintliga labbs virtuella nätverk **(eller) och** ansluter ditt labb med ett skydds konfigurerat VNet. 

Information om hur du distribuerar en skydds-värd i ett VNet finns i [skapa en Azure skydds-värd (för hands version)](../bastion/bastion-create-host-portal.md). När du skapar skydds-värden väljer du Labbets virtuella nätverk. 

Information om hur du ansluter din labb med ett skydds konfigurerat VNet finns [i Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md). Välj det VNet som har skydds-värden distribuerad och **AzureBastionSubnet** i den. Här är de detaljerade stegen: 

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **alla tjänster** i den vänstra navigerings menyn. 
1. Välj **DevTest Labs** från listan. 
1. I listan med labb väljer *du ditt labb*. 

    > [!NOTE]
    > Azure skydds är för närvarande en för hands version. De är begränsade till följande regioner: västra USA, östra USA, västra Europa, södra centrala USA, östra Australien och Östra Japan. Skapa ett labb i något av de här regionerna om ditt labb inte finns i något av dem. 
1. Välj **konfiguration och principer** i avsnittet **Inställningar** på den vänstra menyn. 
1. Välj **virtuella nätverk**.
1. Välj **Lägg till** i verktygsfältet. 
1. Välj det **VNet** som har skydds-värden distribuerad. 
1. Välj under nätet: **AzureBastionSubnet**. 

    ![Undernät](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Välj alternativet **Använd vid skapande av virtuell dator** . 
1. Välj **Spara** i verktygsfältet. 
1. Om du har ett gammalt VNet för labbet tar du bort det genom att välja **...*  och **ta bort**. 

## <a name="enable-browser-connection"></a>Aktivera webb läsar anslutning 

När du har ett skydds konfigurerat VNet i labbet, som labb ägare, kan du aktivera webbläsaren Anslut på virtuella labb datorer.

Följ dessa steg om du vill aktivera webbläsaren Connect på virtuella labb datorer:

1. I Azure Portal navigerar du till *labbet*.
1. Välj **konfiguration och principer**.
1. I **Inställningar**väljer du **webbläsare Connect (för hands version)**.

![Aktivera webb läsar anslutning](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Nästa steg
I följande artikel finns information om hur du ansluter till dina virtuella datorer med hjälp av en webbläsare: [Anslut till dina virtuella datorer via en webbläsare](connect-virtual-machine-through-browser.md)