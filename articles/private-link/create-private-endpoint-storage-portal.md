---
title: Ansluta privat till ett lagringskonto med hjälp av privat Azure-slutpunkt
description: Lär dig hur du ansluter privat till ett lagringskonto i Azure med hjälp av en privat slutpunkt.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 111e6e2f80c3460f363c496b7b32befdca16250d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115115"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Ansluta privat till ett lagringskonto med hjälp av privat Azure-slutpunkt
Azure Private Endpoint är den grundläggande byggstenen för Privat länk i Azure. Det gör det möjligt för Azure-resurser, som virtuella datorer ( VMs), att kommunicera privat med Private Link-resurser.

I den här snabbstarten får du lära dig hur du skapar en virtuell dator i ett virtuellt Azure-nätverk, ett lagringskonto med en privat slutpunkt med Azure-portalen. Sedan kan du komma åt lagringskontot från den virtuella datorn på ett säkert sätt.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vm"></a>Skapa en virtuell dator
I det här avsnittet skapar du virtuella nätverk och undernätet som värd för den virtuella datorn som används för att komma åt din private link-resurs (ett lagringskonto i det här exemplet).

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet skapar du virtuella nätverk och undernätet som värd för den virtuella datorn som används för att komma åt din private link-resurs.

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resursgruppnamn>**  | myResourceGroup |
| **\<>virtuellt nätverksnamn** | myVirtualNetwork          |
| **\<regionnamn>**          | USA, västra centrala      |
| **\<IPv4-adress-utrymme>**   | 10.1.0.0\16          |
| **\<>i>**          | mySubnet        |
| **\<>för>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. På den övre vänstra sidan av skärmen i Azure-portalen väljer du Skapa en > **virtuell**dator**för resursberäknning** **Create a resource** > .

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade detta i föregående avsnitt.  |
    | **INSTANSINFORMATION** |  |
    | Namn på virtuell dator | Ange *myVm*. |
    | Region | Välj **WestCentralUS**. |
    | Alternativ för tillgänglighet | Lämna kvar standardinställningen **Ingen infrastrukturredundans krävs**. |
    | Bild | Välj **Windows Server 2019 Datacenter**. |
    | Storlek | Lämna kvar standardinställningen **Standard DS1 v2**. |
    | **ADMINISTRATÖRSKONTO** |  |
    | Användarnamn | Ange ett användarnamn som du väljer. |
    | lösenord | Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna kvar standardinställningen **Ingen**. |
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna kvar standardinställningen **Nej**. |
    |||

1. Välj **Nästa: Diskar**.

1. I **Skapa en virtuell dator - Diskar**, lämnar du standardvärdena och väljer **Nästa: Nätverk .**

1. I **Skapa en virtuell dator – Nätverk** väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Lämna **standardvärdet MyVirtualNetwork**.  |
    | Adressutrymme | Lämna **standardvärdet 10.1.0.0/24**.|
    | Undernät | Lämna standard **mySubnet (10.1.0.0/24)**.|
    | Offentlig IP-adress | Lämna standardinställningen **(ny) myVm-ip**. |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **HTTP** och **RDP**.|
    ||

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure validerar din konfiguration.

1. När meddelandet **Validering har skickats** väljer du **Skapa**.

## <a name="create-your-private-endpoint"></a>Skapa din privata slutpunkt
I det här avsnittet skapar du ett privat lagringskonto med hjälp av en privat slutpunkt till den. 

1. På den övre vänstra sidan av skärmen i Azure-portalen väljer du **Skapa ett** > **resurslagringslagringskonto****Storage** > .

1. Ange eller välj den här informationen i **Skapa lagringskonto - Grunderna:**

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade detta i föregående avsnitt.|
    | **INSTANSINFORMATION** |  |
    | Lagringskontots namn  | Ange *mystorageaccount*. Om det här namnet tas skapar du ett unikt namn. |
    | Region | Välj **WestCentralUS**. |
    | Prestanda| Lämna kvar standardinställningen **Standard**. |
    | Typ av konto | Lämna **standardlagring (allmänt ändamål v2)**. |
    | Replikering | Välj **Geoundant lagring (Läs-åtkomst)**. |
    |||
  
