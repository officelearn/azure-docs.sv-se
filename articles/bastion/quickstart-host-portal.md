---
title: 'Snabbstart: Anslut till en virtuell dator med en privat IP-adress: Azure Bastion'
description: I den här artikeln får du lära dig hur du skapar en Azure Bastion-värd från en virtuell dator och ansluter säkert med en privat IP-adress.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: a420a3253040fff8b767a81f298ede283c1d214b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619259"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Snabbstart: Anslut till en virtuell dator med en privat IP-adress och Azure Bastion

Den här snabbstartsartikeln visar hur du ansluter till en virtuell dator med en privat IP-adress. När du ansluter via Bastion behöver dina virtuella datorer inte en offentlig IP-adress. Stegen i den här artikeln hjälper dig att distribuera Bastion till ditt virtuella nätverk via din virtuella dator i portalen. När tjänsten har etablerats är RDP/SSH-upplevelsen tillgänglig för alla virtuella datorer i samma virtuella nätverk.

## <a name="prerequisites"></a><a name="prereq"></a>Krav

* Ett virtuellt Azure-nätverk.
* En virtuell Azure-dator som finns i det virtuella nätverket med port 3389 öppen.

### <a name="example-values"></a>Exempelvärden

|**Namn** | **Värde** |
| --- | --- |
| Namn |  VNet1Bastion |
| Region | eastus |
| Virtuellt nätverk |  VNet1 |
| + Namn på undernät | AzureBastionSubnet |
| AzureBastionSubnet-adresser |  10.1.254.0/27 |
| Offentlig IP-adress |  Skapa ny |
| Namn på offentlig IP-adress | VNet1BastionPIP  |
| SKU för offentlig IP-adress |  Standard  |
| Tilldelning  | Statisk |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Skapa en Bastion-värd

När du skapar en skyddsvärd i portalen med hjälp av en befintlig virtuell dator, kommer olika inställningar automatiskt standard att motsvara din virtuella dator och / eller virtuella nätverk.

1. Öppna [Azure-portalen](https://portal.azure.com). Gå till din virtuella dator och klicka sedan på **Anslut**.

   ![inställningar för virtuella datorer](./media/quickstart-host-portal/vm-settings.png)
1. Välj **Bastion**i listrutan .
1. På sidan Anslut väljer du **Använd bastion**.

   ![välj Bastion](./media/quickstart-host-portal/select-bastion.png)

1. Fyll i följande inställningsfält på sidan Bastion:

   * **Namn**: Namnge skyddsvärden
   * **Undernät:** Undernätet i det virtuella nätverket som Bastion-resursen ska distribueras till. Undernätet måste skapas med namnet **AzureBastionSubnet**. Namnet låter Azure veta vilket undernät som bastionresursen ska distribueras till. Detta skiljer sig från ett gateway-undernät. Använd ett undernät på minst /27 eller större (/27, /26, /25 och så vidare).
   
      * Välj **Hantera undernätskonfiguration**och välj sedan **+ Undernät**.
      * Skriv **AzureBastionSubnet**på sidan Lägg till undernät .
      * Ange adressintervallet i CIDR-notation. Till exempel 10.1.254.0/27.
      * Välj **OK** om du vill skapa undernätet. Längst upp på sidan navigerar du tillbaka till Bastion för att slutföra resten av inställningarna.

         ![navigera till skyddsinställningar](./media/quickstart-host-portal/navigate-bastion.png)
   * **Offentlig IP-adress**: Detta är den offentliga IP-adressen för den Bastion-resurs som RDP/SSH kommer att nås på (över port 443). Skapa en ny offentlig IP eller använd en befintlig. Den offentliga IP-adressen måste finnas i samma region som den Bastion-resurs som du skapar.
   * **Namn på offentlig IP-adress**: Namnet på den offentliga IP-adressresursen.
1. Klicka på **Skapa**på verifieringsskärmen . Vänta i ca 5 minuter innan Bastion-resursen har skapat och distribuerats.

   ![skapa skyddsvärd](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Ansluta

När Bastion har distribuerats till det virtuella nätverket ändras skärmen till anslutningssidan.

1. Skriv användarnamn och lösenord för din virtuella dator. Välj sedan **Anslut**.

   ![connect](./media/quickstart-host-portal/connect.png)
1. RDP-anslutningen till den här virtuella datorn via Bastion öppnas direkt i Azure-portalen (över HTML5) med port 443 och Bastion-tjänsten.

   ![RDP-anslutning](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med det virtuella nätverket och de virtuella datorerna tar du bort resursgruppen och alla resurser som den innehåller:

1. Ange *TestRG1* i **sökrutan** högst upp på portalen och välj **TestRG1** från sökresultaten.

2. Välj **Ta bort resursgrupp**.

3. Ange *TestRG1* för **TYP RESURSGRUPPNAMN** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Bastion-värd för ditt virtuella nätverk och sedan ansluten till en virtuell dator på ett säkert sätt via Bastion-värden.

* Om du vill veta mer om Azure Bastion läser du [vanliga frågor om Bastion-skyddsbeflädning](bastion-overview.md) och [bastion](bastion-faq.md).
* Information om hur du använder nätverkssäkerhetsgrupper med undernätet Azure Bastion finns i [Arbeta med NSG.To](bastion-nsg.md)use Network Security Groups with the Azure Bastion subnet, see Work with NSGs .
* Instruktioner som innehåller förklaringar av Azure Bastion-värdinställningar finns i [självstudien](bastion-create-host-portal.md).
* Information om hur du ansluter till en skaluppsättning för virtuella datorer finns i [Anslut till en skalningsuppsättning för virtuella datorer med Azure Bastion](bastion-connect-vm-scale-set.md).