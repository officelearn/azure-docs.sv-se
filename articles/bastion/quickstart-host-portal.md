---
title: 'Snabb start: ansluta till en virtuell dator med en privat IP-adress: Azure skydds'
description: I den här artikeln lär du dig hur du skapar en Azure skydds-värd från en virtuell dator och ansluter på ett säkert sätt med en privat IP-adress.
services: bastion
author: charwen
ms.service: bastion
ms.topic: quickstart
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: b0155ae92e3179918273d6a19773aa15b67949ea
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985588"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Snabb start: ansluta till en virtuell dator med en privat IP-adress och Azure skydds

Den här snabb starts artikeln visar hur du ansluter till en virtuell dator med en privat IP-adress. När du ansluter via skydds behöver inte de virtuella datorerna någon offentlig IP-adress. Stegen i den här artikeln hjälper dig att distribuera skydds till ditt virtuella nätverk via den virtuella datorn i portalen. När tjänsten har tillhandahållits är RDP/SSH-upplevelsen tillgänglig för alla virtuella datorer i samma virtuella nätverk.

## <a name="prerequisites"></a><a name="prereq"></a>Förutsättningar

* Ett virtuellt Azure-nätverk.
* En virtuell Azure-dator som finns i det virtuella nätverket med port 3389 öppen.

### <a name="example-values"></a>Exempelvärden

|**Namn** | **Värde** |
| --- | --- |
| Name |  VNet1Bastion |
| Region | eastus |
| Virtuellt nätverk |  VNet1 |
| + Under näts namn | AzureBastionSubnet |
| AzureBastionSubnet-adresser |  10.1.254.0/27 |
| Offentlig IP-adress |  Skapa ny |
| Namn på offentlig IP-adress | VNet1BastionPIP  |
| SKU för offentlig IP-adress |  Standard  |
| Tilldelning  | Statisk |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Skapa en skydds-värd

När du skapar en skydds-värd i portalen genom att använda en befintlig virtuell dator, kommer olika inställningar automatiskt att motsvara den virtuella datorn och/eller det virtuella nätverket.

1. Öppna [Azure-portalen](https://portal.azure.com). Gå till den virtuella datorn och klicka sedan på **Anslut**.

   ![inställningar för virtuell dator](./media/quickstart-host-portal/vm-settings.png)
1. Välj **skydds**i list rutan.
1. På sidan Anslut väljer du **Använd skydds**.

   ![Välj skydds](./media/quickstart-host-portal/select-bastion.png)

1. På sidan skydds fyller du i följande inställnings fält:

   * **Namn**: namnge skydds-värden
   * **Undernät**: under nätet i det virtuella nätverket som skydds-resursen ska distribueras till. Under nätet måste skapas med namnet **AzureBastionSubnet**. Namnet låter Azure veta vilket undernät som ska användas för att distribuera skydds-resursen. Detta skiljer sig från ett Gateway-undernät. Använd ett undernät på minst/27 eller större (/27,/26,/25 osv.).
   
      * Välj **Hantera under näts konfiguration**och välj sedan **+ undernät**.
      * På sidan Lägg till undernät skriver du **AzureBastionSubnet**.
      * Ange adress intervallet i CIDR-notation. Till exempel 10.1.254.0/27.
      * Välj **OK** för att skapa under nätet. Längst upp på sidan går du tillbaka till skydds för att slutföra resten av inställningarna.

         ![Navigera till skydds-inställningar](./media/quickstart-host-portal/navigate-bastion.png)
   * **Offentlig IP-adress**: det här är den offentliga IP-adressen för den skydds-resurs som RDP/SSH kommer att få åtkomst till (via port 443). Skapa en ny offentlig IP-adress eller Använd en befintlig. Den offentliga IP-adressen måste finnas i samma region som den skydds-resurs som du skapar.
   * **Namn på offentlig IP-adress**: namnet på den offentliga IP-adressresursen.
1. På validerings skärmen klickar du på **skapa**. Vänta i 5 minuter för skydds-resursen skapa och distribuera.

   ![Skapa skydds-värd](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Gå

När skydds har distribuerats till det virtuella nätverket ändras skärmen till sidan Anslut.

1. Ange användar namn och lösen ord för den virtuella datorn. Välj sedan **Anslut**.

   ![Skärm bild som visar dialog rutan Anslut med Azure skydds, där du uppmanas att ange ett användar namn och lösen ord.](./media/quickstart-host-portal/connect.png)
1. RDP-anslutningen till den virtuella datorn via skydds öppnas direkt i Azure Portal (via HTML5) med port 443 och skydds-tjänsten.

   ![RDP-anslutning](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med det virtuella nätverket och de virtuella datorerna tar du bort resurs gruppen och alla resurser den innehåller:

1. Skriv *TestRG1* i **sökrutan längst** upp i portalen och välj **TestRG1** från Sök resultaten.

2. Välj **Ta bort resursgrupp**.

3. Ange *TestRG1* för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en skydds-värd för ditt virtuella nätverk och anslöt sedan till en virtuell dator på ett säkert sätt via skydds-värden.

* Läs mer om Azure skydds i [Översikt över skydds](bastion-overview.md) och [vanliga frågor och svar om skydds](bastion-faq.md).
* Information om hur du använder nätverks säkerhets grupper med Azure skydds-undernätet finns i [arbeta med NSG: er](bastion-nsg.md).
* Instruktioner som innehåller förklaringar av inställningarna för Azure skydds-värden finns i [självstudien](bastion-create-host-portal.md).
* Om du vill ansluta till en skalnings uppsättning för virtuella datorer läser du [Anslut till en skalnings uppsättning för virtuella datorer med hjälp av Azure skydds](bastion-connect-vm-scale-set.md).