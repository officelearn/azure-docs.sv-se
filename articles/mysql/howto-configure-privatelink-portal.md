---
title: Privat länk – Azure Portal-Azure Database for MySQL
description: Lär dig hur du konfigurerar en privat länk för Azure Database for MySQL från Azure Portal
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 7dbce3f2645a1ae4e01dc52d516962297ea07df6
ms.sourcegitcommit: 20e246e86e25d63bcd521a4b4d5864fbc7bad1b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84488859"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-portal"></a>Skapa och hantera en privat länk för Azure Database for MySQL med hjälp av portalen

En privat slut punkt är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, t. ex. Virtual Machines (VM), att kommunicera privat med privata länk resurser. I den här artikeln får du lära dig hur du använder Azure Portal för att skapa en virtuell dator i en Azure-Virtual Network och en Azure Database for MySQL-server med en privat Azure-slutpunkt.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!NOTE]
> Den här funktionen är tillgänglig i alla Azure-regioner där Azure Database for MySQL stöder Generell användning och minnesoptimerade pris nivåer.

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Skapa en virtuell Azure-dator

I det här avsnittet ska du skapa ett virtuellt nätverk och under nätet som är värd för den virtuella datorn som används för att komma åt din privata länk resurs (en MySQL-server i Azure).

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket
I det här avsnittet ska du skapa en Virtual Network och under nätet som är värd för den virtuella datorn som används för åtkomst till din privata länk resurs.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **virtuellt nätverk**.
2. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställningen | Värde |
    | ------- | ----- |
    | Namn | Ange *MyVirtualNetwork*. |
    | Adressutrymme | Ange *10.1.0.0/16*. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Location | Välj **Europa, västra**.|
    | Undernät – Namn | Ange *undernät*. |
    | Undernät – adressintervall | Ange *10.1.0.0/24*. |
    |||
3. Lämna resten som standard och välj **skapa**.

### <a name="create-virtual-machine"></a>Skapa virtuell dator

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **beräknings**  >  **virtuell dator**.

2. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställningen | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.  |
    | **INSTANSINFORMATION** |  |
    | Namn på virtuell dator | Ange *myVm*. |
    | Region | Välj **Europa, västra**. |
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
    |||


1. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen.

1. När du ser meddelandet **valideringen har skickats** väljer du **skapa**.

## <a name="create-an-azure-database-for-mysql"></a>Skapa en Azure Database för MySQL-server

I det här avsnittet ska du skapa en Azure Database for MySQL-server i Azure. 

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **databas**  >  **Azure Database for MySQL**.

1. I **Azure Database for MySQL** anger du följande information:

    | Inställningen | Värde |
    | ------- | ----- |
    | **Projekt information** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | **Serverinformation** |  |
    |servernamn  | Ange *Server*. Om det här namnet tas skapar du ett unikt namn.|
    | Administratörens användar namn| Ange ett administratörs namn som du väljer. |
    | lösenordsinställning | Ange ett valfritt lösenord. Lösen ordet måste vara minst 8 tecken långt och uppfylla de definierade kraven. |
    | Location | Välj en Azure-region där du vill att MySQL-servern ska finnas. |
    |Version  | Välj den databas version av MySQL-servern som krävs.|
    | Compute + Storage| Välj den pris nivå som krävs för servern baserat på arbets belastningen. |
    |||
 
7. Välj **OK**. 
8. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen. 
9. När du ser meddelandet valideringen har skickats väljer du **skapa**. 
10. När du ser meddelandet valideringen har skickats väljer du skapa. 

> [!NOTE]
> I vissa fall finns Azure Database for MySQL och VNet-under nätet i olika prenumerationer. I dessa fall måste du se till att följande konfigurationer:
> - Se till att båda prenumerations resurs leverantören för **Microsoft. DBforMySQL** är registrerad. Mer information hittar du i [Resource Manager-Registration][resource-manager-portal]

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

I det här avsnittet ska du skapa en MySQL-server och lägga till en privat slut punkt till den. 

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **nätverk**  >  **privat länk**.

