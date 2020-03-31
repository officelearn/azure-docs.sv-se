---
title: Snabbstart – hantera privata slutpunkter i Azure
description: Lär dig hur du skapar en privat slutpunkt med hjälp av Azure-portalen i den här snabbstarten
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 485eb14938fc7e490ea2d68c9090cdfdbf01cc8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78252561"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Snabbstart: Skapa en privat slutpunkt med Azure-portal

En privat slutpunkt är den grundläggande byggstenen för privat länk i Azure. Det gör det möjligt för Azure-resurser, till exempel virtuella datorer (VMs), att kommunicera privat med privata länkresurser. I den här snabbstarten får du lära dig hur du skapar en virtuell dator i ett Virtuellt Azure-nätverk, en SQL-databasserver med en privat Azure-slutpunkt med Azure-portalen. Sedan kan du säkert komma åt SQL Database Server från den virtuella datorn.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vm"></a>Skapa en virtuell dator
I det här avsnittet skapar du virtuella nätverk och undernätet som värd för den virtuella datorn som används för att komma åt din Private Link-resurs (en SQL-server i Azure i det här exemplet).

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet skapar du ett virtuellt nätverk och undernätet som värd för den virtuella datorn som används för att komma åt din private link-resurs.

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resursgruppnamn>**  | myResourceGroup |
| **\<>virtuellt nätverksnamn** | myVirtualNetwork          |
| **\<regionnamn>**          | USA, västra centrala    |
| **\<IPv4-adress-utrymme>**   | 10.1.0.0\16          |
| **\<>i>**          | mySubnet        |
| **\<>för>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machine"></a>Skapa virtuell dator

1. På den övre vänstra sidan av skärmen i Azure-portalen väljer du Skapa en virtuell dator > **för resursberäknning** > **Virtual Machine**. **Create a resource**

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
    |||


1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure validerar din konfiguration.

1. När meddelandet **Validering har skickats** väljer du **Skapa**.

## <a name="create-a-sql-database-server"></a>Skapa en SQL-databasserver
I det här avsnittet skapar du en SQL-databasserver i Azure. 

1. På den övre vänstra sidan av skärmen i Azure-portalen väljer du Skapa en**SQL-databas****för** >  **databaser** > .

1. Ange eller välj den här informationen i **Skapa SQL-databas - Basics:**

    | Inställning | Värde |
    | ------- | ----- |
    | **Databasinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade detta i föregående avsnitt.|
    | **INSTANSINFORMATION** |  |
    | Databasnamn  | Ange *mydatabas .* Om det här namnet tas skapar du ett unikt namn. |
    |||
5. Välj Skapa **nytt**i **Server**. 
6. Ange eller välj den här informationen på **Ny server:**

    | Inställning | Värde |
    | ------- | ----- |
    |servernamn  | Ange *myserver*. Om det här namnet tas skapar du ett unikt namn.|
    | inloggning för serveradministratör| Ange ett administratörsnamn som du väljer. |
    | lösenord | Ange ett valfritt lösenord. Lösenordet måste vara minst 8 tecken långt och uppfylla de definierade kraven. |
    | Location | Välj en Azure-region där du vill att SQL Server ska finnas. |
    
7. Välj **OK**. 
8. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure validerar din konfiguration. 
9. När meddelandet Validering har skickats väljer du **Skapa**. 
10. När meddelandet Validering har skickats väljer du Skapa. 

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

I det här avsnittet ska du skapa en SQL-server och lägga till en privat slutpunkt i den. 

1. På den övre vänstra sidan av skärmen i Azure-portalen väljer du **Skapa en resurs** > **Nätverk privat** > **länkcenter (förhandsversion)**.
2. I **Private Link Center -** Översikt **Build a private connection to a service**väljer du **Start**.
1. Ange eller välj den här informationen i **Skapa en privat slutpunkt (förhandsgranskning) – Grunderna:**

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade detta i föregående avsnitt.|
    | **INSTANSINFORMATION** |  |
    | Namn | Ange *myPrivateEndpoint*. Om det här namnet tas skapar du ett unikt namn. |
    |Region|Välj **WestCentralUS**.|
    |||
5. Välj **Nästa: Resurs**.
6. Ange eller välj den här informationen i **Skapa en privat slutpunkt - Resurs:**

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj ansluta till en Azure-resurs i min katalog.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft.Sql/servers**. |
    | Resurs |Välj *myServer*|
    |Målunderresurs |Välj *sqlServer*|
    |||
7. Välj **Nästa: Konfiguration**.
8. Ange eller välj den här informationen i **Skapa en privat slutpunkt (förhandsgranskning) – konfiguration:**

    | Inställning | Värde |
    | ------- | ----- |
    |**Nätverk**| |
    | Virtuellt nätverk| Välj *MyVirtualNetwork*. |
    | Undernät | Välj *mySubnet*. |
    |**PRIVAT DNS-INTEGRERING**||
    |Integrera med privat DNS-zon |Välj **Ja**. |
    |Privat DNS-zon |Välj *(Ny)privatelink.database.windows.net* |
    |||

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure validerar din konfiguration. 
2. När meddelandet **Validering har skickats** väljer du **Skapa**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Ansluta till en virtuell dator med fjärrskrivbord (RDP)


När du har skapat **myVm**ansluter du till den från internet enligt följande: 

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

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Komma åt SQL-databasservern privat från den virtuella datorn

1. Öppna PowerShell i fjärrskrivbordet *för myVM.*

2. Ange `nslookup myserver.database.windows.net`. 

    Du får ett meddelande som liknar detta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Installera [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. Ange eller välj den här informationen i **Anslut till server:**

    | Inställning | Värde |
    | ------- | ----- |
    | Servertyp| Välj **Databasmotor**.|
    | servernamn| Välj *myserver.database.windows.net* |
    | Användarnamn | Ange användarnamn username@servername som anges när SQL-servern skapas. |
    |lösenord |Ange ett lösenord som anges när SQL-servern skapades. |
    |Kom ihåg lösenord|Välj **Ja**.|
    |||
1. Välj **Anslut**.
2. Bläddra i databaser från vänstermeny.
3. (Valfritt) Skapa eller fråga information från min databas.
4. Stäng fjärrskrivbordsanslutningen till *myVm*. 

## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slutpunkten, SQL-servern och den virtuella datorn tar du bort resursgruppen och alla resurser som den innehåller: 
1. Skriv *in myResourceGroup* i **sökrutan** högst upp på portalen och välj *myResourceGroup* från sökresultaten. 
2. Välj **Ta bort resursgrupp**. 
3. Ange myResourceGroup för **TYP RESURSGRUPPNAMN** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en virtuell dator i ett virtuellt nätverk, en SQL-databasserver och en privat slutpunkt för privat åtkomst. Du är ansluten till en virtuell dator från internet och kommuniceras säkert till SQL-databasservern med private link. Mer information om privata slutpunkter finns i [Vad är Azure-privat slutpunkt?](private-endpoint-overview.md).
