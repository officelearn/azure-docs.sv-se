---
title: Anslut privat till ett lagrings konto med hjälp av en privat Azure-slutpunkt
description: Lär dig hur du ansluter privat till ett lagrings konto i Azure med en privat slut punkt.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 93ca3c8c4b7a4da0387d1302b301cf677b689281
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71074976"
---
# <a name="connect-privately-to-a-storage-account-using-an-azure-private-endpoint"></a>Anslut privat till ett lagrings konto med hjälp av en privat Azure-slutpunkt
Den privata Azure-slutpunkten är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, t. ex. virtuella datorer, att kommunicera privat med privata länk resurser.

I den här snabb starten får du lära dig hur du skapar en virtuell dator i ett virtuellt Azure-nätverk, ett lagrings konto med en privat slut punkt med hjälp av Azure Portal. Sedan kan du på ett säkert sätt komma åt lagrings kontot från den virtuella datorn.


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vm"></a>Skapa en virtuell dator
I det här avsnittet ska du skapa ett virtuellt nätverk och under nätet som är värd för den virtuella datorn som används för att komma åt din privata länk resurs (ett lagrings konto i det här exemplet).

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet ska du skapa ett virtuellt nätverk och under nätet som är värd för den virtuella datorn som används för åtkomst till din privata länk resurs.

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


### <a name="create-virtual-machine"></a>Skapa en virtuell dator

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
    ||

1. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen.

1. När du ser meddelandet **valideringen har skickats** väljer du **skapa**.

## <a name="create-your-private-endpoint"></a>Skapa din privata slut punkt
I det här avsnittet ska du skapa ett privat lagrings konto med en privat slut punkt till det. 

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa ett resurs** > **lagrings** > **lagrings konto**.

1. I **skapa lagrings konto – grunderna**anger eller väljer du den här informationen:

    | Inställning | Value |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Subscription | Välj din prenumeration. |
    | Resource group | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | **INSTANSINFORMATION** |  |
    | Lagringskontonamn  | Ange *mystorageaccount*. Om det här namnet tas skapar du ett unikt namn. |
    | Region | Välj **WestCentralUS**. |
    | Prestanda| Lämna kvar standardinställningen **Standard**. |
    | Typ av konto | Lämna standard **lagrings utrymmet (generell användning v2)** . |
    | Replikering | Välj **Geo-redundant lagring med Läs behörighet (RA-GRS)** . |
    |||
  
3. Välj **nästa: Nätverk**.
4. I **skapa ett lagrings konto – nätverk**, anslutnings metod, väljer du **privat slut punkt**.
5. I **skapa ett lagrings konto – nätverk**väljer du **Lägg till privat slut punkt**. 
6. I **skapa privat slut punkt**anger eller väljer du den här informationen:

    | Inställning | Value |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Subscription | Välj din prenumeration. |
    | Resource group | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    |Location|Välj **WestCentralUS**.|
    |Name|Ange *myPrivateEndpoint*.  |
    |Lagrings under resurs|Lämna standard- **bloben**. |
    | **NÄTVERK** |  |
    | Virtuellt nätverk  | Välj *MyVirtualNetwork* från resurs gruppen *myResourceGroup*. |
    | Subnet | Välj *undernät*. |
    | **PRIVAT DNS-INTEGRERING**|  |
    | Integrera med privat DNS-zon  | Låt standardinställningen vara **Ja**. |
    | Privat DNS zon  | Lämna standard  ** -privatelink.blob.Core.Windows.net (ny)** . |
    |||
7. Välj **OK**. 
8. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen. 
9. När du ser meddelandet **valideringen har skickats** väljer du **skapa**. 
10. Bläddra till den lagrings konto resurs som du nyss skapade.
11. Välj **åtkomst nycklar** på menyn till vänster innehåll.
12. Välj **Kopiera** i anslutnings strängen för KEY1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Anslut till VM- *myVm* från Internet på följande sätt:

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

## <a name="access-storage-account-privately-from-the-vm"></a>Få åtkomst till lagrings kontot privat från den virtuella datorn

I det här avsnittet ska du ansluta privat till lagrings kontot med hjälp av den privata slut punkten.

> [!IMPORTANT]
> DNS-konfigurationen för Storage kräver en manuell ändring på värd filen för att inkludera det fullständiga domän namnet för det aktuella kontot. ändra följande fil med administratörs behörighet för Windows: c:\Windows\System32\Drivers\etc\hosts eller Linux/etc/hosts Ta med DNS-information för kontot från föregående steg i följande format [privat IP-adress] myaccount.blob.core.windows.net

1. Öppna PowerShell i fjärr skrivbordet för *myVM*.
2. Ange `nslookup mystorageaccount.blob.core.windows.net` ett meddelande som liknar detta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
3. Install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Select **Next**.
8. Enter the connection string by pasting the information previously copied.
9. Select **Next**.
10. Select **Connect**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Use AzCopy to transfer data to and from Blob, File, and Table storage. 


## Clean up resources 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## Next steps
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
