---
title: Azures privata Endpoint ARM-mall
description: Lär dig mer om Azures privata länk
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 97b38d9db53db5c53090df54b283e6e2c85f3e88
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84172342"
---
# <a name="create-a-private-endpoint---resource-manager-template"></a>Skapa en privat slut punkt – Resource Manager-mall

I den här snabb starten använder du en Resource Manager-mall för att skapa en privat slut punkt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Du kan också slutföra den här snabb starten med [Azure Portal](create-private-endpoint-portal.md), [Azure POWERSHELL](create-private-endpoint-powershell.md)eller [Azure CLI](create-private-endpoint-cli.md).

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-private-endpoint"></a>Skapa en privat slut punkt

den här mallen skapar en privat slut punkt för en Azure SQL-Server.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/101-private-endpoint-sql/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json" range="001-295" highlight="131-156":::

Flera Azure-resurser definieras i mallen:


- [**Microsoft. Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints) : privat slut punkt för att få åtkomst till Azure SQL-servern privat
- [**Microsoft. Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones) : används för att matcha den privata slut PUNKTens IP-adress 
- [**Microsoft. Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft. Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups) : associera privat slut punkt med en privat DNS-zon
- [**Microsoft. SQL/Servers**](/azure/templates/microsoft.sql/servers) : Azure SQL Server med exempel databasen
- [**Microsoft. SQL/Servers/databaser**](/azure/templates/microsoft.sql/servers/databases) : exempel databas
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : Virtual Network där den privata slut punkten distribueras 
- [**Microsoft. Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses) : offentlig IP-adress för att få åtkomst till den virtuella datorn
- [**Microsoft. Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : virtuell dator för att testa den privata anslutningen med privat slut punkt till Azure SQL Server
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : nätverks gränssnitt för den virtuella datorn 

### <a name="deploy-the-template"></a>Distribuera mallen

Distribuera Resource Manager-mall till Azure:

1. Välj **distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar den privata slut punkten, Azure SQL Server, nätverks infrastrukturen och en virtuell dator som ska verifieras.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Välj eller skapa en resurs grupp.
3. Ange inloggning och lösen ord för SQL-administratören.
3. Ange administratörs användar namn och lösen ord för den virtuella datorn.
3. Välj **Jag accepterar de villkor som anges ovan** och välj sedan **köp**. Distributionen kan ta 20 minuter eller längre att slutföra.

## <a name="validate-the-deployment"></a>Verifiera distributionen
> [!NOTE]
> ARM-mallen genererar unikt namn för den virtuella datorns myVm<b>{UniqueID}</b> -resurs och för Azure SQL Server SQLServer<b>{UniqueID}</b> -resursen ersätter du <b>{UniqueID}</b> med det genererade värdet.

### <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Anslut till VM- *myVm {UniqueID}* från Internet på följande sätt:

1. I portalens Sök fält anger du *myVm {UniqueID}*.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna den nedladdade RDP *-filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **fler alternativ**  >  **Använd ett annat konto**för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  

### <a name="access-sql-database-server-privately-from-the-vm"></a>Åtkomst SQL Database Server privat från den virtuella datorn

I det här avsnittet ska du ansluta till SQL Database servern från den virtuella datorn med hjälp av den privata slut punkten.

 1. Öppna PowerShell i fjärr skrivbordet för *myVM {UniqueID}*.
 2. Ange nslookup SQLServer {UniqueID}. Database. Windows. net   du får ett meddelande som liknar detta: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    sqlserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  sqlserver.database.windows.net 
```
 3. Installera SQL Server Management Studio 
 4. I Anslut till server anger eller väljer du följande information: Server Typ: Välj databas motor.
 Server Namn: Välj SQLServer {UniqueID}. databas. Windows. net username: Ange ett användar namn som angavs vid skapandet.
 Lösen ord: Ange ett lösen ord som anges när du skapar.
 Kom ihåg lösen ord: Välj Ja.
 
 5. Välj **Anslut**.
 6. Bläddra bland **databaser** från menyn till vänster.
 7. Du kan också Skapa eller fråga efter information från *exempel-DB*
 8. Stäng fjärr skrivbords anslutningen till *myVm {UniqueID}*.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med den privata slut punkten tar du bort resurs gruppen. Den privata slut punkten och alla relaterade resurser tas bort.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azures privata länk](private-link-overview.md)
