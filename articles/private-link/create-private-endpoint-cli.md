---
title: Snabbstart – Skapa en privat Azure-slutpunkt med Azure CLI
description: Lär dig mer om Azures privata slutpunkt i den här snabbstarten
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: dbcb833e6f8b90cebd3d013e58168558bcd96827
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75459970"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Snabbstart: Skapa en privat slutpunkt med Azure CLI
Privat slutpunkt är den grundläggande byggstenen för Privat länk i Azure. Det gör det möjligt för Azure-resurser, som virtuella datorer ( VMs), att kommunicera privat med Private Link Resources. I den här snabbstarten får du lära dig hur du skapar en virtuell dator i ett virtuellt nätverk, en SQL Database Server med en privat slutpunkt med Azure CLI. Sedan kan du komma åt den virtuella datorn till och säkert komma åt den privata länkresursen (en privat Azure SQL Database-server i det här exemplet). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i stället, måste du köra Azure CLI version 2.0.28 eller senare i den här snabbstarten. Kör `az --version` för att hitta den installerade versionen. Se [Installera Azure CLI](/cli/azure/install-azure-cli) för installations- eller uppgraderingsinformation.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa en resurs måste du skapa en resursgrupp som är värd för det virtuella nätverket. Skapa en resursgrupp med [az group create](/cli/azure/group). I det här exemplet skapas en resursgrupp med namnet *myResourceGroup* på *platsen för westcentralus:*

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa ett virtuellt nätverk med [az nätverk vnet skapa](/cli/azure/network/vnet). I det här exemplet skapas ett virtuellt standardnätverk med namnet *myVirtualNetwork* med ett undernät med namnet *mySubnet:*

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>Inaktivera privata slutpunktsprinciper för undernät 
Azure distribuerar resurser till ett undernät i ett virtuellt nätverk, så du måste skapa eller uppdatera undernätet för att inaktivera privata principer för slutpunktsnätverk. Uppdatera en undernätskonfiguration med namnet *mySubnet* med [az network vnet subnet update:](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Skapa den virtuella datorn 
Skapa en virtuell dator med az vm skapa. Ange ett lösenord som ska användas som inloggningsuppgifter för den virtuella datorn när du uppmanas att göra det. I det här exemplet skapas en virtuell dator med namnet *myVm:* 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Observera den offentliga IP-adressen för den virtuella datorn. Du kommer att använda den här adressen när du ansluter till den virtuella datorn från Internet i nästa steg.

## <a name="create-a-sql-database-server"></a>Skapa en SQL Database Server 
Skapa en SQL Database Server med kommandot az sql server create. Kom ihåg att namnet på din SQL Server måste vara unikt i Hela Azure, så ersätt platshållarvärdet inom parentes med ditt eget unika värde: 

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

SQL Server-ID:et ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` liknar du kommer att använda SQL Server-ID i nästa steg. 

## <a name="create-the-private-endpoint"></a>Skapa den privata slutpunkten 
Skapa en privat slutpunkt för SQL Database-servern i det virtuella nätverket: 
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
## <a name="configure-the-private-dns-zone"></a>Konfigurera den privata DNS-zonen 
Skapa en privat DNS-zon för SQL Database-serverdomän och skapa en associationslänk med det virtuella nätverket. 
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

Anslut till VM *myVm* från internet enligt följande:

1. I portalens sökfält anger du *myVm*.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna filen downloaded.rdp*.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användarnamn och lösenord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **Fler alternativ** > **Använd ett annat konto**för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  

## <a name="access-sql-database-server-privately-from-the-vm"></a>Access SQL Database Server privat från den virtuella datorn

I det här avsnittet ansluter du till SQL Database Server från den virtuella datorn med hjälp av den privata slutpunkten.

 1. Öppna PowerShell i fjärrskrivbordet *för myVM.*
 2. Ange nslookup myserver.database.windows.net  Du får ett meddelande som liknar detta: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. Installera SQL Server Management Studio 
 4. Ange eller välj den här informationen i Anslut till servern: Servertyp: Välj Databasmotor.
 Servernamn: Välj användarnamn myserver.database.windows.net: Ange ett användarnamn som anges när du skapar det.
 Lösenord: Ange ett lösenord som anges när du skapar det.
 Kom ihåg lösenord: Välj Ja.
 
 5. Välj **Anslut**.
 6. Bläddra **i databaser** från vänstermeny.
 7. (Valfritt) Skapa eller fråga information från *min databas*
 8. Stäng fjärrskrivbordsanslutningen till *myVm*.

## <a name="clean-up-resources"></a>Rensa resurser 
När det inte längre behövs kan du använda az-gruppborttagning för att ta bort resursgruppen och alla resurser den har: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Private Link](private-link-overview.md)