2. I **privat länk Center – översikt**, på alternativet för att **skapa en privat anslutning till en tjänst**, väljer du **Start**.

    ![Översikt över privat länk](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. I **skapa en privat slut punkt – grunderna**anger eller väljer du den här informationen:

    | Inställningen | Värde |
    | ------- | ----- |
    | **Projekt information** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | **Instans information** |  |
    | Name | Ange *myPrivateEndpoint*. Om det här namnet tas skapar du ett unikt namn. |
    |Region|Välj **Europa, västra**.|
    |||

5. Välj **Nästa: resurs**.
6. I **skapa en privat slut punkt – resurs**, anger eller väljer du den här informationen:

    | Inställningen | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj Anslut till en Azure-resurs i min katalog.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft. DBforMySQL/servers**. |
    | Resurs |Välj *Server*|
    |Mål under resurs |Välj *SQLServer*|
    |||
7. Välj **Nästa: konfiguration**.
8. I **skapa en privat slut punkt – konfiguration**anger eller väljer du den här informationen:

    | Inställningen | Värde |
    | ------- | ----- |
    |**NÄTVERK**| |
    | Virtuellt nätverk| Välj *MyVirtualNetwork*. |
    | Undernät | Välj *undernät*. |
    |**PRIVAT DNS-INTEGRERING**||
    |Integrera med privat DNS-zon |Välj **Ja**. |
    |Privat DNS zon |Välj *(ny) privatelink. mysql. Database. Azure. com* |
    |||

    > [!Note] 
    > Använd den fördefinierade privata DNS-zonen för din tjänst eller skapa en ny. Mer information finns i [konfiguration av DNS-zoner för Azure-tjänster](../private-link/private-endpoint-dns.md) .

1. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen. 
2. När du ser meddelandet **valideringen har skickats** väljer du **skapa**. 

    ![Privat länk har skapats](media/concepts-data-access-and-security-private-link/show-mysql-private-link.png)

    > [!NOTE] 
    > FQDN i DNS-inställningen för kunden matchar inte den privata IP-adressen som kon figurer ATS. Du måste konfigurera en DNS-zon för den konfigurerade FQDN: en som visas [här](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Ansluta till en virtuell dator med hjälp av fjärr skrivbord (RDP)


När du har skapat **myVm**ansluter du till den från Internet på följande sätt: 

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

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Få åtkomst till MySQL-servern privat från den virtuella datorn

1. Öppna PowerShell i fjärr skrivbordet för *myVM*.

2. Ange  `nslookup  myServer.privatelink.mysql.database.azure.com` . 

    Du får ett meddelande som liknar detta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myServer.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. Testa anslutningen till den privata länken för MySQL-servern med valfri tillgänglig klient. I exemplet nedan har jag använt [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) för att utföra åtgärden.

4. I **ny anslutning**anger eller väljer du den här informationen:

    | Inställningen | Värde |
    | ------- | ----- |
    | Servertyp| Välj **MySQL**.|
    | servernamn| Välj *myserver.privatelink.mysql.Database.Azure.com* |
    | Användarnamn | Ange användar namn som username@servername anges när MySQL-servern skapas. |
    |lösenordsinställning |Ange ett lösen ord som anges när MySQL-servern skapas. |
    |SSL|Välj **obligatoriskt**.|
    ||

5. Välj Anslut.

6. Bläddra bland databaser från menyn till vänster.

7. Du kan också Skapa eller fråga efter information från MySQL-servern.

8. Stäng fjärr skrivbords anslutningen till myVm.

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med den privata slut punkten, MySQL-servern och den virtuella datorn tar du bort resurs gruppen och alla resurser den innehåller:

1. Skriv *myResourceGroup*   i sökrutan längst upp i portalen och välj **Search**  *myResourceGroup*   från Sök resultaten.
2. Välj **Ta bort resursgrupp**.
3. Ange myResourceGroup för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här instruktionen har du skapat en virtuell dator i ett virtuellt nätverk, en Azure Database for MySQL och en privat slut punkt för privat åtkomst. Du har anslutit till en virtuell dator från Internet och kommunicerat på ett säkert sätt till MySQL-servern med hjälp av en privat länk. Mer information om privata slut punkter finns i [Vad är en privat Azure-slutpunkt](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md