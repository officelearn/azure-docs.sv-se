---
title: Privat länk - Azure-portal - Azure Database för PostgreSQL - Enkel server
description: Lär dig hur du konfigurerar privat länk för Azure Database för PostgreSQL- Single server från Azure Portal
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 72dcf95c8ae8d8da34532fa96e3bf0371f5112fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370924"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-portal"></a>Skapa och hantera privat länk för Azure Database för PostgreSQL - Enkel server med Portal

En privat slutpunkt är den grundläggande byggstenen för privat länk i Azure. Det gör det möjligt för Azure-resurser, till exempel virtuella datorer (VMs), att kommunicera privat med privata länkresurser.  I den här artikeln får du lära dig hur du använder Azure-portalen för att skapa en virtuell dator i ett Virtuellt Azure-nätverk och en Azure-databas för PostgreSQL Single-server med en privat Azure-slutpunkt.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

> [!NOTE]
> Den här funktionen är tillgänglig i alla Azure-regioner där Azure Database for PostgreSQL Single server stöder prisnivåer för allmänt ändamål och minne.

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Skapa en virtuell Azure-dator

I det här avsnittet skapar du virtuellt nätverk och undernätet som värd för den virtuella datorn som används för att komma åt din Private Link-resurs (en PostgreSQL-server i Azure).

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket
I det här avsnittet skapar du ett virtuellt nätverk och undernätet som värd för den virtuella datorn som används för att komma åt din private link-resurs.

1. Välj **Skapa ett** > **virtuellt** > **nätverk**längst upp till vänster på skärmen .
2. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *MyVirtualNetwork*. |
    | Adressutrymme | Ange *10.1.0.0/16*. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Location | Välj **Europa, västra**.|
    | Undernät – Namn | Ange *mySubnet*. |
    | Undernät – adressintervall | Ange *10.1.0.0/24*. |
    |||
3. Lämna resten som standard och välj **Skapa**.

### <a name="create-virtual-machine"></a>Skapa virtuell dator

1. På den övre vänstra sidan av skärmen i Azure-portalen väljer du Skapa en virtuell dator > **för resursberäknning** > **Virtual Machine**. **Create a resource**

2. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade detta i föregående avsnitt.  |
    | **INSTANSINFORMATION** |  |
    | Namn på virtuell dator | Ange *myVm*. |
    | Region | Välj **Europa, västra**. |
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

## <a name="create-an-azure-database-for-postgresql-single-server"></a>Skapa en Azure-databas för PostgreSQL-server

I det här avsnittet skapar du en Azure-databas för PostgreSQL-server i Azure. 

1. På den övre vänstra sidan av skärmen i Azure-portalen väljer du **Skapa en azure-databas** > för**resursdatabaser** > **för PostgreSQL**.

1. I **Azure Database for PostgreSQL-distributionsalternativ väljer**du En **server** och anger följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade detta i föregående avsnitt.|
    | **Serverinformation** |  |
    |servernamn  | Ange *myserver*. Om det här namnet tas skapar du ett unikt namn.|
    | Administratörsanvändarnamn| Ange ett administratörsnamn som du väljer. |
    | lösenord | Ange ett valfritt lösenord. Lösenordet måste vara minst 8 tecken långt och uppfylla de definierade kraven. |
    | Location | Välj en Azure-region där du vill att din PostgreSQL-server ska finnas. |
    |Version  | Välj databasversionen av PostgreSQL-servern som krävs.|
    | Beräkna + lagring| Välj den prisnivå som behövs för servern baserat på arbetsbelastningen. |
    |||
 
7. Välj **OK**. 
8. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure validerar din konfiguration. 
9. När meddelandet Validering har skickats väljer du **Skapa**. 
10. När meddelandet Validering har skickats väljer du Skapa. 

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

I det här avsnittet skapar du en PostgreSQL-server och lägger till en privat slutpunkt i den. 

