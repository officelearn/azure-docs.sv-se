---
title: Quickstart - Create an Azure private endpoint using Azure CLI
description: Learn about Azure private endpoint in this Quickstart
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 67513c2155e956e005b143c3049abe70a2f126f2
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419823"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Quickstart: Create a private endpoint using Azure CLI
Private Endpoint is the fundamental building block for Private Link in Azure. It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link Resources. In this Quickstart, you will learn how to create a VM on a virtual network, a SQL Database Server with a Private Endpoint using Azure CLI. Then, you can access the VM to and securely access the private link resource (a private Azure SQL Database server in this example). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i stället, måste du köra Azure CLI version 2.0.28 eller senare i den här snabbstarten. Kör `az --version` för att hitta den installerade versionen. Se [Installera Azure CLI](/cli/azure/install-azure-cli) för installations- eller uppgraderingsinformation.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Before you can create any resource, you have to create a resource group to host the Virtual Network. Skapa en resursgrupp med [az group create](/cli/azure/group). This example creates a resource group named *myResourceGroup* in the *westcentralus* location:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Create a Virtual Network
Create a Virtual Network with [az network vnet create](/cli/azure/network/vnet). This example creates a default Virtual Network named *myVirtualNetwork* with one subnet named *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>Disable subnet private endpoint policies 
Azure deploys resources to a subnet within a virtual network, so you need to create or update the subnet to disable private endpoint network policies. Update a subnet configuration named *mySubnet* with [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Skapa den virtuella datorn 
Create a VM with az vm create. When prompted, provide a password to be used as the sign-in credentials for the VM. This example creates a VM named *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Note the public IP address of the VM. Du kommer att använda den här adressen när du ansluter till den virtuella datorn från Internet i nästa steg.

## <a name="create-a-sql-database-server"></a>Create a SQL Database Server 
Create a SQL Database Server with the az sql server create command. Remember that the name of your SQL Server must be unique across Azure, so replace the placeholder value in brackets with your own unique value: 

```azurecli-interactive
# Create a logical server in the resource group 
az sql server create \ 
    --name "myserver"\ 
    --resource-group myResourceGroup \ 
    --location WestUS \ 
    --admin-user "sqladmin" \ 
    --admin-password "CHANGE_PASSWORD_1" 
 
# Create a database in the server with zone redundancy as false 
az sql db create \ 
    --resource-group myResourceGroup  \ 
    --server myserver \ 
    --name mySampleDatabase \ 
    --sample-name AdventureWorksLT \ 
    --edition GeneralPurpose \ 
    --family Gen4 \ 
    --capacity 1 
```

Note the SQL Server ID is similar to ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` You will use the SQL Server ID in the next step. 

## <a name="create-the-private-endpoint"></a>Create the Private Endpoint 
Create a private endpoint for the SQL Database server in your Virtual Network: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<SQL Server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```
## <a name="configure-the-private-dns-zone"></a>Configure the Private DNS Zone 
Create a Private DNS Zone for SQL Database server domain and create an association link with the Virtual Network. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.windows.net" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.windows.net"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the SQL server name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Connect to the VM *myVm* from the internet as follows:

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

## <a name="access-sql-database-server-privately-from-the-vm"></a>Access SQL Database Server privately from the VM

In this section, you will connect to the SQL Database Server from the VM using the Private Endpoint.

 1. In the Remote Desktop of *myVM*, open PowerShell.
 2. Enter nslookup myserver.database.windows.net  You'll receive a message similar to this: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. Install SQL Server Management Studio 
 4. In Connect to server, enter or select this information: Server type: Select Database Engine.
 Server name: Select myserver.database.windows.net Username: Enter a username provided during creation.
 Password: Enter a password provided during creation.
 Remember password: Select Yes.
 
 5. Välj **Anslut**.
 6. Browse **Databases** from left menu.
 7. (Optionally) Create or query information from *mydatabase*
 8. Close the remote desktop connection to *myVm*.

## <a name="clean-up-resources"></a>Rensa resurser 
When no longer needed, you can use az group delete to remove the resource group and all the resources it has: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Nästa steg
- Learn more about [Azure Private Link](private-link-overview.md)
