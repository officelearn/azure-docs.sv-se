---
title: Ansluta privat till ett lagringskonto med hjälp av privat Azure-slutpunkt
description: Lär dig hur du ansluter privat till ett lagrings konto i Azure med en privat slut punkt.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 1ccbb685ceb406fd7a52edf793b53d9e1c32630b
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737333"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Ansluta privat till ett lagringskonto med hjälp av privat Azure-slutpunkt
Den privata Azure-slutpunkten är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, t. ex. virtuella datorer, att kommunicera privat med privata länk resurser.

I den här snabb starten får du lära dig hur du skapar en virtuell dator i ett virtuellt Azure-nätverk, ett lagrings konto med en privat slut punkt med hjälp av Azure Portal. Sedan kan du på ett säkert sätt komma åt lagrings kontot från den virtuella datorn.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vm"></a>Skapa en virtuell dator
I det här avsnittet ska du skapa ett virtuellt nätverk och under nätet som är värd för den virtuella datorn som används för att komma åt din privata länk resurs (ett lagrings konto i det här exemplet).

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet ska du skapa ett virtuellt nätverk och under nätet som är värd för den virtuella datorn som används för åtkomst till din privata länk resurs.

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | USA, västra centrala      |
| **\<IPv4-address-space>**   | 10.1.0.0 \ 16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **beräknings**  >  **virtuell dator**.

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställningen | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.  |
    | **INSTANSINFORMATION** |  |
    | Namn på virtuell dator | Ange *myVm*. |
    | Region | Välj **WestCentralUS**. |
    | Alternativ för tillgänglighet | Lämna kvar standardinställningen **Ingen infrastrukturredundans krävs**. |
    | Bild | Välj **Windows Server 2019 Data Center**. |
    | Storlek | Lämna kvar standardinställningen **Standard DS1 v2**. |
    | **ADMINISTRATÖRSKONTO** |  |
    | Användarnamn | Ange ett användar namn som du väljer. |
    | lösenordsinställning | Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna kvar standardinställningen **Ingen**. |
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna kvar standardinställningen **Nej**. |
    |||

1. Välj **Nästa: diskar**.

1. Lämna standardvärdena i **skapa en virtuell dator – diskar**och välj **Nästa: nätverk**.

1. I **Skapa en virtuell dator – Nätverk** väljer du följande information:

    | Inställningen | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Lämna standard **MyVirtualNetwork**.  |
    | Adressutrymme | Lämna standard **10.1.0.0/24**.|
    | Undernät | Lämna standard **under nätet (10.1.0.0/24)**.|
    | Offentlig IP-adress | Lämna standardinställningen **(ny) myVm-ip**. |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **HTTP** och **RDP**.|
    ||

1. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen.

1. När du ser meddelandet **valideringen har skickats** väljer du **skapa**.

## <a name="create-your-private-endpoint"></a>Skapa din privata slut punkt
I det här avsnittet ska du skapa ett privat lagrings konto med en privat slut punkt till det. 

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa ett resurs**  >  **lagrings**  >  **lagrings konto**.

1. I **skapa lagrings konto – grunderna**anger eller väljer du den här informationen:

    | Inställningen | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | **INSTANSINFORMATION** |  |
    | Lagringskontots namn  | Ange *mystorageaccount*. Om det här namnet tas skapar du ett unikt namn. |
    | Region | Välj **WestCentralUS**. |
    | Prestanda| Lämna kvar standardinställningen **Standard**. |
    | Typ av konto | Lämna standard **lagrings utrymmet (generell användning v2)**. |
    | Replikering | Välj **Geo-redundant lagring med Läs behörighet (RA-GRS)**. |
    |||
  
3. Välj **Nästa: nätverk**.
4. I **skapa ett lagrings konto – nätverk**, anslutnings metod, väljer du **privat slut punkt**.
5. I **skapa ett lagrings konto – nätverk**väljer du **Lägg till privat slut punkt**. 
6. I **skapa privat slut punkt**anger eller väljer du den här informationen:

    | Inställningen | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    |Location|Välj **WestCentralUS**.|
    |Name|Ange *myPrivateEndpoint*.  |
    |Lagrings under resurs|Lämna standard- **bloben**. |
    | **NÄTVERK** |  |
    | Virtuellt nätverk  | Välj *MyVirtualNetwork* från resurs gruppen *myResourceGroup*. |
    | Undernät | Välj *undernät*. |
    | **PRIVAT DNS-INTEGRERING**|  |
    | Integrera med privat DNS-zon  | Låt standardinställningen vara **Ja**. |
    | Privat DNS-zon  | Lämna standard **-privatelink.blob.Core.Windows.net (ny)**. |
    |||
7. Välj **OK**. 
8. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen. 
9. När du ser meddelandet **valideringen har skickats** väljer du **skapa**. 
10. Bläddra till den lagrings konto resurs som du nyss skapade.
11. Välj **åtkomst nycklar** på menyn till vänster innehåll.
12. Välj **Kopiera** i anslutnings strängen för KEY1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Anslut till VM- *myVm* från Internet på följande sätt:

1. Skriv *myVm*i portalens Sök fält.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna den *nedladdade RDP* -filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **fler alternativ**  >  **Använd ett annat konto**för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  

## <a name="access-storage-account-privately-from-the-vm"></a>Få åtkomst till lagrings kontot privat från den virtuella datorn

I det här avsnittet ska du ansluta privat till lagrings kontot med hjälp av den privata slut punkten.

1. Öppna PowerShell i fjärr skrivbordet för *myVM*.
2. Ange `nslookup mystorageaccount.blob.core.windows.net` ett meddelande som liknar detta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Installera [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Välj **lagrings konton** med högerklickning.
5. Välj **Anslut till en Azure-lagring**.
6. Välj **Använd en anslutnings sträng**.
7. Välj **Nästa**.
8. Ange anslutnings strängen genom att klistra in informationen som tidigare har kopierats.
9. Välj **Nästa**.
10. Välj **Anslut**.
11. Bläddra bland BLOB-behållarna från mystorageaccount 
12. Du kan också Skapa mappar och/eller ladda upp filer till *mystorageaccount*. 
13. Stäng fjärr skrivbords anslutningen till *myVM*. 

Ytterligare alternativ för att komma åt lagrings kontot:
- Microsoft Azure Storage Explorer är en fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data på Windows, macOS och Linux. Du kan installera programmet om du vill söka i privat lagrings konto innehåll. 
 
- AzCopy-verktyget är ett annat alternativ för högpresterande skript data överföring med hög prestanda för Azure Storage. Använd AzCopy för att överföra data till och från Blob, File och Table Storage. 


## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slut punkten, lagrings kontot och den virtuella datorn tar du bort resurs gruppen och alla resurser den innehåller: 
1. Skriv *myResourceGroup* i **sökrutan längst** upp i portalen och välj *myResourceGroup* från Sök resultaten. 
2. Välj **Ta bort resursgrupp**. 
3. Ange *myResourceGroup* för **Skriv resurs gruppens namn** och välj **ta bort**. 

## <a name="next-steps"></a>Nästa steg
I den här snabb starten skapade du en virtuell dator i ett virtuellt nätverk och ett lagrings konto och en privat slut punkt. Du är ansluten till en virtuell dator från Internet och kommunicerat på ett säkert sätt till lagrings kontot med hjälp av en privat länk. Mer information om privata slut punkter finns i [Vad är Azures privata slut punkt?](private-endpoint-overview.md).
