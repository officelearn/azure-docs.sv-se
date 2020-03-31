---
title: 'Skapa en Azure Bastion-värd: portal'
description: I den här artikeln får du lära dig hur du skapar en Azure Bastion-värd med hjälp av portalen
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 14a596d78fb1f560c62013e7e439ed60d3a29b8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366151"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Skapa en Azure Bastion-värd med hjälp av portalen

Den här artikeln visar hur du skapar en Azure Bastion-värd med Azure-portalen. När du har etablerat Azure Bastion-tjänsten i ditt virtuella nätverk är den sömlösa RDP/SSH-upplevelsen tillgänglig för alla virtuella datorer i samma virtuella nätverk. Azure Bastion-distributionen är per virtuellt nätverk, inte per prenumeration/konto eller virtuell dator.

Du kan skapa en ny skyddsvärdresurs i portalen antingen genom att ange alla inställningar manuellt eller genom att använda inställningarna som motsvarar en befintlig virtuell dator. Om du vill skapa en skyddsvärd med hjälp av vm-inställningar läser du [snabbstartsartikeln.](quickstart-host-portal.md) Du kan också använda [Azure PowerShell](bastion-create-host-powershell.md) för att skapa en Azure Bastion-värd.

## <a name="before-you-begin"></a>Innan du börjar

Bastion är tillgängligt i följande offentliga Azure-regioner:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Skapa en Bastion-värd

Det här avsnittet hjälper dig att skapa en ny Azure Bastion-resurs från Azure-portalen.

1. På [Azure-portalmenyn](https://portal.azure.com) eller på **startsidan** väljer du **Skapa en resurs**.

1. På den **nya** sidan, i fältet *Sök på Marketplace,* skriver du **Bastion**och klickar sedan på **Retur** för att komma åt sökresultaten.

1. Klicka på **Bastion**i resultatet . Kontrollera att utgivaren är *Microsoft* och att kategorin är *Nätverk.*

1. På sidan **Bastion** klickar du på **Skapa** för att öppna sidan **Skapa en bastion.**

1. Konfigurera en ny Bastion-resurs på sidan **Skapa en bastion.** Ange konfigurationsinställningarna för din Bastion-resurs.

    ![skapa en bastion](./media/bastion-create-host-portal/settings.png)

    * **Prenumeration**: Den Azure-prenumeration som du vill använda för att skapa en ny Bastion-resurs.
    * **Resursgrupp:** Den Azure-resursgrupp där den nya Bastion-resursen skapas i. Om du inte har en befintlig resursgrupp kan du skapa en ny.
    * **Namn**: Namnet på den nya Bastion-resursen
    * **Region**: Den offentliga Azure-regionen som resursen kommer att skapas i.
    * **Virtuellt nätverk:** Det virtuella nätverk där Bastion-resursen kommer att skapas i. Du kan skapa ett nytt virtuellt nätverk i portalen under den här processen eller använda ett befintligt virtuellt nätverk. Om du använder ett befintligt virtuellt nätverk kontrollerar du att det befintliga virtuella nätverket har tillräckligt med ledigt adressutrymme för att uppfylla kraven i Bastion-undernätet.
    * **Undernät:** Undernätet i det virtuella nätverket som den nya bastion-värdresursen ska distribueras till. Du måste skapa ett undernät med namnvärdet **AzureBastionSubnet**. Det här värdet gör att Azure vet vilket undernät som bastionresurserna ska distribueras till. Detta skiljer sig från ett gateway-undernät. Du måste använda ett undernät på minst /27 eller större (/27, /26 och så vidare).
    
       Skapa **AzureBastionSubnet** utan några vägtabeller eller delegeringar. Om du använder nätverkssäkerhetsgrupper på **AzureBastionSubnet**läser du artikeln [Arbeta med NSG:er.](bastion-nsg.md)
    * **Offentlig IP-adress**: Den offentliga IP-adressen för den Bastion-resurs som RDP/SSH kommer att nås på (över port 443). Skapa en ny offentlig IP eller använd en befintlig. Den offentliga IP-adressen måste finnas i samma region som den Bastion-resurs som du skapar.
    * **Namn på offentlig IP-adress**: Namnet på den offentliga IP-adressresursen.
    * **Allmän IP-adress SKU:** Den här inställningen är förifylld som standard **till Standard**. Azure Bastion använder/stöder endast Standard Public IP SKU.
    * **Tilldelning**: Den här inställningen är förifylld som standard **statisk**.

1. När du har angett inställningarna klickar du på **Granska + Skapa**. Detta validerar värdena. När valideringen har gått kan du påbörja skapandeprocessen.
1. Klicka på **Skapa**på sidan **Skapa en bastion** .
1. Du kommer att se ett meddelande som talar om att distributionen pågår. Status visas på den här sidan när resurserna skapas. Det tar cirka 5 minuter för Bastion-resursen att skapas och distribueras.

## <a name="next-steps"></a>Nästa steg

* Läs [vanliga frågor om Bastion](bastion-faq.md) för ytterligare information.

* Information om hur du använder nätverkssäkerhetsgrupper med undernätet Azure Bastion finns i [Arbeta med NSG.To](bastion-nsg.md)use Network Security Groups with the Azure Bastion subnet, see Work with NSGs .