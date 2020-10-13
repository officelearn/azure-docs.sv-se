---
title: 'Skapa en Azure skydds-värd: virtuell Windows-dator: Portal'
description: I den här artikeln får du lära dig hur du skapar en Azure skydds-värd och ansluter till en virtuell Windows-dator.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 10/09/2020
ms.author: cherylmc
ms.openlocfilehash: e4f8bc38a79fdbb84624e126c84d679a906a701b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91980173"
---
# <a name="tutorial-create-an-azure-bastion-host-and-connect-to-a-windows-vm"></a>Självstudie: skapa en Azure skydds-värd och Anslut till en virtuell Windows-dator

Du kan ansluta till en virtuell Windows-dator (VM) via en webbläsare via Azure Portal. Anslut till din virtuella dator direkt via webbläsaren via TLS med en privat IP-adress. Du behöver inte installera och konfigurera klient programmet lokalt.

Om du vill ansluta till en virtuell dator med skydds måste du etablera Azure skydds-tjänsten i ditt virtuella nätverk (VNet). När tjänsten har tillhandahållits är den här typen av anslutning tillgänglig för alla virtuella datorer som finns i det virtuella nätverket, beroende på den virtuella datorns säkerhets konfiguration. Azure skydds-distributionen är per virtuellt nätverk, inte per prenumeration/konto eller virtuell dator.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Skapa en skydds-värd för ditt VNet
> * Ansluta till en virtuell Windows-dator

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Ett virtuellt nätverk.
* En virtuell Windows-dator i det virtuella nätverket.
* Följande nödvändiga roller:
  * Rollen läsare på den virtuella datorn.
  * Rollen läsare på NÄTVERKSKORTet med den virtuella datorns privata IP-adress.
  * Rollen läsare på Azure skydds-resursen.

* Portar: för att ansluta till den virtuella Windows-datorn måste du ha följande portar öppna på din virtuella Windows-dator:
  * Inkommande portar: RDP (3389)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Skapa en skydds-värd

Det här avsnittet hjälper dig att skapa skydds-objektet i ditt VNet. Detta krävs för att skapa en säker anslutning till en virtuell dator i det virtuella nätverket.

1. På **Start** sidan väljer du **+ skapa en resurs**.
1. På sidan **ny** i rutan Sök skriver du **skydds**och väljer sedan **RETUR** för att komma till Sök resultaten. På resultatet för **skydds**kontrollerar du att utgivaren är Microsoft.
1. Välj **Skapa**.
1. På sidan **skapa en skydds** konfigurerar du en ny skydds-resurs.

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-basics.png" alt-text="Skapa en Bastion-värd" lightbox="./media/tutorial-create-host-portal/bastion-basics.png":::

    * **Prenumeration**: den Azure-prenumeration som du vill använda för att skapa en ny skydds-resurs.
    * **Resurs grupp**: Azure-resurs gruppen där den nya skydds-resursen ska skapas. Om du inte har en befintlig resurs grupp kan du skapa en ny.
    * **Namn**: namnet på den nya skydds-resursen.
    * **Region**: den offentliga Azure-region som resursen ska skapas i.
    * **Virtuellt nätverk**: det virtuella nätverk där skydds-resursen ska skapas. Du kan skapa ett nytt virtuellt nätverk i portalen under den här processen eller använda ett befintligt virtuellt nätverk. Om du använder ett befintligt virtuellt nätverk kontrollerar du att det befintliga virtuella nätverket har tillräckligt med ledigt adress utrymme för att uppfylla skydds-undernätets krav. Om du inte ser det virtuella nätverket i list rutan kontrollerar du att du har valt rätt resurs grupp.
    * **Undernät**: när du har skapat eller valt ett virtuellt nätverk visas fältet undernät. Under nätet i det virtuella nätverket där den nya skydds-värden ska distribueras. Under nätet är ett dedikerat skydds-värde. Välj **Hantera under näts konfiguration** och skapa Azure skydds-undernätet. Välj **+ undernät** och skapa ett undernät med hjälp av följande rikt linjer:

         * Under nätet måste ha namnet **AzureBastionSubnet**.
         * Under nätet måste vara minst/27 eller större.

      Du behöver inte fylla i ytterligare fält. Välj **OK** och välj sedan **skapa en skydds** längst upp på sidan och gå tillbaka till konfigurations sidan för skydds.
    * **Offentlig IP-adress**: den offentliga IP-adressen för den skydds-resurs som RDP/SSH kommer att få åtkomst till (via port 443). Skapa en ny offentlig IP-adress. Den offentliga IP-adressen måste finnas i samma region som den skydds-resurs som du skapar. Det här är en IP-adress som inte har något att göra med någon av de virtuella datorer som du vill ansluta till. Det är den offentliga IP-adressen för skydds-värd resursen.
    * **Namn på offentlig IP-adress**: namnet på den offentliga IP-adressresursen. I den här självstudien kan du lämna standardvärdet.
    * **SKU för offentlig IP-adress**: den här inställningen fylls i automatiskt **som standard.** Azure skydds använder/stöder bara standard-offentliga IP SKU.
    * **Tilldelning**: den här inställningen är förifylld som standard som **statisk**.

1. När du är klar med att ange inställningarna väljer du **Granska + skapa**. Detta validerar värdena. När verifieringen har godkänts kan du skapa skydds-resursen.
1. Välj **Skapa**.
1. Ett meddelande visas där du vet att distributionen pågår. Statusen visas på den här sidan när resurserna skapas. Det tar ungefär 5 minuter för skydds-resursen att skapas och distribueras.

## <a name="connect-to-a-vm"></a>Ansluta till en virtuell dator

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort dina resurser med följande steg:

1. Ange namnet på din resurs grupp i rutan **Sök** högst upp i portalen. När du ser resurs gruppen i Sök resultaten väljer du den.
1. Välj **Ta bort resursgrupp**.
1. Ange namnet på resurs gruppen för **Skriv resurs gruppens namn:** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en skydds-värd och kopplat den till ett virtuellt nätverk och anslutit till en virtuell Windows-dator. Du kan välja att använda nätverks säkerhets grupper med ditt Azure skydds-undernät. För att göra det, se [arbeta med NSG: er](bastion-nsg.md).