3. Välj **Nästa: Nätverk .**
4. Välj **Privat slutpunkt**i **Skapa ett lagringskonto - Nätverk**, anslutningsmetod .
5. I **Skapa ett lagringskonto - Nätverk**väljer du Lägg till privat **slutpunkt**. 
6. Ange eller välj den här informationen i **Skapa privat slutpunkt:**

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade detta i föregående avsnitt.|
    |Location|Välj **WestCentralUS**.|
    |Namn|Ange *myPrivateEndpoint*.  |
    |Underresurs för lagring|Lämna **standardblobben**. |
    | **Nätverk** |  |
    | Virtuellt nätverk  | Välj *MyVirtualNetwork* från resursgruppen *myResourceGroup*. |
    | Undernät | Välj *mySubnet*. |
    | **PRIVAT DNS-INTEGRERING**|  |
    | Integrera med privat DNS-zon  | Lämna standard **Ja**. |
    | Privat DNS-zon  | Lämna standard **privatelink.blob.core.windows.net (Ny)**. |
    |||
7. Välj **OK**. 
8. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure validerar din konfiguration. 
9. När meddelandet **Validering har skickats** väljer du **Skapa**. 
10. Bläddra till den lagringskontoresurs som du just skapade.
11. Välj **Snabbtangenter** på menyn för vänster innehåll.
12. Välj **Kopiera** på anslutningssträngen för nyckel1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Anslut till VM *myVm* från internet enligt följande:

1. I portalens sökfält anger du *myVm*.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna *filen downloaded.rdp.*

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användarnamn och lösenord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **Fler alternativ** > **Använd ett annat konto**för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  

## <a name="access-storage-account-privately-from-the-vm"></a>Komma åt lagringskontot privat från den virtuella datorn

I det här avsnittet ansluter du privat till lagringskontot med hjälp av den privata slutpunkten.

1. Öppna PowerShell i fjärrskrivbordet *för myVM.*
2. Ange `nslookup mystorageaccount.blob.core.windows.net` Du får ett meddelande som liknar detta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Installera [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Välj **Lagringskonton** med högerklicka.
5. Välj **Anslut till en azure storage**.
6. Välj **Använd en anslutningssträng**.
7. Välj **Nästa**.
8. Ange anslutningssträngen genom att klistra in den information som tidigare kopierats.
9. Välj **Nästa**.
10. Välj **Anslut**.
11. Bläddra bland Blob-behållarna från mittstorakontot 
12. (Valfritt) Skapa mappar och/eller ladda upp filer till *mystorageaccount*. 
13. Stäng fjärrskrivbordsanslutningen till *myVM*. 

Ytterligare alternativ för att komma åt lagringskontot:
- Microsoft Azure Storage Explorer är en fristående gratis app från Microsoft som gör att du kan arbeta visuellt med Azure-lagringsdata på Windows, macOS och Linux. Du kan installera programmet för att bläddra privat lagringskontoinnehållet. 
 
- AzCopy-verktyget är ett annat alternativ för högpresterande skriptbar dataöverföring för Azure-lagring. Använd AzCopy för att överföra data till och från Blob, File och Table Storage. 


## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slutpunkten, lagringskontot och den virtuella datorn tar du bort resursgruppen och alla resurser som den innehåller: 
1. Skriv *in myResourceGroup* i **sökrutan** högst upp på portalen och välj *myResourceGroup* från sökresultaten. 
2. Välj **Ta bort resursgrupp**. 
3. Ange *myResourceGroup* för **TYP RESURSGRUPPNAMN** och välj **Ta bort**. 

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten skapade du en virtuell dator på ett virtuellt nätverks- och lagringskonto och en privat slutpunkt. Du är ansluten till en virtuell dator från internet och kommuniceras säkert till lagringskontot med Private Link. Mer information om privat slutpunkt finns i [Vad är Azure Private Endpoint?](private-endpoint-overview.md).
