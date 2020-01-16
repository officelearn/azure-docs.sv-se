---
title: Snabb start – hantera privata slut punkter i Azure
description: Lär dig hur du skapar en privat slut punkt med hjälp av Azure Portal i den här snabb starten
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 491ba986c6ca71be0bc5b13e2f9f0717ffec99a4
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028891"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Snabb start: skapa en privat slut punkt med hjälp av Azure Portal

En privat slut punkt är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, t. ex. Virtual Machines (VM), att kommunicera privat med privata länk resurser. I den här snabb starten får du lära dig hur du skapar en virtuell dator på en Azure-Virtual Network, en SQL Database-Server med en privat Azure-slutpunkt med Azure Portal. Sedan kan du på ett säkert sätt komma åt SQL Database-servern från den virtuella datorn.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vm"></a>Skapa en virtuell dator
I det här avsnittet skapar du ett virtuellt nätverk och under nätet som är värd för den virtuella datorn som används för åtkomst till din privata länk resurs (en SQL-Server i Azure i det här exemplet).

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket


I det här avsnittet ska du skapa en Virtual Network och under nätet som är värd för den virtuella datorn som används för åtkomst till din privata länk resurs.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.
1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *MyVirtualNetwork*. |
    | Adressutrymme | Ange *10.1.0.0/16*. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Location | Välj **WestCentralUS**.|
    | Undernät – Namn | Ange *undernät*. |
    | Undernät – adressintervall | Ange *10.1.0.0/24*. |
    |||
1. Lämna resten som standard och välj **skapa**.


### <a name="create-virtual-machine"></a>Skapa virtuell dator

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs** > **Compute** > **virtuell dator**.

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Värde |
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
    | lösenord | Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna kvar standardinställningen **Ingen**. |
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna kvar standardinställningen **Nej**. |
    |||

1. Välj **Nästa: diskar**.

1. Lämna standardvärdena i **skapa en virtuell dator – diskar**och välj **Nästa: nätverk**.

1. I **Skapa en virtuell dator – Nätverk** väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Lämna standard **MyVirtualNetwork**.  |
    | Adressutrymme | Lämna standard **10.1.0.0/24**.|
    | Undernät | Lämna standard **under nätet (10.1.0.0/24)** .|
    | Offentlig IP-adress | Lämna standardinställningen **(ny) myVm-ip**. |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **HTTP** och **RDP**.|
    |||


1. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen.

1. När du ser meddelandet **valideringen har skickats** väljer du **skapa**.

## <a name="create-a-sql-database-server"></a>Skapa en SQL Database-Server
I det här avsnittet ska du skapa en SQL Database-Server i Azure. 

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs** > **databaser** > **SQL Database**.

1. I **skapa SQL Database – grunderna**anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | **Databas information** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | **INSTANSINFORMATION** |  |
    | Databasnamn  | Ange min *databas*. Om det här namnet tas skapar du ett unikt namn. |
    |||
5. I **Server**väljer du **Skapa ny**. 
6. I **ny server**anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    |servernamn  | Ange *Server*. Om det här namnet tas skapar du ett unikt namn.|
    | inloggning för serveradministratör| Ange ett administratörs namn som du väljer. |
    | lösenord | Ange ett valfritt lösenord. Lösen ordet måste vara minst 8 tecken långt och uppfylla de definierade kraven. |
    | Location | Välj en Azure-region där du vill att din SQL Server ska finnas. |
    
7. Välj **OK**. 
8. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen. 
9. När du ser meddelandet valideringen har skickats väljer du **skapa**. 
10. När du ser meddelandet valideringen har skickats väljer du skapa. 

## <a name="create-a-private-endpoint"></a>Skapa en privat slut punkt

I det här avsnittet ska du skapa en SQL-Server och lägga till en privat slut punkt i den. 

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs** > **nätverk** > **Private Link Center (för hands version)** .
2. I **privat länk Center – översikt**, på alternativet för att **skapa en privat anslutning till en tjänst**, väljer du **Start**.
1. I **skapa en privat slut punkt (för hands version) – grundläggande**anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projekt information** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | **INSTANSINFORMATION** |  |
    | Namn | Ange *myPrivateEndpoint*. Om det här namnet tas skapar du ett unikt namn. |
    |Region|Välj **WestCentralUS**.|
    |||
5. Välj **Nästa: resurs**.
6. I **skapa en privat slut punkt – resurs**, anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj Anslut till en Azure-resurs i min katalog.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft. SQL/Servers**. |
    | Resurs |Välj *Server*|
    |Mål under resurs |Välj *sqlServer*|
    |||
7. Välj **Nästa: konfiguration**.
8. I **skapa en privat slut punkt (för hands version) – konfiguration**, anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    |**NÄTVERK**| |
    | Virtuellt nätverk| Välj *MyVirtualNetwork*. |
    | Undernät | Välj *undernät*. |
    |**PRIVAT DNS-INTEGRERING**||
    |Integrera med privat DNS-zon |Välj **Ja**. |
    |Privat DNS zon |Välj *(ny) privatelink. Database. Windows. net* |
    |||

1. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen. 
2. När du ser meddelandet **valideringen har skickats** väljer du **skapa**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Ansluta till en virtuell dator med hjälp av fjärr skrivbord (RDP)


När du har skapat **myVm**ansluter du till den från Internet på följande sätt: 

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

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Få åtkomst till SQL Database-servern privat från den virtuella datorn

1. Öppna PowerShell i fjärr skrivbordet för *myVM*.

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

4. I **Anslut till Server**anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Servertyp| Välj **Databasmotor**.|
    | servernamn| Välj *myserver.Database.Windows.net* |
    | Användarnamn | Ange användar namn som username@servername som anges när SQL-servern skapas. |
    |lösenord |Ange ett lösen ord som du angav när du skapade SQL-servern. |
    |Kom ihåg lösenordet|Välj **Ja**.|
    |||
1. Välj **Anslut**.
2. Bläddra bland databaser från menyn till vänster.
3. Du kan också Skapa eller fråga efter information från databasen.
4. Stäng fjärr skrivbords anslutningen till *myVm*. 

## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slut punkten, SQL Server och den virtuella datorn tar du bort resurs gruppen och alla resurser den innehåller: 
1. Skriv *myResourceGroup* i **sökrutan längst** upp i portalen och välj *myResourceGroup* från Sök resultaten. 
2. Välj **Ta bort resursgrupp**. 
3. Ange myResourceGroup för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en virtuell dator i ett virtuellt nätverk, en SQL Database-Server och en privat slut punkt för privat åtkomst. Du har anslutit till en virtuell dator från Internet och kommunicerat på ett säkert sätt till SQL Database-servern med hjälp av en privat länk. Mer information om privata slut punkter finns i [Vad är Azures privata slut punkt?](private-endpoint-overview.md).
