---
title: Privat länk – Azure CLI – Azure Database for MariaDB
description: Lär dig hur du konfigurerar privat länk för Azure Database for MariaDB från Azure CLI
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: c28c5494c1cff2c198a94ea6b92003ae74ee2c8e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371808"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-cli"></a>Skapa och hantera en privat länk för Azure Database for MariaDB med CLI

En privat slut punkt är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, t. ex. Virtual Machines (VM), att kommunicera privat med privata länk resurser. I den här artikeln får du lära dig hur du använder Azure CLI för att skapa en virtuell dator i en Azure-Virtual Network och en Azure Database for MariaDB-server med en privat Azure-slutpunkt.

> [!NOTE]
> Den här funktionen är tillgänglig i alla Azure-regioner där Azure Database for MariaDB stöder Generell användning och minnesoptimerade pris nivåer.

## <a name="prerequisites"></a>Förutsättningar

För att gå igenom den här instruktions guiden behöver du:

- En [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i stället, måste du köra Azure CLI version 2.0.28 eller senare i den här snabbstarten. Kör `az --version` för att hitta den installerade versionen. Se [Installera Azure CLI](/cli/azure/install-azure-cli) för installations- eller uppgraderingsinformation.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa en resurs måste du skapa en resurs grupp som är värd för Virtual Network. Skapa en resursgrupp med [az group create](/cli/azure/group). I det här exemplet skapas en resurs grupp med namnet *myResourceGroup* på *westeurope* -platsen:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Skapa en Virtual Network
Skapa en Virtual Network med [AZ Network VNet Create](/cli/azure/network/vnet). I det här exemplet skapas en standard Virtual Network med namnet *myVirtualNetwork* med ett undernät med namnet *undernät*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Inaktivera privata slut punkts principer för undernät 
Azure distribuerar resurser till ett undernät i ett virtuellt nätverk, så du måste skapa eller uppdatera under nätet för att inaktivera nätverks principer för privata slut punkter. Uppdatera en under näts konfiguration med namnet *mitt undernät* med [AZ Network VNet Subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Skapa den virtuella datorn 
Skapa en virtuell dator med AZ VM Create. När du uppmanas anger du ett lösen ord som ska användas som inloggnings uppgifter för den virtuella datorn. I det här exemplet skapas en virtuell dator med namnet *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Observera den offentliga IP-adressen för den virtuella datorn. Du kommer att använda den här adressen när du ansluter till den virtuella datorn från Internet i nästa steg.

## <a name="create-an-azure-database-for-mariadb-server"></a>Skapa en Azure Database for MariaDB-server 
Skapa en Azure Database for MariaDB med kommandot AZ MariaDB Server Create. Kom ihåg att namnet på din MariaDB-Server måste vara unikt i Azure, så Ersätt plats hållarens värde inom hakparenteser med ditt eget unika värde: 

```azurecli-interactive
# Create a logical server in the resource group 
az mariadb server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Observera att MariaDB Server-ID liknar ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMariaDB/servers/servername.``` du kommer att använda MariaDB-serverns ID i nästa steg. 

## <a name="create-the-private-endpoint"></a>Skapa den privata slut punkten 
Skapa en privat slut punkt för MariaDB-servern i Virtual Network: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MariaDB Server ID>" \  
    --group-ids mariadbServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Konfigurera Privat DNS zon 
Skapa en Privat DNS zon för MariDB-Server domän och skapa en kopplings länk med Virtual Network. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mariadb.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mariadb.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MariaDB name 
 
 
#Create DNS records 
az network private-dns record-set a create --name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name mydemoserver --zone-name privatelink.mariadb.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> FQDN i DNS-inställningen för kunden matchar inte den privata IP-adressen som kon figurer ATS. Du måste konfigurera en DNS-zon för den konfigurerade FQDN: en som visas [här](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från internet

Anslut till VM- *myVm* från Internet på följande sätt:

1. Skriv *myVm*i portalens Sök fält.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna den *nedladdade RDP* -filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **Fler alternativ** > **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Få åtkomst till MariaDB-servern privat från den virtuella datorn

1. Öppna PowerShell i fjärr skrivbordet för *myVM*.

2. Ange  `nslookup mydemoserver.privatelink.mariadb.database.azure.com`. 

    Du får ett meddelande som liknar detta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Testa anslutningen till den privata länken för MariaDB-servern med valfri tillgänglig klient. I exemplet nedan har jag använt [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) för att utföra åtgärden.

4. I **ny anslutning**anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Anslutningsnamn| Välj önskat anslutnings namn.|
    | Värdnamn | Välj *mydemoserver.privatelink.MariaDB.Database.Azure.com* |
    | Användarnamn | Ange användar namn som *username@servername* som anges när MariaDB-servern skapas. |
    | lösenord | Ange ett lösen ord som angavs när MariaDB-servern skapades. |
    ||

5. Välj **Testa anslutning** eller **OK**.

6. Du kan också Bläddra bland databaser från den vänstra menyn och skapa eller fråga efter information från MariaDB-databasen

8. Stäng fjärr skrivbords anslutningen till myVm.

## <a name="clean-up-resources"></a>Rensa resurser 
När de inte längre behövs kan du använda AZ Group Delete för att ta bort resurs gruppen och alla resurser den har: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Nästa steg
Läs mer om [vad som är en privat Azure-slutpunkt](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
