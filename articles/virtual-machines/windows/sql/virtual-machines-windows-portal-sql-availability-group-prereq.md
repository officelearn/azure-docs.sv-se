---
title: 'Självstudiekurs: Förutsättningar för tillgänglighetsgrupp'
description: Den här självstudien visar hur du konfigurerar förutsättningarna för att skapa en SQL Server Always On-tillgänglighetsgrupp på virtuella Azure-datorer.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: c80a4c07e0649612b4c024cac79833c4b730f55e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060173"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Förutsättningar för att skapa alltid på tillgänglighetsgrupper på SQL Server på virtuella Azure-datorer

Den här självstudien visar hur du slutför förutsättningarna för att skapa en [SQL Server Always On-tillgänglighetsgrupp på virtuella Azure-datorer](virtual-machines-windows-portal-sql-availability-group-tutorial.md). När du är klar med förutsättningarna har du en domänkontrollant, två virtuella SQL Server-datorer och en vittnesserver i en enda resursgrupp.

**Tidsuppskattning**: Det kan ta ett par timmar att slutföra förutsättningarna. Mycket av denna tid går åt till att skapa virtuella datorer.

Följande diagram illustrerar vad du bygger i självstudien.

![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Granska dokumentation för tillgänglighetsgruppen

Den här självstudien förutsätter att du har en grundläggande förståelse för SQL Server Always On tillgänglighetsgrupper. Om du inte är bekant med den här tekniken läser du [Översikt över SQL Server (Always On Availability Groups).](https://msdn.microsoft.com/library/ff877884.aspx)


## <a name="create-an-azure-account"></a>Skapa ett Azure-konto
Du behöver ett Azure-konto. Du kan [öppna ett kostnadsfritt Azure-konto](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) eller [aktivera Visual Studio-prenumerationsförmåner](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka **+** här om du vill skapa ett nytt objekt i portalen.

   ![Nytt objekt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Skriv **resursgrupp** i sökfönstret **på Marketplace.**

   ![Resursgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Klicka på **Resursgrupp**.
5. Klicka på **Skapa**.
6. Under **Resursgruppsnamn**skriver du ett namn för resursgruppen. Skriv till exempel **sql-ha-rg**.
7. Om du har flera Azure-prenumerationer kontrollerar du att prenumerationen är den Azure-prenumeration som du vill skapa tillgänglighetsgruppen i.
8. Välj en plats. Platsen är den Azure-region där du vill skapa tillgänglighetsgruppen. Den här artikeln bygger alla resurser på en Azure-plats.
9. Kontrollera att **fäst på instrumentpanelen** är markerat. Den här valfria inställningen placerar en genväg för resursgruppen på Instrumentpanelen för Azure-portalen.

   ![Resursgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Klicka på **Skapa** för att skapa resursgruppen.

Azure skapar resursgruppen och fäster en genväg till resursgruppen i portalen.

## <a name="create-the-network-and-subnets"></a>Skapa nätverk och undernät
Nästa steg är att skapa nätverk och undernät i Azure-resursgruppen.

Lösningen använder ett virtuellt nätverk med två undernät. Översikten [över virtuella nätverk](../../../virtual-network/virtual-networks-overview.md) innehåller mer information om nätverk i Azure.

Så här skapar du det virtuella nätverket:

1. Klicka på **+ Lägg till**i resursgruppen i Azure-portalen. 

   ![Nytt objekt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Sök efter **virtuellt nätverk**.

     ![Sök virtuellt nätverk](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Klicka på **Virtuellt nätverk**.
4. Klicka på distributionsmodellen **för Resurshanteraren** i det **virtuella nätverket**och klicka sedan på **Skapa**.

    I följande tabell visas inställningarna för det virtuella nätverket:

   | **Field** | Värde |
   | --- | --- |
   | **Namn** |autoHAVNET |
   | **Adressutrymme** |10.33.0.0/24 |
   | **Namn på undernät** |Admin |
   | **Adressintervall för undernät** |10.33.0.0/29 |
   | **Prenumeration** |Ange vilken prenumeration du tänker använda. **Prenumerationen** är tom om du bara har en prenumeration. |
   | **Resursgrupp** |Välj **Använd befintliga** och välj namnet på resursgruppen. |
   | **Location** |Ange Azure-platsen. |

   Adressutrymmet och adressintervallet för undernätet kan skilja sig från tabellen. Beroende på din prenumeration föreslår portalen ett tillgängligt adressutrymme och motsvarande undernätsadressintervall. Om det inte finns tillräckligt med adressutrymme använder du en annan prenumeration.

   I exemplet används undernätsnamnet **Admin**. Det här undernätet är till för domänkontrollanterna.

5. Klicka på **Skapa**.

   ![Konfigurera det virtuella nätverket](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure returnerar dig till portalinstrumentpanelen och meddelar dig när det nya nätverket skapas.

### <a name="create-a-second-subnet"></a>Skapa ett andra undernät
Det nya virtuella nätverket har ett undernät med namnet **Admin**. Domänkontrollanterna använder det här undernätet. Virtuella SQL Server-datorer använder ett andra undernät med namnet **SQL**. Så här konfigurerar du det här undernätet:

1. Klicka på den resursgrupp som du **SQL-HA-RG**skapade i instrumentpanelen. Leta reda på nätverket i resursgruppen under **Resurser**.

    Om **SQL-HA-RG** inte visas hittar du den genom att klicka på **Resursgrupper** och filtrera efter resursgruppsnamnet.
2. Klicka på **autoHAVNET** i listan över resurser. 
3. Under **Inställningar** **i**det virtuella nätverket **autoHAVNET** .

    Observera det undernät som du redan har skapat.

   ![Konfigurera det virtuella nätverket](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Skapa ett andra undernät. Klicka på **+ undernät**.
6. Konfigurera undernätet i **Lägg till undernät**genom att skriva **sqlsubnet** under **Namn**. Azure anger automatiskt ett giltigt **adressintervall**. Kontrollera att det här adressintervallet har minst 10 adresser i det. I en produktionsmiljö kan du behöva fler adresser.
7. Klicka på **OK**.

    ![Konfigurera det virtuella nätverket](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

I följande tabell sammanfattas inställningarna för nätverkskonfiguration:

| **Field** | Värde |
| --- | --- |
| **Namn** |**autoHAVNET** |
| **Adressutrymme** |Det här värdet beror på tillgängliga adressutrymmen i prenumerationen. Ett typiskt värde är 10.0.0.0/16. |
| **Namn på undernät** |**administratör** |
| **Adressintervall för undernät** |Det här värdet beror på tillgängliga adressintervall i din prenumeration. Ett typiskt värde är 10.0.0.0/24. |
| **Namn på undernät** |**sqlsubnet** |
| **Adressintervall för undernät** |Det här värdet beror på tillgängliga adressintervall i din prenumeration. Ett typiskt värde är 10.0.1.0/24. |
| **Prenumeration** |Ange vilken prenumeration du tänker använda. |
| **Resursgrupp** |**SQL-HA-RG** |
| **Location** |Ange samma plats som du valde för resursgruppen. |

## <a name="create-availability-sets"></a>Skapa tillgänglighetsuppsättningar

Innan du skapar virtuella datorer måste du skapa tillgänglighetsuppsättningar. Tillgänglighetsuppsättningar minskar stilleståndstiden för planerade eller oplanerade underhållshändelser. En Azure-tillgänglighetsuppsättning är en logisk grupp av resurser som Azure placerar på fysiska feldomäner och uppdatera domäner. En feldomän säkerställer att medlemmarna i tillgänglighetsuppsättningen har separata ström- och nätverksresurser. En uppdateringsdomän säkerställer att medlemmar i tillgänglighetsuppsättningen inte tas ned för underhåll samtidigt. Mer information finns [i Hantera tillgängligheten för virtuella datorer](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Du behöver två tillgänglighetsuppsättningar. En är för domänkontrollanterna. Den andra är för virtuella SQL Server-datorer.

Om du vill skapa en tillgänglighetsuppsättning går du till resursgruppen och klickar på **Lägg till**. Filtrera resultaten genom att skriva **tillgänglighetsuppsättning**. Klicka på **Tillgänglighetsuppsättning** i resultatet och klicka sedan på **Skapa**.

Konfigurera två tillgänglighetsuppsättningar enligt parametrarna i följande tabell:

| **Field** | Tillgänglighetsuppsättning för domänkontrollant | Tillgänglighetsuppsättning för SQL Server |
| --- | --- | --- |
| **Namn** |adavailabilityset |sqlavailabilityset |
| **Resursgrupp** |SQL-HA-RG |SQL-HA-RG |
| **Feldomäner** |3 |3 |
| **Uppdateringsdomäner** |5 |3 |

När du har skapat tillgänglighetsuppsättningarna går du tillbaka till resursgruppen i Azure-portalen.

## <a name="create-domain-controllers"></a>Skapa domänkontrollanter
När du har skapat nätverks-, undernäts- och tillgänglighetsuppsättningarna är du redo att skapa de virtuella datorerna för domänkontrollanterna.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Skapa virtuella datorer för domänkontrollanterna
Om du vill skapa och konfigurera domänkontrollanterna går du tillbaka till resursgruppen **SQL-HA-RG.**

1. Klicka på **Lägg till**. 
2. Skriv **Windows Server 2016 Datacenter**.
3. Klicka på **Windows Server 2016 Datacenter**. Kontrollera att distributionsmodellen är **Resurshanterare**i **Windows Server 2016**och klicka sedan på **Skapa**. 

Upprepa föregående steg för att skapa två virtuella datorer. Namnge de två virtuella datorerna:

* ad-primär-dc
* ad-sekundär-dc

  > [!NOTE]
  > Den virtuella datorn **ad-secondary-dc** är valfri för att ge hög tillgänglighet för Active Directory Domain Services.
  >
  >

I följande tabell visas inställningarna för dessa två datorer:

| **Field** | Värde |
| --- | --- |
| **Namn** |Första domänkontrollanten: *ad-primary-dc*.</br>Andra domänkontrollanten *ad-secondary-dc*. |
| **Disktyp för virtuell dator** |SSD |
| **Användarnamn** |DomänAdmin |
| **Lösenord** |Contoso!0000 |
| **Prenumeration** |*Din prenumeration* |
| **Resursgrupp** |SQL-HA-RG |
| **Location** |*Din plats* |
| **Storlek** |DS1_V2 |
| **Lagring** | **Använda hanterade diskar** - **Ja** |
| **Virtuellt nätverk** |autoHAVNET |
| **Undernät** |admin |
| **Offentlig IP-adress** |*Samma namn som den virtuella datorn* |
| **Säkerhetsgrupp för nätverk** |*Samma namn som den virtuella datorn* |
| **Tillgänglighetsuppsättning** |adavailabilityset </br>**Feldomäner**:2 </br>**Uppdatera domäner**:2|
| **Diagnostik** |Enabled |
| **Lagringskonto för diagnostik** |*Skapas automatiskt* |

   >[!IMPORTANT]
   >Du kan bara placera en virtuell dator i en tillgänglighetsuppsättning när du skapar den. Du kan inte ändra tillgänglighetsuppsättningen när en virtuell dator har skapats. Se [Hantera tillgängligheten för virtuella datorer](../manage-availability.md).

Azure skapar de virtuella datorerna.

När de virtuella datorerna har skapats konfigurerar du domänkontrollanten.

### <a name="configure-the-domain-controller"></a>Konfigurera domänkontrollanten
I följande steg konfigurerar du **den ad primary-dc-datorn** som domänkontrollant för corp.contoso.com.

1. Öppna resursgruppen **SQL-HA-RG** i portalen och välj **den ad-primary-dc-datorn.** På **ad primary-dc**klickar du på **Anslut** för att öppna en RDP-fil för fjärrskrivbordsåtkomst.

    ![Ansluta till en virtuell dator](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Logga in med det konfigurerade administratörskontot (**\DomainAdmin**) och lösenord (**Contoso!0000**).
3. Som standard ska **instrumentpanelen i Serverhanteraren** visas.
4. Klicka på länken **Lägg till roller och funktioner** på instrumentpanelen.

    ![Serverhanteraren - Lägg till roller](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Välj **Nästa** tills du kommer till avsnittet **Serverroller.**
6. Välj rollerna **Active Directory Domain Services** och DNS **Server.** När du uppmanas att lägga till ytterligare funktioner som krävs av dessa roller.

   > [!NOTE]
   > Windows varnar dig om att det inte finns någon statisk IP-adress. Om du testar konfigurationen klickar du på **Fortsätt**. För produktionsscenarier anger du IP-adressen till statisk i Azure-portalen eller [använder PowerShell för att ange den statiska IP-adressen för domänkontrollantdatorn](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Dialogrutan Lägg till roller](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Klicka på **Nästa** tills du kommer till avsnittet **Bekräftelse.** Markera kryssrutan **Starta om målservern automatiskt om det behövs.**
8. Klicka på **Installera**.
9. När funktionerna har installerats klart går du tillbaka till **instrumentpanelen i Serverhanteraren.**
10. Välj det nya **AD DS-alternativet** i den vänstra rutan.
11. Klicka på länken **Mer** i det gula varningsfältet.

    ![AD DS-dialogrutan på den virtuella DNS-servern](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. Klicka på **Befordra den här servern till en domänkontrollant**i kolumnen **Åtgärd** i dialogrutan Uppgifter **om alla** servrar.
13. Använd följande värden i **konfigurationsguiden**för Active Directory Domain Services:

    | **Sida** | Inställning |
    | --- | --- |
    | **Distributionskonfiguration** |**Lägga till en ny skog**<br/> **Rotdomännamn** = corp.contoso.com |
    | **Alternativ för domänkontrollant** |**DSRM-lösenord** = Contoso!0000<br/>**Bekräfta lösenord** = Contoso!0000 |
14. Klicka på **Nästa** om du vill gå igenom de andra sidorna i guiden. På sidan **Kravkontroll** kontrollerar du att följande meddelande visas: **Alla nödvändiga kontroller har godkänts**. Du kan granska alla tillämpliga varningsmeddelanden, men det är möjligt att fortsätta med installationen.
15. Klicka på **Installera**. Den virtuella datorn **ad-primary-dc** startas om automatiskt.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Observera IP-adressen för den primära domänkontrollanten

Använd den primära domänkontrollanten för DNS. Observera den primära IP-adressen för domänkontrollanten.

Ett sätt att få den primära domänkontrollantens IP-adress är via Azure-portalen.

1. Öppna resursgruppen på Azure-portalen.

2. Klicka på den primära domänkontrollanten.

3. Klicka på **Nätverksgränssnitt**på den primära domänkontrollanten .

![Nätverksgränssnitt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Observera den privata IP-adressen för den här servern.

### <a name="configure-the-virtual-network-dns"></a>Konfigurera DNS för det virtuella nätverket
När du har skapat den första domänkontrollanten och aktiverat DNS på den första servern konfigurerar du det virtuella nätverket så att den här servern används för DNS.

1. Klicka på det virtuella nätverket i Azure-portalen.

2. Klicka på **DNS-server**under **Inställningar**.

3. Klicka på **Anpassad**och skriv den privata IP-adressen för den primära domänkontrollanten.

4. Klicka på **Spara**.

### <a name="configure-the-second-domain-controller"></a>Konfigurera den andra domänkontrollanten
När den primära domänkontrollanten har startats om kan du konfigurera den andra domänkontrollanten. Det här valfria steget är för hög tillgänglighet. Så här konfigurerar du den andra domänkontrollanten:

1. Öppna resursgruppen **SQL-HA-RG** i portalen och välj den **ad-secondary-dc-datorn.** På **ad-secondary-dc**klickar du på **Anslut** för att öppna en RDP-fil för fjärrskrivbordsåtkomst.
2. Logga in på den virtuella datorn med hjälp av det konfigurerade administratörskontot (**BUILTIN\DomainAdmin**) och lösenord (**Contoso!0000**).
3. Ändra den önskade DNS-serveradressen till domänkontrollantens adress.
4. Klicka på nätverksgränssnittet i **Nätverks- och delningscenter.**
   ![Nätverksgränssnitt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Klicka på **Egenskaper**.
6. Välj **Internet Protocol Version 4 (TCP/IPv4)** och klicka på **Egenskaper**.
7. Välj **Använd följande DNS-serveradresser** och ange adressen till den primära domänkontrollanten på **önskad DNS-server**.
8. Klicka på **OK**och sedan **stäng** för att genomföra ändringarna. Du kan nu ansluta till den virtuella datorn för att **corp.contoso.com**.

   >[!IMPORTANT]
   >Om du förlorar anslutningen till fjärrskrivbordet efter att du har ändrat DNS-inställningen går du till Azure-portalen och startar om den virtuella datorn.

9. Öppna **Instrumentpanelen**för Serverhanteraren från fjärrskrivbord till den sekundära domänkontrollanten .
10. Klicka på länken **Lägg till roller och funktioner** på instrumentpanelen.

    ![Serverhanteraren - Lägg till roller](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Välj **Nästa** tills du kommer till avsnittet **Serverroller.**
12. Välj rollerna **Active Directory Domain Services** och DNS **Server.** När du uppmanas att lägga till ytterligare funktioner som krävs av dessa roller.
13. När funktionerna har installerats klart går du tillbaka till **instrumentpanelen i Serverhanteraren.**
14. Välj det nya **AD DS-alternativet** i den vänstra rutan.
15. Klicka på länken **Mer** i det gula varningsfältet.
16. Klicka på **Befordra den här servern till en domänkontrollant**i kolumnen **Åtgärd** i dialogrutan Uppgifter **om alla** servrar.
17. Under **Distributionskonfiguration**väljer du **Lägg till en domänkontrollant i en befintlig domän**.
    ![Konfiguration av distribution](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Klicka på **Markera**.
19. Anslut med hjälp av administratörskontot **(CORP. CONTOSO.COM\domainadmin**) och lösenord (**Contoso!0000**).
20. Klicka på **domänen i Välj en domän i skogen**och klicka sedan på **OK**.
21. I **Domänkontrollantalternativ**använder du standardvärdena och anger ett DSRM-lösenord.

    >[!NOTE]
    >**Sidan DNS-alternativ** kan varna dig om att en delegering för den här DNS-servern inte kan skapas. Du kan ignorera den här varningen i miljöer som inte är produktionsmiljöer.
22. Klicka på **Nästa** tills dialogrutan når kontrollen **Förutsättningar.** Klicka på **Installera**.

Starta om servern när servern har slutförts.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Lägga till den privata IP-adressen i den andra domänkontrollanten i VPN DNS-servern

I Azure-portalen, under virtuellt nätverk, ändrar du DNS-servern så att den inkluderar IP-adressen för den sekundära domänkontrollanten. Med den här inställningen kan DNS-tjänsten redundans.

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a>Konfigurera domänkontona

I nästa steg konfigurerar du Active Directory-kontona. I följande tabell visas kontona:

| |Installationskonto<br/> |sqlserver-0 <br/>SQL Server- och SQL Agent-tjänstkonto |sqlserver-1<br/>SQL Server- och SQL Agent-tjänstkonto
| --- | --- | --- | ---
|**Förnamn** |Installera |SQLSvc1 | SQLSvc2
|**Användare SamAccountName** |Installera |SQLSvc1 | SQLSvc2

Följ följande steg för att skapa varje konto.

1. Logga in på **den ad-primary-dc-datorn.**
2. Välj **Verktyg**i **Serverhanteraren**och klicka sedan på **Active Directory Administrationscenter**.   
3. Välj **corp (lokal)** i den vänstra rutan.
4. Välj **Nytt** **i** fönstret Till höger och klicka sedan på **Användare**.
   ![Active Directory Administrationscenter](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Ange ett komplext lösenord för varje konto.<br/> För icke-produktionsmiljöer anger du att användarkontot aldrig ska upphöra att gälla.

5. Klicka på **OK** för att skapa användaren.
6. Upprepa föregående steg för vart och ett av de tre kontona.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Bevilja de behörigheter som krävs till installationskontot
1. Välj **corp (lokal)** i den vänstra rutan i **Active Directory Administrationscenter.** Klicka sedan på **Egenskaper**i fönstret **Aktiviteter till** höger .

    ![CORP-användaregenskaper](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Välj **Tillägg**och klicka sedan på knappen **Avancerat** på fliken **Säkerhet.**
3. Klicka på **Lägg till**i dialogrutan **Avancerade säkerhetsinställningar för korpar.**
4. Klicka på **Välj ett huvudnamn,** sök efter **CORP\Install**och klicka sedan på **OK**.
5. Markera kryssrutan **Läs alla egenskaper.**

6. Markera kryssrutan **Skapa datorobjekt.**

     ![Behörigheter för Corp-användare](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Klicka på **OK** och sedan på **OK** igen. Stäng **fönstret för korpegenskaper.**

Nu när du har konfigurerat Active Directory och användarobjekten skapar du två virtuella SQL Server-datorer och en virtuell vittnesserver. Gå sedan med alla tre till domänen.

## <a name="create-sql-server-vms"></a>Skapa virtuella SQL Server-datorer

Skapa ytterligare tre virtuella datorer. Lösningen kräver två virtuella datorer med SQL Server-instanser. En tredje virtuell dator fungerar som vittne. Windows Server 2016 kan använda ett [molnvittne,](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)men för att det här dokumentet ska vara förenligt med tidigare operativsystem använder ett virtuellt dator för ett vittne.  

Innan du fortsätter överväga följande designbeslut.

* **Lagring - Azure-hanterade diskar**

   Använd Azure Managed Disks för lagring av virtuella datorer. Microsoft rekommenderar hanterade diskar för virtuella SQL Server-datorer. Managed Disks hanterar lagring i bakgrunden. När virtuella datorer med Managed Disks finns i samma tillgänglighetsuppsättning, distribuerar Azure dessutom lagringsresurser för att tillhandahålla rätt redundans. Mer information finns i [Översikt över Azure Managed Disks](../managed-disks-overview.md). Mer information om hanterade diskar i en tillgänglighetsuppsättning finns i [Använda hanterade diskar för virtuella datorer i en tillgänglighetsuppsättning](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Nätverk - Privata IP-adresser i produktion**

   För de virtuella datorerna använder den här självstudien offentliga IP-adresser. En offentlig IP-adress möjliggör fjärranslutning direkt till den virtuella datorn via Internet - det gör konfigurationssteg enklare. I produktionsmiljöer rekommenderar Microsoft endast privata IP-adresser för att minska sårbarhetsavtrycket för SQL Server-instansens VM-resurs.

### <a name="create-and-configure-the-sql-server-vms"></a>Skapa och konfigurera virtuella SQL Server-datorer
Skapa sedan tre virtuella datorer – två virtuella SQL Server-datorer och en virtuell dator för ytterligare en klusternod. Om du vill skapa var och en av de virtuella datorerna går du tillbaka till resursgruppen **SQL-HA-RG,** klickar på **Lägg till**, sök efter lämpligt galleriobjekt, klickar på **Virtuell dator**och klickar sedan på Från **galleri**. Använd informationen i följande tabell för att skapa de virtuella datorerna:


| Sida | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Markera lämpligt galleriobjekt |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise på Windows Server 2016** |**SQL Server 2016 SP1 Enterprise på Windows Server 2016** |
| Grunderna i konfiguration av virtuella **datorer** |**Namn** = kluster-fsw<br/>**Användarnamn** = DomainAdmin<br/>**Lösenord** = Contoso!0000<br/>**Prenumeration** = Din prenumeration<br/>**Resursgrupp** = SQL-HA-RG<br/>**Plats** = Din azurblå plats |**Namn** = sqlserver-0<br/>**Användarnamn** = DomainAdmin<br/>**Lösenord** = Contoso!0000<br/>**Prenumeration** = Din prenumeration<br/>**Resursgrupp** = SQL-HA-RG<br/>**Plats** = Din azurblå plats |**Namn** = sqlserver-1<br/>**Användarnamn** = DomainAdmin<br/>**Lösenord** = Contoso!0000<br/>**Prenumeration** = Din prenumeration<br/>**Resursgrupp** = SQL-HA-RG<br/>**Plats** = Din azurblå plats |
| **Konfigurationsstorlek för** virtuell dator |**STORLEK** = DS1\_V2 (1 vCPU, 3,5 GB) |**STORLEK** = DS2\_V2 (2 vCPUs, 7 GB)</br>Storleken måste ha stöd för SSD-lagring (stöd för Premium-disk. )) |**STORLEK** = DS2\_V2 (2 vCPUs, 7 GB) |
| **Konfigurationsinställningar för** virtuella datorer |**Lagring**: Använd hanterade diskar.<br/>**Virtuellt nätverk** = autoHAVNET<br/>**Undernät** = sqlsubnet(10.1.1.0/24)<br/>**Offentlig IP-adress** genereras automatiskt.<br/>**Nätverkssäkerhetsgrupp** = Ingen<br/>**Övervakningsdiagnostik** = Aktiverad<br/>**Diagnostiklagringskonto** = Använda ett automatiskt genererat lagringskonto<br/>**Tillgänglighetsuppsättning** = sqlAvailabilitySet<br/> |**Lagring**: Använd hanterade diskar.<br/>**Virtuellt nätverk** = autoHAVNET<br/>**Undernät** = sqlsubnet(10.1.1.0/24)<br/>**Offentlig IP-adress** genereras automatiskt.<br/>**Nätverkssäkerhetsgrupp** = Ingen<br/>**Övervakningsdiagnostik** = Aktiverad<br/>**Diagnostiklagringskonto** = Använda ett automatiskt genererat lagringskonto<br/>**Tillgänglighetsuppsättning** = sqlAvailabilitySet<br/> |**Lagring**: Använd hanterade diskar.<br/>**Virtuellt nätverk** = autoHAVNET<br/>**Undernät** = sqlsubnet(10.1.1.0/24)<br/>**Offentlig IP-adress** genereras automatiskt.<br/>**Nätverkssäkerhetsgrupp** = Ingen<br/>**Övervakningsdiagnostik** = Aktiverad<br/>**Diagnostiklagringskonto** = Använda ett automatiskt genererat lagringskonto<br/>**Tillgänglighetsuppsättning** = sqlAvailabilitySet<br/> |
| **SQL Server-inställningar för konfiguration av** virtuella datorer |Inte tillämpligt |**SQL-anslutning** = Privat (inom virtuellt nätverk)<br/>**Port** = 1433<br/>**SQL-autentisering** = Inaktivera<br/>**Lagringskonfiguration** = Allmänt<br/>**Automatisk patchning** = söndag kl 2:00<br/>**Automatisk säkerhetskopiering** = Inaktiverad</br>**Azure Key Vault-integrering** = Inaktiverad |**SQL-anslutning** = Privat (inom virtuellt nätverk)<br/>**Port** = 1433<br/>**SQL-autentisering** = Inaktivera<br/>**Lagringskonfiguration** = Allmänt<br/>**Automatisk patchning** = söndag kl 2:00<br/>**Automatisk säkerhetskopiering** = Inaktiverad</br>**Azure Key Vault-integrering** = Inaktiverad |

<br/>

> [!NOTE]
> De datorstorlekar som föreslås här är avsedda för testning av tillgänglighetsgrupper i virtuella Azure-datorer. Bästa prestanda på produktionsarbetsbelastningar finns i rekommendationerna för SQL Server-maskinstorlekar och konfiguration i [metodtips för prestanda för SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

När de tre virtuella datorerna har etablerats fullständigt måste du ansluta dem till **corp.contoso.com** domänen och bevilja CORP\Install administrativa rättigheter till datorerna.

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>Ansluta till servrarna till domänen

Du kan nu ansluta till de virtuella datorerna för att **corp.contoso.com**. Gör följande för både virtuella SQL Server-datorer och filresursvittneservern:

1. Fjärranslutning till den virtuella datorn med **BUILTIN\DomainAdmin**.
2. Klicka på **Lokal server**i **Serverhanteraren**.
3. Klicka på länken **ARBETSGRUPP.**
4. Klicka på **Ändra**i avsnittet **Datornamn.**
5. Markera kryssrutan **Domän** och skriv **corp.contoso.com** i textrutan. Klicka på **OK**.
6. I popup-dialogrutan **Windows-säkerhet** anger du autentiseringsuppgifterna för standarddomänadministratörskontot (**CORP\DomainAdmin**) och lösenordet (**Contoso!0000**).
7. När meddelandet Välkommen till domänen corp.contoso.com visas klickar du på **OK**.
8. Klicka på **Stäng**och sedan **på Starta om nu** i popup-dialogrutan.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Lägga till Corp\Install-användaren som administratör på varje virtuell kluster-dator

När varje virtuell dator har startats om som medlem i domänen lägger du till **CORP\Install** som medlem i den lokala administratörsgruppen.

1. Vänta tills den virtuella datorn startas om och starta sedan RDP-filen igen från den primära domänkontrollanten för att logga in på **sqlserver-0** med **corp\DomainAdmin-kontot.**
   >[!TIP]
   >Se till att du loggar in med domänadministratörskontot. I föregående steg använde du det INBYGGDA administratörskontot. Använd domänkontot nu när servern finns i domänen. I rdp-sessionen anger du\\*DOMÄN-användarnamn*. *DOMAIN*

2. Välj **Verktyg**i **Serverhanteraren**och klicka sedan på **Datorhantering**.
3. Expandera **Lokala användare och grupper**i fönstret **Datorhantering** och välj sedan **Grupper**.
4. Dubbelklicka på gruppen **Administratörer.**
5. Klicka på knappen Lägg **till** i dialogrutan **Administratörsegenskaper.**
6. Ange användaren **CORP\Install**och klicka sedan på **OK**.
7. Stäng dialogrutan **Administratörsegenskaper** genom att klicka på **OK.**
8. Upprepa föregående steg på **sqlserver-1** och **cluster-fsw**.

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>Ange SQL Server-tjänstkonton

Ange SQL Server-tjänstkontot på varje VIRTUELL SQL Server. Använd de konton som du skapade när du konfigurerade domänkontona.

1. Öppna **SQL Server Configuration Manager**.
2. Högerklicka på SQL Server-tjänsten och klicka sedan på **Egenskaper**.
3. Ange konto och lösenord.
4. Upprepa dessa steg på den andra virtuella datorn för SQL Server.  

För SQL Server-tillgänglighetsgrupper måste varje VIRTUELL SQL Server köras som ett domänkonto.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Skapa en inloggning på varje VIRTUELL SQL Server för installationskontot

Använd installationskontot (CORP\install) för att konfigurera tillgänglighetsgruppen. Det här kontot måste vara medlem i **sysadmin-rollen** för fasta servrar på varje VIRTUELL SQL Server.This account needs to be a member of the sysadmin fixed server role on each SQL Server VM. I följande steg skapas en inloggning för installationskontot:

1. Anslut till servern via RDP (Remote Desktop Protocol) med hjälp av * \<kontot MachineName\>\DomainAdmin.*

1. Öppna SQL Server Management Studio och anslut till den lokala instansen av SQL Server.

1. Klicka på **Säkerhet**i **Objektutforskaren**.

1. Högerklicka på **Inloggningar**. Klicka på **Ny inloggning**.

1. Klicka på **Sök** **i Login - Nytt**.

1. Klicka på **Platser**.

1. Ange autentiseringsuppgifterna för domänadministratörens nätverk.

1. Använd installationskontot.

1. Ange att inloggningen ska vara medlem i rollen fast server **för sysadmin.**

1. Klicka på **OK**.

Upprepa föregående steg på den andra virtuella datorn för SQL Server.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Lägga till funktioner för redundanskluster i båda virtuella SQL Server-datorer

Så här lägger du till funktioner för redundanskluster:

1. Anslut till den virtuella SQL Server-datorn via RDP (Remote Desktop Protocol) med hjälp av *CORP\install-kontot.* Öppna **instrumentpanelen för Serverhanteraren**.
2. Klicka på länken **Lägg till roller och funktioner** på instrumentpanelen.

    ![Serverhanteraren - Lägg till roller](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Välj **Nästa** tills du kommer till avsnittet **Serverfunktioner.**
4. I **Funktioner**väljer du **Redundanskluster.**
5. Lägg till ytterligare funktioner som krävs.
6. Klicka på **Installera** om du vill lägga till funktionerna.

Upprepa stegen på den andra virtuella datorn för SQL Server.

  >[!NOTE]
  > Det här steget, tillsammans med att faktiskt ansluta virtuella SQL Server-datorer till redundansklustret, kan nu automatiseras med [Azure SQL VM CLI-](virtual-machines-windows-sql-availability-group-cli.md) och [Azure Quickstart-mallar](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall">Konfigurera brandväggen på varje VIRTUELL SQL Server-dator

Lösningen kräver att följande TCP-portar ska vara öppna i brandväggen:

- **VIRTUELL SQL Server:**<br/>
   Port 1433 för en standardinstans av SQL Server.
- **Azure belastningsutjämnare avsökning:**<br/>
   Alla tillgängliga portar. Exempel använder ofta 59999.
- **Slutpunkt för databasspegling:** <br/>
   Alla tillgängliga portar. Exempel använder ofta 5022.

Brandväggsportarna måste vara öppna på båda virtuella SQL Server-datorerna.

Hur du öppnar portarna beror på vilken brandväggslösning du använder. I nästa avsnitt beskrivs hur du öppnar portarna i Windows-brandväggen. Öppna de nödvändiga portarna på var och en av dina virtuella SQL Server-datorer.

### <a name="open-a-tcp-port-in-the-firewall"></a>Öppna en TCP-port i brandväggen

1. På den första **Startskärmen** för SQL Server startar du **Windows-brandväggen med avancerad säkerhet**.
2. Välj Inkommande regler **i**den vänstra rutan . Klicka på Ny **regel**i den högra rutan .
3. För **regeltyp**väljer du **Port**.
4. För porten anger du **TCP** och skriver lämpliga portnummer. Se följande exempel:

   ![SQL-brandvägg](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Klicka på **Nästa**.
6. Behåll **Tillåt anslutningen** markerad på sidan **Åtgärd** och klicka sedan på **Nästa**.
7. På sidan **Profil** godkänner du standardinställningarna och klickar sedan på **Nästa**.
8. På sidan **Namn** anger du ett regelnamn (till exempel **Azure LB Probe)** i textrutan **Namn** och klickar sedan på **Slutför**.

Upprepa dessa steg på den andra virtuella datorn för SQL Server.

## <a name="configure-system-account-permissions"></a>Konfigurera behörigheter för systemkonto

Om du vill skapa ett konto för systemkontot och bevilja lämpliga behörigheter utför du följande steg på varje SQL Server-instans:

1. Skapa ett `[NT AUTHORITY\SYSTEM]` konto för varje SQL Server-instans. Följande skript skapar det här kontot:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Bevilja följande behörigheter `[NT AUTHORITY\SYSTEM]` för varje SQL Server-instans:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Följande skript ger dessa behörigheter:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Nästa steg

* [Skapa en SQL Server Always On-tillgänglighetsgrupp på virtuella Azure-datorer](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
