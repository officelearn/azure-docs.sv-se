---
title: Hantera privata slut punkter i Azure
description: Lär dig hur du skapar en privat slut punkt med hjälp av Azure Portal
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: e629a921b5c853f1cae2c224be35570cd71e5d37
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067799"
---
# <a name="create-a-private-endpoint-using-azure-portal"></a>Skapa en privat slut punkt med hjälp av Azure Portal

En privat slut punkt är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, t. ex. Virtual Machines (VM), att kommunicera privat med privata länk resurser. I den här snabb starten får du lära dig hur du skapar en virtuell dator på en Azure-Virtual Network, en SQL Database-Server med en privat Azure-slutpunkt med hjälp av Azure PowerShell. Sedan kan du på ett säkert sätt komma åt SQL Database-servern från den virtuella datorn.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vm"></a>Skapa en virtuell dator
I det här avsnittet skapar du ett virtuellt nätverk och under nätet som är värd för den virtuella datorn som används för åtkomst till din privata länk resurs (en SQL-Server i Azure i det här exemplet).

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket


I det här avsnittet ska du skapa en Virtual Network och under nätet som är värd för den virtuella datorn som används för åtkomst till din privata länk resurs.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.
1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Name | Ange *MyVirtualNetwork*. |
    | Adressutrymme | Ange *10.1.0.0/16*. |
    | Subscription | Välj din prenumeration.|
    | Resource group | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Location | Välj **WestCentralUS**.|
    | Undernät – Namn | Ange *undernät*. |
    | Undernät – adressintervall | Ange *10.1.0.0/24*. |
    |||
1. Lämna resten som standard och välj **skapa**.


### <a name="create-virtual-machine"></a>Skapa virtuell dator

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs** > **beräknings** > **virtuell dator**.

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Value |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Subscription | Välj din prenumeration. |
    | Resource group | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.  |
    | **INSTANSINFORMATION** |  |
    | Namn på virtuell dator | Ange *myVm*. |
    | Region | Välj **WestCentralUS**. |
    | Alternativ för tillgänglighet | Lämna kvar standardinställningen **Ingen infrastrukturredundans krävs**. |
    | Image | Välj **Windows Server 2019 Data Center**. |
    | Size | Lämna kvar standardinställningen **Standard DS1 v2**. |
    | **ADMINISTRATÖRSKONTO** |  |
    | Användarnamn | Ange ett användar namn som du väljer. |
    | lösenordsinställning | Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna kvar standardinställningen **Ingen**. |
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna kvar standardinställningen **Nej**. |
    |||

1. Välj **Nästa: Diskar**.

1. Lämna standardvärdena i **skapa en virtuell dator – diskar**och välj **nästa: Nätverk**.

1. I **Skapa en virtuell dator – nätverk** väljer du följande information:

    | Inställning | Value |
    | ------- | ----- |
    | Virtuellt nätverk | Lämna standard **MyVirtualNetwork**.  |
    | Adressutrymme | Lämna standard **10.1.0.0/24**.|
    | Subnet | Lämna standard **under nätet (10.1.0.0/24)** .|
    | Offentlig IP | Lämna standardinställningen **(ny) myVm-ip**. |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **HTTP** och **RDP**.|
    |||


1. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen.

1. När du ser meddelandet **valideringen har skickats** väljer du **skapa**.

## <a name="create-a-sql-database-server"></a>Skapa en SQL Database-Server
I det här avsnittet ska du skapa en SQL Database-Server i Azure. 

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs** > **databaser** > **SQL-databas**.

1. I **skapa SQL Database – grunderna**anger eller väljer du den här informationen:

    | Inställning | Value |
    | ------- | ----- |
    | **Databas information** | |
    | Subscription | Välj din prenumeration. |
    | Resource group | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | **INSTANSINFORMATION** |  |
    | Databasnamn  | Ange min *databas*. Om det här namnet tas skapar du ett unikt namn. |
    |||
