---
title: 'Självstudier: Lägga till en SQL Database Hanterad instans i en failover-grupp'
description: Lär dig hur du konfigurerar en failover-grupp för din Azure SQL Database hanterade instans.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 06/27/2019
ms.openlocfilehash: e4b7de3931c0d3508e5af6aa6bf85dfa18641aee
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624975"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Självstudier: Lägga till en SQL Database Hanterad instans i en failover-grupp

Lägg till en SQL Database Hanterad instans i en failover-grupp. I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> - Skapa en primär hanterad instans
> - Skapa en sekundär hanterad instans som en del av en [grupp för växling vid fel](sql-database-auto-failover-group.md). 
> - Testa redundans

  > [!NOTE]
  > Det kan ta lång tid att skapa en hanterad instans. Därför kan det ta flera timmar att slutföra den här självstudien. Mer information om etablerings tider finns i hanterings [åtgärder för hanterade instanser](sql-database-managed-instance.md#managed-instance-management-operations). Att använda Redundansrelationer med hanterade instanser är för närvarande en för hands version. 

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa den här självstudien måste du ha: 

- En Azure-prenumeration [skapar du ett kostnads fritt konto](https://azure.microsoft.com/free/) om du inte redan har en. 


## <a name="1----create-resource-group-and-primary-managed-instance"></a>1 – Skapa en resurs grupp och en primär hanterad instans
I det här steget ska du skapa resurs gruppen och den primära hanterade instansen för din failover-grupp med hjälp av Azure Portal. 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Välj att **skapa en resurs** i det övre vänstra hörnet av Azure Portal. 
1. Skriv `managed instance` i sökrutan och välj alternativet för Azure SQL-hanterad instans. 
1. Välj **skapa** för att starta sidan för skapande av **SQL-hanterad instans** . 
1. På sidan **skapa Azure SQL Database Hanterad instans** på fliken **grundläggande**
    1. Under **projekt information**väljer du din **prenumeration** i list rutan och väljer sedan att skapa en **ny** resurs grupp. Ange ett namn för resurs gruppen, till exempel `myResourceGroup`. 
    1. Under **information om hanterade instanser**anger du namnet på den hanterade instansen och den region där du vill distribuera din hanterade instans. Se till att välja en region med en [kopplad region](/azure/best-practices-availability-paired-regions). Lämna kvar **beräkningen + lagringen** med standardvärdena. 
    1. Under **administratörs konto**anger du en Administratörs inloggning, `azureuser`till exempel och ett komplext administratörs lösen ord. 

    ![Skapa primär MI](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Lämna resten av inställningarna med standardvärden och välj **Granska + skapa** för att granska inställningarna för hanterade instanser. 
1. Välj **skapa** för att skapa din primära hanterade instans. 


## <a name="2---create-a-virtual-network"></a>2 – Skapa ett virtuellt nätverk
I det här steget ska du skapa ett virtuellt nätverk för den sekundära hanterade instansen. Det här steget är nödvändigt eftersom det finns ett krav på att under nätet för de primära och sekundära hanterade instanserna har adress intervall som inte överlappar varandra. 

Följ dessa steg om du vill kontrol lera under näts intervallet för ditt primära virtuella nätverk:
1. I [Azure Portal](https://portal.azure.com)navigerar du till din resurs grupp och väljer det virtuella nätverket för din primära instans. 
1. Välj **undernät** under **Inställningar** och notera **adress intervallet**. Under nätets adress intervall för det virtuella nätverket för den sekundära hanterade instansen får inte överlappa detta. 


   ![Primärt undernät](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Följ dessa steg om du vill skapa ett virtuellt nätverk:

1. I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs** och söker efter *virtuellt nätverk*. 
1. Välj alternativet **Virtual Network** som publicerats av Microsoft och välj sedan **skapa** på nästa sida. 
1. Fyll i de obligatoriska fälten om du vill konfigurera det virtuella nätverket för den sekundära hanterade instansen och välj sedan **skapa**. 

   Följande tabell visar de värden som krävs för det sekundära virtuella nätverket:

    | **Fält** | Value |
    | --- | --- |
    | **Namn** |  Namnet på det virtuella nätverk som ska användas av den sekundära hanterade instansen, `vnet-sql-mi-secondary`till exempel. |
    | **Adressutrymme** | Adress utrymmet för det virtuella nätverket, till exempel `10.128.0.0/16`. | 
    | **Prenumeration** | Den prenumeration där den primära hanterade instansen och resurs gruppen finns. |
    | **Region** | Den plats där du ska distribuera den sekundära hanterade instansen. Detta ska finnas i en [kopplad region](/azure/best-practices-availability-paired-regions) till den primära hanterade instansen.  |
    | **Undernät** | Namnet på under nätet. `default`tillhandahålls som standard. |
    | **Adressintervall**| Adress intervallet för ditt undernät. Detta måste vara ett annat än det under näts adress intervall som används av den primära hanterade instansens virtuella nätverk `10.128.0.0/24`, till exempel.  |
    | &nbsp; | &nbsp; |

    ![Sekundära virtuella nätverks värden](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)


## <a name="3---create-a-secondary-managed-instance"></a>3 – skapa en sekundär hanterad instans
I det här steget ska du skapa en sekundär hanterad instans i Azure Portal, vilket även konfigurerar nätverket mellan de två hanterade instanserna. 

Din andra hanterade instans måste:
- Vara tomt. 
- Ligga inom en [kopplad region](/azure/best-practices-availability-paired-regions) med motsvarande motsvarighet till den primära hanterade instansen. 
- Ha ett annat undernät och IP-intervall än den primära hanterade instansen. 

Följ dessa steg om du vill skapa en sekundär hanterad instans: 

1. I [Azure Portal](http://portal.azure.com)väljer du **skapa en resurs** och söker efter *Azure SQL-hanterad instans*. 
1. Välj alternativet för **hanterad Azure SQL-instans** som publicerats av Microsoft och välj sedan **skapa** på nästa sida.
1. På fliken **grundläggande** på sidan **Skapa Azure SQL Database Hanterad instans** fyller du i fälten som krävs för att konfigurera din sekundära hanterade instans. 

   Följande tabell visar de värden som krävs för den sekundära hanterade instansen:
 
    | **Fält** | Value |
    | --- | --- |
    | **Prenumeration** |  Den prenumeration där din primära hanterade instans är. |
    | **Resursgrupp**| Resurs gruppen där den primära hanterade instansen finns. |
    | **Namn på hanterad instans** | Namnet på den nya sekundära hanterade instansen, till exempel`sql-mi-secondary`  | 
    | **Region**| Den [kopplade regionens](/azure/best-practices-availability-paired-regions) plats för din sekundära hanterade instans.  |
    | **Administratörsinloggning för hanterad instans** | Den inloggning som du vill använda för den nya sekundära hanterade instansen `azureuser`, till exempel. |
    | **Lösenord** | Ett komplext lösen ord som ska användas av administratörs inloggningen för den nya sekundära hanterade instansen.  |
    | &nbsp; | &nbsp; |

1. Under fliken **nätverk** väljer du det virtuella nätverk som du skapade för den sekundära hanterade instansen i list rutan för **Virtual Network**.

   ![Sekundärt MI-nätverk](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Under fliken **ytterligare inställningar** , för **geo-replikering**väljer du **Ja** för att _använda som sekundär redundans_. Välj den primära hanterade instansen i list rutan. 
    1. Se till att sorterings-och tids zonen matchar den primära hanterade instansen. Den primära hanterade instansen som skapas i den här `SQL_Latin1_General_CP1_CI_AS` självstudien använde `(UTC) Coordinated Universal Time` standardvärdet för sortering och tidszon. 

   ![Sekundärt MI-nätverk](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Välj **Granska + skapa** för att granska inställningarna för din sekundära hanterade instans. 
1. Välj **skapa** för att skapa en sekundär hanterad instans. 


## <a name="4---create-primary-virtual-network-gateway"></a>4 – Skapa primär virtuell nätverksgateway 

För två hanterade instanser som ska ingå i en failover-grupp måste det finnas en gateway som kon figurer ATS mellan de två hanterade instansernas virtuella nätverk för att tillåta nätverkskommunikation. Du kan skapa en gateway för den primära hanterade instansen med hjälp av Azure Portal:

1. I [Azure Portal](https://portal.azure.com)går du till din resurs grupp och väljer den **virtuella nätverks** resursen för din primära hanterade instans. 
1. Välj **undernät** under **Inställningar** och välj sedan för att lägga till ett nytt **Gateway-undernät**. Låt standardvärdena vara kvar. 

   ![Lägg till gateway för primär hanterad instans](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. När du har skapat en gateway för undernät väljer du **skapa en resurs** i det vänstra navigerings fönstret och `Virtual network gateway` skriver sedan i sökrutan. Välj den **virtuella nätverks-Gateway** -resurs som publicerats av **Microsoft**. 

   ![Skapa en ny virtuell nätverksgateway](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Fyll i de obligatoriska fälten om du vill konfigurera gatewayen för den primära hanterade instansen. 

   I följande tabell visas de värden som krävs för gatewayen för den primära hanterade instansen:
 
    | **Fält** | Value |
    | --- | --- |
    | **Prenumeration** |  Den prenumeration där din primära hanterade instans är. |
    | **Namn** | Namnet på din virtuella nätverksgateway, till exempel `primary-mi-gateway`. | 
    | **Region** | Den region där den sekundära hanterade instansen är. |
    | **Typ av Gateway** | Välj **VPN**. |
    | **VPN-typ** | Välj **Route-baserad** |
    | **SKU**| Lämna standardvärdet `VpnGw1`. |
    | **Location**| Den plats där din primära hanterade instans och det primära virtuella nätverket är.   |
    | **Virtuellt nätverk**| Välj det virtuella nätverk som skapades i avsnitt 2, till exempel `vnet-sql-mi-primary`. |
    | **Offentlig IP-adress**| Välj **Skapa ny**. |
    | **Namn på offentlig IP-adress**| Ange ett namn för din IP-adress, till `primary-gateway-IP`exempel. |
    | &nbsp; | &nbsp; |
1. Lämna de andra värdena som standard och välj sedan **Granska + skapa** för att granska inställningarna för din virtuella nätverksgateway.

   ![Inställningar för primär Gateway](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Välj **skapa** för att skapa din nya virtuella nätverksgateway. 

## <a name="5---configure-secondary-virtual-network-gateway"></a>5 – Konfigurera en sekundär virtuell nätverksgateway 

Upprepa stegen i föregående avsnitt för att skapa det virtuella nätverkets undernät och gateway för den sekundära hanterade instansen. Fyll i de obligatoriska fälten om du vill konfigurera gatewayen för din sekundära hanterade instans. 

   I följande tabell visas de värden som krävs för gatewayen för den sekundära hanterade instansen:

   | **Fält** | Value |
   | --- | --- |
   | **Prenumeration** |  Prenumerationen där den sekundära hanterade instansen är. |
   | **Namn** | Namnet på din virtuella nätverksgateway, till exempel `secondary-mi-gateway`. | 
   | **Region** | Den region där den sekundära hanterade instansen är. |
   | **Typ av Gateway** | Välj **VPN**. |
   | **VPN-typ** | Välj **Route-baserad** |
   | **SKU**| Lämna standardvärdet `VpnGw1`. |
   | **Location**| Den plats där den sekundära hanterade instansen och det sekundära virtuella nätverket är.   |
   | **Virtuellt nätverk**| Välj det virtuella nätverk som skapades i avsnitt 2, till exempel `vnet-sql-mi-secondary`. |
   | **Offentlig IP-adress**| Välj **Skapa ny**. |
   | **Namn på offentlig IP-adress**| Ange ett namn för din IP-adress, till `secondary-gateway-IP`exempel. |
   | &nbsp; | &nbsp; |

   ![Inställningar för sekundär Gateway](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

## <a name="6---connect-the-gateways"></a>6 – Anslut gatewayerna
I det här steget skapar du en anslutning mellan gatewayer. En anslutning måste upprättas från den primära till den sekundära gatewayen och en separat anslutning måste upprättas mellan den sekundära till den primära gatewayen. Se till att använda samma **delade nyckel** när du konfigurerar anslutningen mellan båda gatewayerna. 

Följ dessa steg om du vill konfigurera anslutningen:

1. Navigera till din resurs grupp i [Azure Portal](https://portal.azure.com) och välj den primära gateway som du skapade i steg 4. 
1. Välj **anslutningar** under **Inställningar** och välj sedan **Lägg till** för att skapa en ny anslutning. 

   ![Lägg till anslutning till primär Gateway](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. Ange ett namn för anslutningen, till exempel `Primary-connection`och ange ett värde för den **delade** `mi1mi2psk`nyckeln, till exempel. 
1. Välj den **andra virtuella Nätverksgatewayen** och välj sedan en gateway för den sekundära hanterade instansen, `secondary-mi-gateway`till exempel. 

   ![Skapa primär till sekundär anslutning](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. Välj **OK** för att lägga till din nya anslutning från primär till sekundär Gateway.
1. Upprepa de här stegen för att skapa en anslutning från gatewayen för den sekundära hanterade instansen till gatewayen för den primära hanterade instansen. 

   ![Skapa sekundär till primär anslutning](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)


## <a name="7---create-a-failover-group"></a>7 – skapa en grupp för redundans
I det här steget skapar du gruppen redundans och lägger till båda hanterade instanser till den. 

1. I [Azure Portal](https://portal.azure.com)går du till **alla tjänster** och skriver i `managed instance` rutan Sök. 
1. Valfritt Välj stjärnan bredvid SQL- **hanterade instanser** för att lägga till hanterade instanser som genväg till det vänstra navigerings fältet. 
1. Välj **SQL-hanterade instanser** och välj din primära hanterade instans `sql-mi-primary`, till exempel. 
1. Under **Inställningar**navigerar du till **instans grupper för redundans** och väljer sedan att **lägga till grupp** för att öppna sidan **instans redundans grupp** . 

   ![Lägg till en grupp för redundans](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. På sidan **instans redundans** anger du namnet på din grupp för redundans, till exempel `failovergrouptutorial` och väljer sedan den sekundära hanterade `sql-mi-secondary` instansen, till exempel från List rutan. Välj **skapa** för att skapa din grupp för redundans. 

   ![Skapa redundans grupp](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. När redundansväxlingen är klar kommer du tillbaka till sidan för **redundans** . 

## <a name="8---test-failover"></a>8-testa redundans
I det här steget kommer du inte att kunna redundansväxla gruppen till den sekundära servern och sedan växla tillbaka med Azure Portal. 

1. Navigera till din hanterade instans i [Azure Portal](https://portal.azure.com) och välj **instans grupper för redundans** under Inställningar. 
1. Granska vilken hanterad instans som är primär och vilken hanterad instans som är sekundär. 
1. Välj **redundans** och välj sedan **Ja** på varningen om TDS-sessioner som kopplas från. 

   ![Redundansväxla failover-gruppen](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Granska vilken hanterade-instans som är primär och vilken instans som är sekundär. Om redundans har slutförts ska de två instanserna ha växlade roller. 

   ![Hanterade instanser har växlat roller efter redundans](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Välj **redundans** igen så att den primära instansen inte kan återställas till den primära rollen igen. 


## <a name="clean-up-resources"></a>Rensa resurser
Rensa resurser genom att först ta bort den hanterade instansen, sedan det virtuella klustret, sedan eventuella återstående resurser och slutligen resurs gruppen. 

1. Navigera till din resurs grupp i [Azure Portal](https://portal.azure.com). 
1. Välj den hanterade instansen och välj sedan **ta bort**. Skriv `yes` text rutan för att bekräfta att du vill ta bort resursen och välj sedan **ta bort**. Den här processen kan ta lite tid att slutföra i bakgrunden och tills den är klar kommer du inte att kunna ta bort det *virtuella klustret* eller andra beroende resurser. Övervaka borttagningen på fliken aktivitet för att bekräfta att den hanterade instansen har tagits bort. 
1. När den hanterade instansen har tagits bort tar du bort det *virtuella klustret* genom att markera det i resurs gruppen och sedan välja **ta bort**. Skriv `yes` text rutan för att bekräfta att du vill ta bort resursen och välj sedan **ta bort**. 
1. Ta bort eventuella återstående resurser. Skriv `yes` text rutan för att bekräfta att du vill ta bort resursen och välj sedan **ta bort**. 
1. Ta bort resurs gruppen genom att välja **ta bort resurs**grupp, Skriv namnet på resurs gruppen `myResourceGroup`och välj sedan **ta bort**. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du en redundans grupp mellan två hanterade instanser. Du har lärt dig att:

> [!div class="checklist"]
> - Skapa en primär hanterad instans
> - Skapa en sekundär hanterad instans som en del av en [grupp för växling vid fel](sql-database-auto-failover-group.md). 
> - Testa redundans

Gå vidare till nästa snabb start när du ansluter till din hanterade instans och hur du återställer en databas till din hanterade instans: 

> [!div class="nextstepaction"]
> [Anslut till din hanterade instans](sql-database-managed-instance-configure-vm.md)
> [Återställ en databas till en hanterad instans](sql-database-managed-instance-get-started-restore.md)


