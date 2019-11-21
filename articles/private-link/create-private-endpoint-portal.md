---
title: Manage Private Endpoints in Azure
description: Learn how to create a Private Endpoint using the Azure portal
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 50e358684ebef82e96c4c21e6139434f8581595a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224790"
---
# <a name="create-a-private-endpoint-using-azure-portal"></a>Create a Private Endpoint using Azure portal

A Private Endpoint is the fundamental building block for private link in Azure. It enables Azure resources, like Virtual Machines (VMs), to communicate privately with private link resources. In this Quickstart, you will learn how to create a VM on an Azure Virtual Network, a  SQL Database Server with an Azure private endpoint using the Azure Portal. Then, you can securely access the SQL Database Server from the VM.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


> [!NOTE]
> Private endpoint(s) are not permitted in conjunction with service endpoints in the same subnet!

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vm"></a>Skapa en virtuell dator
In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link resource (a SQL server in Azure in this example).

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket


In this section, you will create a Virtual Network and the subnet to host the VM that is used to access your Private Link resource.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.
1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Enter *MyVirtualNetwork*. |
    | Adressutrymme | Ange *10.1.0.0/16*. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Plats | Select **WestCentralUS**.|
    | Undernät – namn | Enter *mySubnet*. |
    | Undernät – adressintervall | Ange *10.1.0.0/24*. |
    |||
1. Leave the rest as default and select **Create**.


### <a name="create-virtual-machine"></a>Skapa virtuell dator

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Compute** > **Virtual Machine**.

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. You created this in the previous section.  |
    | **INSTANSINFORMATION** |  |
    | Namn på virtuell dator | Enter *myVm*. |
    | Region | Select **WestCentralUS**. |
    | Alternativ för tillgänglighet | Lämna kvar standardinställningen **Ingen infrastrukturredundans krävs**. |
    | Bild | Select **Windows Server 2019 Datacenter**. |
    | Storlek | Lämna kvar standardinställningen **Standard DS1 v2**. |
    | **ADMINISTRATÖRSKONTO** |  |
    | Användarnamn | Enter a username of your choosing. |
    | Lösenord | Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna kvar standardinställningen **Ingen**. |
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna kvar standardinställningen **Nej**. |
    |||

1. Select **Next: Disks**.

1. In **Create a virtual machine - Disks**, leave the defaults and select **Next: Networking**.

1. I **Skapa en virtuell dator – nätverk** väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Leave the default **MyVirtualNetwork**.  |
    | Adressutrymme | Leave the default **10.1.0.0/24**.|
    | Undernät | Leave the default **mySubnet (10.1.0.0/24)** .|
    | Offentlig IP-adress | Lämna standardinställningen **(ny) myVm-ip**. |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **HTTP** och **RDP**.|
    |||


1. Välj **Granska + skapa**. You're taken to the **Review + create** page where Azure validates your configuration.

1. When you see the **Validation passed** message, select **Create**.

## <a name="create-a-sql-database-server"></a>Create a SQL database server
In this section, you will create a SQL database server in Azure. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Databases** > **SQL database**.

1. In **Create SQL database - Basics**, enter or select this information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Database details** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. You created this in the previous section.|
    | **INSTANSINFORMATION** |  |
    | Databasnamn  | Enter *mydatabase*. If this name is taken, create a unique name. |
    |||
5. In **Server**, select **Create new**. 
6. In **New server**, enter or select this information:

    | Inställning | Värde |
    | ------- | ----- |
    |servernamn  | Enter *myserver*. If this name is taken, create a unique name.|
    | inloggning för serveradministratör| Enter an administrator name of your choosing. |
    | Lösenord | Ange ett valfritt lösenord. The password must be at least 8 characters long and meet the defined requirements. |
    | Plats | Select an Azure region where you want to want your SQL Server to reside. |
    
7. Välj **OK**. 
8. Välj **Granska + skapa**. You're taken to the **Review + create** page where Azure validates your configuration. 
9. When you see the Validation passed message, select **Create**. 
10. When you see the Validation passed message, select Create. 

## <a name="create-a-private-endpoint"></a>Create a private endpoint

In this section, you will create a SQL server and add a private endpoint to it. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Networking** > **Private Link Center (Preview)** .
2. In **Private Link Center - Overview**, on the option to **Build a private connection to a service**, select **Start**.
1. In **Create a private endpoint (Preview) - Basics**, enter or select this information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Project details** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. You created this in the previous section.|
    | **INSTANSINFORMATION** |  |
    | Namn | Enter * myPrivateEndpoint*. If this name is taken, create a unique name. |
    |Region|Select **WestCentralUS**.|
    |||
5. Select **Next: Resource**.
6. In **Create a private endpoint - Resource**, enter or select this information:

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Select connect to an Azure resource in my directory.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Select **Microsoft.Sql/servers**. |
    | Resurs |Select *myServer*|
    |Target sub-resource |Select *sqlServer*|
    |||
7. Select **Next: Configuration**.
8. In **Create a private endpoint (Preview) - Configuration**, enter or select this information:

    | Inställning | Värde |
    | ------- | ----- |
    |**NETWORKING**| |
    | Virtuellt nätverk| Select *MyVirtualNetwork*. |
    | Undernät | Select *mySubnet*. |
    |**PRIVATE DNS INTEGRATION**||
    |Integrate with private DNS zone |Välj **Ja**. |
    |Private DNS Zone |Select *(New)privatelink.database.windows.net* |
    |||

1. Välj **Granska + skapa**. You're taken to the **Review + create** page where Azure validates your configuration. 
2. When you see the **Validation passed** message, select **Create**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Connect to a VM using Remote Desktop (RDP)


After you've created **myVm*, connect to it from the internet as follows: 

1. In the portal's search bar, enter *myVm*.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Open the downloaded.rdp* file.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Enter the username and password you specified when creating the VM.

        > [!NOTE]
        > Du kan behöva välja **Fler alternativ** > **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Access the SQL database server privately from the VM

1. In the Remote Desktop of *myVM*, open PowerShell.

2. Enter `nslookup myserver.database.windows.net`. 

    You'll receive a message similar to this:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Install [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. In **Connect to server**, enter or select this information:

    | Inställning | Värde |
    | ------- | ----- |
    | Servertyp| Välj **Databasmotor**.|
    | servernamn| Select *myserver.database.windows.net* |
    | Användarnamn | Enter username as username@servername which is provided during the SQL server creation. |
    |Lösenord |Enter a password provided during the SQL server creation. |
    |Remember password|Välj **Ja**.|
    |||
1. Välj **Anslut**.
2. Browse databases from left menu.
3. (Optionally) Create or query information from mydatabase.
4. Close the remote desktop connection to *myVm*. 

## <a name="clean-up-resources"></a>Rensa resurser 
When you're done using the private endpoint, SQL server, and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Välj **Ta bort resursgrupp**. 
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## <a name="next-steps"></a>Nästa steg

In this quickstart, you created a VM on a virtual network, a SQL database server, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the SQL database server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint?](private-endpoint-overview.md).