5. I **Server**väljer du **Skapa ny**. 
6. I **ny server**anger eller väljer du den här informationen:

    | Inställning | Value |
    | ------- | ----- |
    |servernamn  | Ange *Server*. Om det här namnet tas skapar du ett unikt namn.|
    | Inloggning för serveradministratör| Ange ett administratörs namn som du väljer. |
    | lösenordsinställning | Ange ett valfritt lösenord. Lösen ordet måste vara minst 8 tecken långt och uppfylla de definierade kraven. |
    | Location | Välj en Azure-region där du vill att din SQL Server ska finnas. |
    
7. Välj **OK**. 
8. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen. 
9. När du ser meddelandet valideringen har skickats väljer du **skapa**. 
10. När du ser meddelandet valideringen har skickats väljer du skapa. 

## <a name="create-a-private-endpoint"></a>Skapa en privat slut punkt

I det här avsnittet ska du skapa en SQL-Server och lägga till en privat slut punkt i den. 

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs** > **nätverk** > **Private Link Center (för hands version)** .
2. I **privat länk Center – översikt**, på alternativet för att **skapa en privat anslutning till en tjänst**, väljer du **Start**.
1. I **skapa en privat slut punkt (för hands version) – grundläggande**anger eller väljer du den här informationen:

    | Inställning | Value |
    | ------- | ----- |
    | **Projekt information** | |
    | Subscription | Välj din prenumeration. |
    | Resource group | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | **INSTANSINFORMATION** |  |
    | Name | *Ange myPrivateEndpoint*. Om det här namnet tas skapar du ett unikt namn. |
    |Region|Välj **WestCentralUS**.|
    |||
5. Välj **nästa: Resurs**.
6. I **skapa en privat slut punkt – resurs**, anger eller väljer du den här informationen:

    | Inställning | Value |
    | ------- | ----- |
    |Anslutningsmetod  | Välj Anslut till en Azure-resurs i min katalog.|
    | Subscription| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft. SQL/Servers**. |
    | Resource |Välj *Server*|
    |Mål under resurs |Välj *sqlServer*|
    |||
7. Välj **nästa: Konfiguration**.
8. I **skapa en privat slut punkt (för hands version) – konfiguration**, anger eller väljer du den här informationen:

    | Inställning | Value |
    | ------- | ----- |
    |**NÄTVERK**| |
    | Virtuellt nätverk| Välj *MyVirtualNetwork*. |
    | Subnet | Välj *undernät*. |
    |**PRIVAT DNS-INTEGRERING**||
    |Integrera med privat DNS-zon |Välj **Ja**. |
    |Privat DNS zon |Välj *(ny) privatelink. Database. Windows. net* |
    |||

1. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen. 
2. När du ser meddelandet **valideringen har skickats** väljer du **skapa**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Ansluta till en virtuell dator med hjälp av fjärr skrivbord (RDP)


När du har skapat **myVm*ansluter du till den från Internet på följande sätt: 

1. Skriv *myVm*i portalens Sök fält.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna den nedladdade RDP *-filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **Fler alternativ** > **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Få åtkomst till SQL Database-servern privat från den virtuella datorn

1. Öppna PowerShell i fjärr skrivbordet för *myVM*.
2. Ange `nslookup myserver.database.windows.net`. 

    Du får ett meddelande som liknar detta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
3. Install [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. In **Connect to server**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **Database Engine**.|
    | Server name| Select *myserver.database.windows.net* |
    | User name | Enter a password provided during the SQL server creation. |
    |Password |Enter a password provided during the SQL server creation. |
    |Remember password|Select **Yes**.|
    |||
1. Select **Connect**.
2. Browse databases from left menu.
3. (Optionally) Create or query information from mydatabase.
4. Close the remote desktop connection to *myVm*. 

## Clean up resources 
When you're done using the private endpoint, SQL server, and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this quickstart, you created a VM on a virtual network, a SQL database server, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the SQL database server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint?](private-endpoint-overview.md).

