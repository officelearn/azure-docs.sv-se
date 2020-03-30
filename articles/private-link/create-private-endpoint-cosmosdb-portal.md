---
title: Ansluta till ett Azure Cosmos-konto med Azure Private Link
description: Lär dig hur du på ett säkert sätt får åtkomst till Azure Cosmos-kontot från en virtuell dator genom att skapa en privat slutpunkt.
author: malopMSFT
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: b7a50a2dabc9503ca5dbdd3388e29cfc69963885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252601"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Ansluta privat till ett Azure Cosmos-konto med Azure Private Link

Azure Private Endpoint är den grundläggande byggstenen för Privat länk i Azure. Det gör det möjligt för Azure-resurser, som virtuella datorer ( VMs), att kommunicera privat med Private Link-resurser.

I den här artikeln får du lära dig hur du skapar en virtuell dator i ett virtuellt Azure-nätverk och ett Azure Cosmos-konto med en privat slutpunkt med Azure-portalen. Sedan kan du komma åt Azure Cosmos-kontot på ett säkert sätt från den virtuella datorn.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen.](https://portal.azure.com)

## <a name="create-a-vm"></a>Skapa en virtuell dator

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet skapar du ett virtuellt nätverk och undernätet som är värd för den virtuella datorn som används för att komma åt din Private Link-resurs (ett Azure Cosmos-konto i det här exemplet).

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resursgruppnamn>**  | myResourceGroup|
| **\<>virtuellt nätverksnamn** | myVirtualNetwork         |
| **\<regionnamn>**          | USA, västra centrala     |
| **\<IPv4-adress-utrymme>**   | 10.1.0.0\16          |
| **\<>i>**          | mySubnet        |
| **\<>för>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

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
    | lösenord | Ange ett lösenord som du väljer. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
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

## <a name="create-an-azure-cosmos-account"></a>Skapa ett Azure Cosmos-konto

Skapa ett [Azure Cosmos SQL API-konto](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). För enkelhetens skull kan du skapa Azure Cosmos-kontot i samma region som de andra resurserna (det vill säga "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Skapa en privat slutpunkt för ditt Azure Cosmos-konto

Skapa en privat länk för ditt Azure Cosmos-konto enligt beskrivningen i avsnittet Skapa en privat länk med hjälp av [azure-portalavsnittet](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) i den länkade artikeln.

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Anslut till VM *myVm* från internet enligt följande:

1. I portalens sökfält anger du *myVm*.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna den nedladdade *.rdp*-filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användarnamn och lösenord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **Fler alternativ** > **Använd ett annat konto**för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Få tillgång till Azure Cosmos-kontot privat från den virtuella datorn

I det här avsnittet ansluter du privat till Azure Cosmos-kontot med hjälp av den privata slutpunkten. 

1. Om du vill inkludera IP-adressen och DNS-mappningen loggar `c:\Windows\System32\Drivers\etc\hosts` du in på *myvm*för virtuella datorer, öppnar filen och inkluderar DNS-informationen från föregående steg i följande format:

   [Privat IP-adress] [Slutpunkt för konto].documents.azure.com

   **Exempel:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. Installera [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)på fjärrskrivbordet i *myVM*.

1. Välj **Cosmos DB-konton (förhandsgranskning)** med högerklicka.

1. Välj **Anslut till Cosmos DB**.

1. Välj **API**.

1. Ange anslutningssträngen genom att klistra in den information som tidigare kopierats.

1. Välj **Nästa**.

1. Välj **Anslut**.

1. Bläddra i Azure Cosmos-databaser och behållare från *mycosmosaccount*.

1. (Alternativt) lägga till nya objekt *i mycosmosaccount*.

1. Stäng fjärrskrivbordsanslutningen till *myVM*.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med private endpoint, Azure Cosmos-konto och den virtuella datorn tar du bort resursgruppen och alla resurser som den innehåller: 

1. Skriv *in myResourceGroup* i **sökrutan** högst upp på portalen och välj *myResourceGroup* från sökresultaten.

1. Välj **Ta bort resursgrupp**.

1. Ange *myResourceGroup* för **TYP RESURSGRUPPNAMN** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du en virtuell dator i ett virtuellt nätverk, ett Azure Cosmos-konto och en privat slutpunkt. Du är ansluten till den virtuella datorn från internet och kommuniceras säkert till Azure Cosmos-kontot med private link.

* Mer information om privat slutpunkt finns i [Vad är Azure Private Endpoint?](private-endpoint-overview.md).

* Mer information om begränsning av privat slutpunkt när du använder med Azure Cosmos DB finns i [Azure Private Link med Azure Cosmos DB-artikel.](../cosmos-db/how-to-configure-private-endpoints.md)