1. På den övre vänstra sidan av skärmen i Azure-portalen väljer du **Skapa en privat** > **resursnätverkslänk****Networking** > .
2. I **Private Link Center -** Översikt **Build a private connection to a service**väljer du **Start**.

    ![Översikt över privat länk](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. Ange eller välj den här informationen i **Skapa en privat slutpunkt - Grunderna:**

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade detta i föregående avsnitt.|
    | **Information om instans** |  |
    | Namn | Ange *myPrivateEndpoint*. Om det här namnet tas skapar du ett unikt namn. |
    |Region|Välj **Europa, västra**.|
    |||
5. Välj **Nästa: Resurs**.
6. Ange eller välj den här informationen i **Skapa en privat slutpunkt - Resurs:**

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj ansluta till en Azure-resurs i min katalog.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft.DBforPostgreSQL/servers**. |
    | Resurs |Välj *myServer*|
    |Målunderresurs |Välj *postgresqlServer*|
    |||
7. Välj **Nästa: Konfiguration**.
8. Ange eller välj den här informationen i **Skapa en privat slutpunkt - Konfiguration:**

    | Inställning | Värde |
    | ------- | ----- |
    |**Nätverk**| |
    | Virtuellt nätverk| Välj *MyVirtualNetwork*. |
    | Undernät | Välj *mySubnet*. |
    |**PRIVAT DNS-INTEGRERING**||
    |Integrera med privat DNS-zon |Välj **Ja**. |
    |Privat DNS-zon |Välj *(Ny)privatelink.postgres.database.azure.com* |
    |||

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure validerar din konfiguration. 
2. När meddelandet **Validering har skickats** väljer du **Skapa**. 

    ![Privat länk skapad](media/concepts-data-access-and-security-private-link/show-postgres-private-link.png)

    > [!NOTE] 
    > FQDN i kundens DNS-inställning matchas inte till den privata IP-konfigurerade. Du måste ställa in en DNS-zon för den konfigurerade FQDN som visas [här](../dns/dns-operations-recordsets-portal.md).

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

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Få tillgång till PostgreSQL-servern privat från den virtuella datorn

1. Öppna PowerShell i fjärrskrivbordet *för myVM.*

2. Skriv `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`in . 

    Du får ett meddelande som liknar detta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. Testa den privata länkanslutningen för PostgreSQL-servern med valfri tillgänglig klient. I exemplet nedan har jag använt [Azure Data studio](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) för att utföra åtgärden.

4. Ange eller välj den här informationen i **Ny anslutning:**

    | Inställning | Värde |
    | ------- | ----- |
    | Servertyp| Välj **PostgreSQL**.|
    | servernamn| Välj *mydemopostgresserver.privatelink.postgres.database.azure.com* |
    | Användarnamn | Ange användarnamn username@servername som anges under skapandet av PostgreSQL-servern. |
    |lösenord |Ange ett lösenord som anges när PostgreSQL-servern skapades. |
    |SSL|Välj **Obligatoriskt**.|
    ||

5. Välj Anslut.

6. Bläddra i databaser från vänstermeny.

7. (Valfritt) Skapa eller fråga information från postgreSQL-servern.

8. Stäng fjärrskrivbordsanslutningen till myVm.

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med den privata slutpunkten, PostgreSQL-servern och den virtuella datorn tar du bort resursgruppen och alla resurser som den innehåller:

1. Skriv *in myResourceGroup* i **sökrutan** högst upp på portalen och välj *myResourceGroup* från sökresultaten.
2. Välj **Ta bort resursgrupp**.
3. Ange myResourceGroup för **TYP RESURSGRUPPNAMN** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I det här beslutet skapade du en virtuell dator i ett virtuellt nätverk, en Azure-databas för PostgreSQL - Single server och en privat slutpunkt för privat åtkomst. Du har anslutit till en virtuell dator från internet och kommunicerade säkert till PostgreSQL-servern med private link. Mer information om privata slutpunkter finns i [Vad är Azure-privat slutpunkt](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).
