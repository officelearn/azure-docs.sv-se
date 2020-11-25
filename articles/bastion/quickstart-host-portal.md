---
title: 'Snabb start: Konfigurera Azure-skydds och ansluta till en virtuell dator via privat IP-adress och en webbläsare'
titleSuffix: Azure Bastion
description: I den här snabb starts artikeln lär du dig hur du skapar en Azure skydds-värd från en virtuell dator och ansluter till den virtuella datorn via din webbläsare via privat IP-adress.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 325f39b695d80c14ed7097d071380b937458546c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021494"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>Snabb start: ansluta till en virtuell dator på ett säkert sätt via en webbläsare via privat IP-adress

Du kan ansluta till en virtuell dator (VM) via webbläsaren med hjälp av Azure Portal och Azure-skydds. Den här snabb starts artikeln visar hur du konfigurerar Azure-skydds baserat på dina VM-inställningar och sedan ansluter till den virtuella datorn via portalen. Den virtuella datorn behöver inte ha någon offentlig IP-adress, klient program vara, agent eller en särskild konfiguration. När tjänsten har tillhandahållits är RDP/SSH-upplevelsen tillgänglig för alla virtuella datorer i samma virtuella nätverk. Mer information om Azure skydds finns i [Vad är Azure skydds?](bastion-overview.md).

## <a name="prerequisites"></a><a name="prereq"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. Om du inte har ett kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). För att kunna ansluta till en virtuell dator via webbläsaren med skydds måste du kunna logga in på den Azure Portal.

* En virtuell Windows-dator i ett virtuellt nätverk. Om du inte har en virtuell dator skapar du en med [snabb start: skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md).

  * Om du behöver exempel värden kan du se de tillhandahållna [exempel värdena](#values).
  * Om du redan har ett virtuellt nätverk, se till att välja det på fliken nätverk när du skapar din virtuella dator.
  * Om du inte redan har ett virtuellt nätverk kan du skapa ett på samma tidpunkt som du skapar den virtuella datorn.
  * Du behöver inte ha en offentlig IP-adress för den här virtuella datorn för att kunna ansluta via Azure skydds.

* Nödvändiga VM-roller:
  * Rollen läsare på den virtuella datorn.
  * Rollen läsare på NÄTVERKSKORTet med den virtuella datorns privata IP-adress.
  
* Obligatoriska VM-portar:
  * Inkommande portar: RDP (3389)

### <a name="example-values"></a><a name="values"></a>Exempelvärden

Du kan använda följande exempel värden när du skapar den här konfigurationen, eller så kan du ersätta din egen.

**Grundläggande VNet-och VM-värden:**

|**Namn** | **Värde** |
| --- | --- |
| Virtuell dator| TestVM |
| Resursgrupp | TestRG |
| Region | East US |
| Virtuellt nätverk | TestVNet1 |
| Adressutrymme | 10.0.0.0/16 |
| Undernät | Klient del: 10.0.0.0/24 |

**Azure skydds-värden:**

|**Namn** | **Värde** |
| --- | --- |
| Name | TestVNet1 – skydds |
| + Under näts namn | AzureBastionSubnet |
| AzureBastionSubnet-adresser | Ett undernät inom ditt VNet-adressutrymme med en/27-nätmask. Till exempel 10.0.1.0/27.  |
| Offentlig IP-adress |  Skapa ny |
| Namn på offentlig IP-adress | VNet1BastionPIP  |
| SKU för offentlig IP-adress |  Standard  |
| Tilldelning  | Statisk |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Skapa en skydds-värd

Det finns flera olika sätt att konfigurera en skydds-värd. I följande steg ska du skapa en skydds-värd i Azure Portal direkt från din virtuella dator. När du skapar en värd från en virtuell dator fylls olika inställningar automatiskt i som motsvarar den virtuella datorn och/eller det virtuella nätverket.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till den virtuella dator som du vill ansluta till och välj sedan **Anslut**.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="inställningar för virtuell dator" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. Välj **skydds** i list rutan.
1. På **testvm | Sidan Anslut** väljer du **Använd skydds**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Välj skydds" border="false":::

1. På sidan **skydds** fyller du i följande inställnings fält:

   * **Namn**: namnge skydds-värden.
   * **Undernät**: Detta är det virtuella nätverks adress utrymme som skydds-resursen ska distribueras till. Under nätet måste skapas med namnet **AzureBastionSubnet**. Använd ett undernät på minst/27 eller större (/27,/26,/25 osv.).
   * Välj **Hantera under näts konfiguration**.
1. På sidan **undernät** väljer du **+ undernät**.

   :::image type="content" source="./media/quickstart-host-portal/subnet.png" alt-text="+ Undernät":::
    
1. Skriv **AzureBastionSubnet** i **namn** på sidan **Lägg till undernät** .
   * För under näts adress intervall väljer du en under näts adress som ligger inom det virtuella nätverkets adress utrymme.
   * Justera inte andra inställningar. Välj **OK** för att acceptera och spara under näts ändringarna.

   :::image type="content" source="./media/quickstart-host-portal/add-subnet.png" alt-text="Lägga till undernät":::
1. Klicka på knappen tillbaka i webbläsaren för att gå tillbaka till sidan **skydds** och fortsätt att ange värden.
   * **Offentlig IP-adress**: lämna som **Skapa ny**.
   * **Namn på offentlig IP-adress**: namnet på den offentliga IP-adressresursen.
   * **Tilldelning**: standardvärdet är statiskt. Du kan inte använda en dynamisk tilldelning för Azure-skydds.
   * **Resurs grupp**: samma resurs grupp som den virtuella datorn.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="Skapa skydds-värden":::
1. Välj **skapa** för att skapa skydds-värden. Azure verifierar inställningarna och skapar sedan värden. Värden och dess resurser tar ungefär 5 minuter att skapa och distribuera.

## <a name="connect"></a><a name="connect"></a>Anslut

När skydds har distribuerats till det virtuella nätverket ändras skärmen till sidan Anslut.

1. Ange användar namn och lösen ord för den virtuella datorn. Välj sedan **Anslut**.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="Skärm bild som visar dialog rutan Anslut med Azure skydds.":::
1. RDP-anslutningen till den virtuella datorn öppnas direkt i Azure Portal (via HTML5) med port 443 och skydds-tjänsten.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="RDP-anslutning":::

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med det virtuella nätverket och de virtuella datorerna tar du bort resurs gruppen och alla resurser den innehåller:

1. Ange namnet på din resurs grupp i rutan **Sök** högst upp i portalen och välj den från Sök resultaten.

1. Välj **Ta bort resursgrupp**.

1. Ange resurs gruppen för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en skydds-värd för ditt virtuella nätverk och anslöt sedan till en virtuell dator på ett säkert sätt via skydds. Sedan kan du fortsätta med följande steg om du vill ansluta till en skalnings uppsättning för virtuella datorer.

> [!div class="nextstepaction"]
> [Ansluta till en skalnings uppsättning för virtuella datorer med hjälp av Azure skydds](bastion-connect-vm-scale-set.md)
