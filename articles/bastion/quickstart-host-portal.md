---
title: 'Snabb start: skapa en skydds-värd från en virtuell dator och Anslut via privat IP-adress'
titleSuffix: Azure Bastion
description: I den här snabb starts artikeln lär du dig hur du skapar en Azure skydds-värd från en virtuell dator och ansluter på ett säkert sätt med en privat IP-adress.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 6f451e7b115c00bc7b2cf350e00b9f704ab1d29f
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019060"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Snabb start: ansluta till en virtuell dator med en privat IP-adress och Azure skydds

Den här snabb starts artikeln visar hur du ansluter till en virtuell dator via webbläsaren med hjälp av Azure skydds och Azure Portal. I Azure Portal, från din virtuella Azure-dator, kan du distribuera skydds till det virtuella nätverket. När du har distribuerat skydds kan du ansluta till den virtuella datorn via dess privata IP-adress med hjälp av Azure Portal. Din virtuella dator behöver ingen offentlig IP-adress eller speciell program vara. En fördel med att skapa en skydds-värd för ditt VNet direkt från din virtuella dator är att många av inställningarna fylls i automatiskt.

När tjänsten har tillhandahållits är RDP/SSH-upplevelsen tillgänglig för alla virtuella datorer i samma virtuella nätverk. Mer information om Azure skydds finns i [Vad är Azure skydds?](bastion-overview.md).

## <a name="prerequisites"></a><a name="prereq"></a>Förutsättningar

* Ett virtuellt nätverk.
* En virtuell Windows-dator i det virtuella nätverket.
* Följande nödvändiga roller:
  * Rollen läsare på den virtuella datorn.
  * Rollen läsare på NÄTVERKSKORTet med den virtuella datorns privata IP-adress.

* Portar: för att ansluta till den virtuella datorn måste du ha följande portar öppna på den virtuella datorn:
  * Inkommande portar: RDP (3389)

### <a name="example-values"></a>Exempelvärden

|**Namn** | **Värde** |
| --- | --- |
| Name |  TestVNet1 – skydds |
| Virtuellt nätverk |  TestVNet1 (baserat på den virtuella datorn) |
| + Under näts namn | AzureBastionSubnet |
| AzureBastionSubnet-adresser |  10.1.254.0/27 |
| Offentlig IP-adress |  Skapa ny |
| Namn på offentlig IP-adress | VNet1BastionPIP  |
| SKU för offentlig IP-adress |  Standard  |
| Tilldelning  | Statisk |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Skapa en skydds-värd

När du skapar en skydds-värd i Azure Portal med hjälp av en befintlig virtuell dator, kommer olika inställningar automatiskt att motsvara den virtuella datorn och/eller det virtuella nätverket.

1. Öppna [Azure-portalen](https://portal.azure.com). Gå till den virtuella datorn och välj **Anslut**.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="inställningar för virtuell dator" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. Välj **skydds**i list rutan.
1. På **testvm | Sidan Anslut**väljer du **Använd skydds**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="inställningar för virtuell dator" border="false":::

1. På sidan **skydds** fyller du i följande inställnings fält:

   * **Namn**: namnge skydds-värden.
   * **Undernät**: under nätet i det virtuella nätverket som skydds-resursen ska distribueras till. Under nätet måste skapas med namnet **AzureBastionSubnet**. Namnet låter Azure veta vilket undernät som ska användas för att distribuera skydds-resursen. Detta skiljer sig från ett Gateway-undernät. Använd ett undernät på minst/27 eller större (/27,/26,/25 osv.).
   
      * Välj **Hantera under näts konfiguration**.
      * Välj **AzureBastionSubnet**.
      * Justera adress intervallet i CIDR-notation vid behov. Till exempel 10.1.254.0/27.
      * Justera inte andra inställningar. Välj **OK** för att acceptera och spara under näts ändringarna eller Välj **x** överst på sidan om du inte vill göra några ändringar.
1. Klicka på knappen tillbaka i webbläsaren för att gå tillbaka till sidan **skydds** och fortsätt att ange värden.
   * **Namn på offentlig IP-adress**: namnet på den offentliga IP-adressresursen.
   * **Offentlig IP-adress**: det här är den offentliga IP-adressen för den skydds-resurs som RDP/SSH kommer att få åtkomst till (via port 443). Skapa en ny offentlig IP-adress.
1. Välj **skapa** för att skapa skydds-värden. Azure verifierar inställningarna och skapar sedan värden. Värden och dess resurser tar ungefär 5 minuter att skapa och distribuera.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="inställningar för virtuell dator":::

## <a name="connect"></a><a name="connect"></a>Anslut

När skydds har distribuerats till det virtuella nätverket ändras skärmen till sidan Anslut.

1. Ange användar namn och lösen ord för den virtuella datorn. Välj sedan **Anslut**.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="inställningar för virtuell dator":::
1. RDP-anslutningen till den virtuella datorn via skydds öppnas direkt i Azure Portal (via HTML5) med port 443 och skydds-tjänsten.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="inställningar för virtuell dator":::

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med det virtuella nätverket och de virtuella datorerna tar du bort resurs gruppen och alla resurser den innehåller:

1. Ange namnet på din resurs grupp i rutan **Sök** högst upp i portalen och välj den från Sök resultaten.

1. Välj **Ta bort resursgrupp**.

1. Ange resurs gruppen för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en skydds-värd för ditt virtuella nätverk och anslöt sedan till en virtuell dator på ett säkert sätt via skydds-värden. Sedan kan du fortsätta med följande steg om du vill ansluta till en skalnings uppsättning för virtuella datorer.

> [!div class="nextstepaction"]
> [Ansluta till en skalnings uppsättning för virtuella datorer med hjälp av Azure skydds](bastion-connect-vm-scale-set.md)
