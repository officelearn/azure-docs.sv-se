---
title: Grupperar tillgänglighet för SQL Server - virtuella datorer i Azure - krav för Hanteringsstrategierna | Microsoft Docs
description: Den här självstudiekursen visar hur du konfigurerar kraven för att skapa en SQL Server Always On-tillgänglighetsgrupp på virtuella Azure-datorer.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.openlocfilehash: 17eb51044aae7f1f1536e965eea1d1cdb0a1a42b
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817447"
---
# <a name="complete-the-prerequisites-for-creating-always-on-availability-groups-on-azure-virtual-machines"></a>Slutför stegen för att skapa Always On-Tillgänglighetsgrupper på virtuella Azure-datorer

Den här självstudiekursen visar hur du slutför stegen för att skapa en [SQL Server Always On-tillgänglighetsgrupp på virtuella Azure-datorer (VM)](virtual-machines-windows-portal-sql-availability-group-tutorial.md). När du är klar kraven, har du en domänkontrollant, två SQL Server-datorer och ett vittnesserver i en enskild resursgrupp.

**Uppskattad tidsåtgång**: Det kan ta ett par timmar att slutföra förutsättningarna. Mycket av den här tiden går åt till att skapa virtuella datorer.

Följande diagram illustrerar vad du skapar under kursen.

![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Granska availability group-dokumentationen

Den här självstudien förutsätter att du har grundläggande kunskaper om SQL Server Always On-Tillgänglighetsgrupper. Om du inte är bekant med den här tekniken finns i [översikt över alltid på Tillgänglighetsgrupper (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Skapa ett Azure-konto
Du behöver ett Azure-konto. Du kan [öppna ett kostnadsfritt konto](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) eller [aktivera Visual Studio-prenumerantförmåner](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
1. Logga in på [Azure Portal](http://portal.azure.com).
2. Klicka på **+** att skapa ett nytt objekt i portalen.

   ![Nytt objekt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Typ **resursgrupp** i den **Marketplace** sökfönstret.

   ![Resursgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Klicka på **resursgrupp**.
5. Klicka på **Skapa**.
6. Under **resursgruppens namn**, Skriv ett namn för resursgruppen. Skriv exempelvis **sql-ha-rg**.
7. Om du har flera Azure-prenumerationer kan du kontrollera att prenumerationen är Azure-prenumerationen som du vill skapa tillgänglighetsgruppen i.
8. Välj en plats. Platsen är den Azure-region där du vill skapa tillgänglighetsgruppen. Den här artikeln skapar alla resurser i en Azure-plats.
9. Kontrollera att **fäst på instrumentpanelen** kontrolleras. Den här valfria inställningen placerar en genväg för resursgruppen på instrumentpanelen för Azure portal.

   ![Resursgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Klicka på **skapa** att skapa resursgruppen.

Azure skapar resursgruppen och PIN-koder en genväg till resursgruppen i portalen.

## <a name="create-the-network-and-subnets"></a>Skapa nätverk och undernät
Nästa steg är att skapa nätverk och undernät i Azure-resursgruppen.

Lösningen använder ett virtuellt nätverk med två undernät. Den [översikt över virtuella nätverk](../../../virtual-network/virtual-networks-overview.md) innehåller mer information om nätverk i Azure.

Skapa det virtuella nätverket:

1. I Azure-portalen, i resursgruppen, klickar du på **+ Lägg till**. 

   ![Nytt objekt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Sök efter **virtuellt nätverk**.

     ![Sök virtuellt nätverk](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Klicka på **virtuellt nätverk**.
4. På den **virtuellt nätverk**, klickar du på den **Resource Manager** distributionsmodellen och klicka sedan på **skapa**.

    I följande tabell visas inställningarna för det virtuella nätverket:

   | **Fält** | Värde |
   | --- | --- |
   | **Namn** |autoHAVNET |
   | **Adressutrymme** |10.33.0.0/24 |
   | **Namn på undernät** |Administratör |
   | **Adressintervall för undernätet** |10.33.0.0/29 |
   | **Prenumeration** |Ange den prenumeration som du tänker använda. **Prenumeration** är tom om du bara har en prenumeration. |
   | **Resursgrupp** |Välj **Använd befintlig** och välj namnet på resursgruppen. |
   | **Plats** |Ange den Azure-platsen. |

   Adress adressutrymmet och undernätsegenskaperna adressintervall kan skilja sig från tabellen. Beroende på din prenumeration föreslår portalen en tillgänglig adressutrymme och motsvarande adressintervall för undernätet. Om det finns inte tillräckligt med adressutrymme använder du en annan prenumeration.

   I exemplet används namnet på undernätet **Admin**. Det här undernätet är för domänkontrollanter.

5. Klicka på **Skapa**.

   ![Konfigurera det virtuella nätverket](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure du återvänder till portalens instrumentpanel och meddelar dig när det nya nätverket har skapats.

### <a name="create-a-second-subnet"></a>Skapa ett till undernät
Det nya virtuella nätverket har ett undernät som heter **Admin**. Domänkontrollanter använder det här undernätet. Den SQL Server-datorer används ett andra undernät med namnet **SQL**. Så här konfigurerar du det här undernätet:

1. På instrumentpanelen, klicka på resursgruppen som du skapade **SQL-HA-RG**. Leta upp nätverket i resursgruppen under **resurser**.

    Om **SQL-HA-RG** inte visas, hitta den genom att klicka på **resursgrupper** och filtrering av resursgruppens namn.
2. Klicka på **autoHAVNET** på listan över resurser. 
3. På den **autoHAVNET** virtuella nätverk, under **inställningar** Välj **undernät**.

    Observera det undernät som du redan har skapat.

   ![Konfigurera det virtuella nätverket](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Skapa ett andra undernät. Klicka på **+ undernät**.
6. På **Lägg till undernät**, konfigurera undernätet genom att skriva **sqlsubnet** under **namn**. Azure automatiskt anger en giltig **adressintervall**. Kontrollera att den här adressintervall har minst 10-adresser i den. Du kan behöva fler adresser i en produktionsmiljö.
7. Klicka på **OK**.

    ![Konfigurera det virtuella nätverket](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

I följande tabell sammanfattas inställningarna för nätverk:

| **Fält** | Värde |
| --- | --- |
| **Namn** |**autoHAVNET** |
| **Adressutrymme** |Det här värdet är beroende av de tillgängliga adressutrymmena i din prenumeration. Ett typiskt värde är 10.0.0.0/16. |
| **Namn på undernät** |**admin** |
| **Adressintervall för undernätet** |Det här värdet beror på tillgängliga adressintervallen i din prenumeration. Ett typiskt värde är 10.0.0.0/24. |
| **Namn på undernät** |**sqlsubnet** |
| **Adressintervall för undernätet** |Det här värdet beror på tillgängliga adressintervallen i din prenumeration. Ett typiskt värde är 10.0.1.0/24. |
| **Prenumeration** |Ange den prenumeration som du tänker använda. |
| **Resursgrupp** |**SQL-HA-RG** |
| **Plats** |Ange samma plats som du valde för resursgruppen. |

## <a name="create-availability-sets"></a>Skapa tillgänglighetsuppsättningar

Innan du skapar virtuella datorer, måste du skapa tillgänglighetsuppsättningar. Tillgänglighetsuppsättningar minska avbrottstiden för planerat eller oplanerat underhåll. En Azure-tillgänglighetsuppsättning är en logisk grupp av resurser som Azure placerar fysiska feldomäner och uppdateringsdomäner. En feldomän garanterar att medlemmarna i tillgänglighetsuppsättningen har separata power- och nätverksresurser. En uppdateringsdomän säkerställer att medlemmar i tillgänglighetsuppsättningen inte är hämtade för underhåll på samma gång. Mer information finns i [hantera tillgängligheten för virtuella datorer](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Du behöver två tillgänglighetsuppsättningar. En är för domänkontrollanter. Andra är för SQL Server-datorer.

Om du vill skapa en tillgänglighetsuppsättning, gå till resursgruppen och klicka på **Lägg till**. Filtrera resultaten genom att skriva **tillgänglighetsuppsättning**. Klicka på **Tillgänglighetsuppsättning** i resultatet och klicka sedan på **skapa**.

Konfigurera två tillgänglighetsuppsättningar enligt parametrarna i följande tabell:

| **Fält** | Domain controller tillgänglighetsuppsättning | SQL Server-tillgänglighetsuppsättning |
| --- | --- | --- |
| **Namn** |adavailabilityset |sqlavailabilityset |
| **Resursgrupp** |SQL-HA-RG |SQL-HA-RG |
| **Feldomäner** |3 |3 |
| **Uppdatera domäner** |5 |3 |

När du skapar tillgänglighetsuppsättningar kan du gå tillbaka till resursgruppen på Azure-portalen.

## <a name="create-domain-controllers"></a>Skapa domänkontrollanter
När du har skapat nätverket, undernät, tillgänglighetsuppsättningar och en belastningsutjämnare mot Internet, är du redo att skapa de virtuella datorerna för domänkontrollanter.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Skapa virtuella datorer för domänkontrollanter
Om du vill skapa och konfigurera domänkontrollanterna kan återgå till den **SQL-HA-RG** resursgrupp.

1. Klicka på **Lägg till**. 
2. Typ **Windows Server 2016 Datacenter**.
3. Klicka på **Windows Server 2016 Datacenter**. I **Windows Server 2016 Datacenter**, kontrollera att distributionsmodellen är **Resource Manager**, och klicka sedan på **skapa**. 

Upprepa föregående steg för att skapa två virtuella datorer. Namnge två virtuella datorer:

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > Den **ad-sekundär-dc** virtuell dator är valfritt, att tillhandahålla hög tillgänglighet för Active Directory Domain Services.
  >
  >

I följande tabell visas inställningarna för dessa två datorer:

| **Fält** | Värde |
| --- | --- |
| **Namn** |Första domänkontrollant: *ad-primary-dc*.</br>Andra domänkontrollanten *ad-sekundär-dc*. |
| **Typ av virtuell datordisk** |SSD |
| **Användarnamn** |DomainAdmin |
| **Lösenord** |Contoso!0000 |
| **Prenumeration** |*Din prenumeration* |
| **Resursgrupp** |SQL-HA-RG |
| **Plats** |*Din plats* |
| **Storlek** |DS1_V2 |
| **Storage** | **Använda hanterade diskar** - **Ja** |
| **Virtuellt nätverk** |autoHAVNET |
| **Undernät** |admin |
| **Offentlig IP-adress** |*Samma namn som den virtuella datorn* |
| **Nätverkssäkerhetsgrupp** |*Samma namn som den virtuella datorn* |
| **Tillgänglighetsuppsättning** |adavailabilityset </br>**Feldomäner**: 2 </br>**Uppdateringsdomäner**: 2|
| **Diagnostik** |Enabled |
| **Diagnostiklagringskonto** |*Skapas automatiskt* |

   >[!IMPORTANT]
   >Du kan bara placera en virtuell dator i en tillgänglighetsuppsättning när du skapar den. Du kan inte ändra tillgänglighetsuppsättningen när en virtuell dator har skapats. Se [hantera tillgängligheten för virtuella datorer](../manage-availability.md).

Azure skapar de virtuella datorerna.

När de virtuella datorerna har skapats kan konfigurera en domänkontrollant.

### <a name="configure-the-domain-controller"></a>Konfigurera en domänkontrollant
I följande steg, konfigurerar den **ad-primary-dc** datorer som en domänkontrollant för corp.contoso.com.

1. I portalen, öppnar den **SQL-HA-RG** resource gruppen och välj den **ad-primary-dc** dator. På **ad-primary-dc**, klickar du på **Connect** att öppna en RDP-fil för fjärråtkomst till skrivbordet.

    ![Ansluta till en virtuell dator](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Logga in med ditt konfigurerade administratörskonto (**\DomainAdmin**) och lösenord (**Contoso! 0000**).
3. Som standard den **Serverhanteraren** instrumentpanelen visas.
4. Klicka på den **Lägg till roller och funktioner** länk på instrumentpanelen.

    ![Serverhanteraren – Lägg till roller](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Välj **nästa** tills du kommer till den **serverroller** avsnittet.
6. Välj den **Active Directory Domain Services** och **DNS-Server** roller. När du uppmanas, lägger du till ytterligare funktioner som krävs av dessa roller.

   > [!NOTE]
   > Windows varnar dig om att det finns ingen statisk IP-adress. Om du testar konfigurationen klickar du på **Fortsätt**. I produktionsscenarier ange IP-adress till statisk i Azure-portalen eller [använder PowerShell för att ange statiska IP-adressen för den domain controller datorn](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Lägg till roller dialogrutan](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Klicka på **nästa** tills du når den **bekräftelse** avsnittet. Välj den **starta om målservern automatiskt om det behövs** markerar du kryssrutan.
8. Klicka på **Installera**.
9. När funktionerna har installerat, gå tillbaka till den **Serverhanteraren** instrumentpanelen.
10. Välj den nya **AD DS** alternativet på den vänstra rutan.
11. Klicka på den **mer** länk på det gula fältet i varningen.

    ![Dialogrutan för AD DS på den virtuella datorn DNS-Server](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. I den **åtgärd** kolumnen i den **alla Server-aktivitetsinformation** dialogrutan klickar du på **befordra servern till en domänkontrollant**.
13. I den **guiden Active Directory Domain Services Configuration**, använder du följande värden:

    | **Sidan** | Inställning |
    | --- | --- |
    | **Distributionskonfiguration** |**Lägg till en ny skog**<br/> **Rotdomännamn** = corp.contoso.com |
    | **Alternativ för domänkontrollant** |**DSRM-lösenordet** = Contoso! 0000<br/>**Bekräfta lösenord** = Contoso! 0000 |
14. Klicka på **nästa** gå igenom de övriga sidorna i guiden. På den **Kravkontroll** kontrollerar du att du ser följande meddelande: **Alla kravkontroller har lyckats**. Du kan granska alla meddelanden, men det är möjligt att fortsätta med installationen.
15. Klicka på **Installera**. Den **ad-primary-dc** virtuella datorn startas om automatiskt.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Obs IP-adressen för den primära domänkontrollanten

Använd den primära domänkontrollanten för DNS. Observera IP-adress för primär domän kontrollenhet.

Ett sätt att hämta IP-adress för primär domän kontrollenhet är via Azure portal.

1. Öppna resursgruppen på Azure-portalen.

2. Klicka på den primära domänkontrollanten.

3. Klicka på den primära domänkontrollanten **nätverksgränssnitt**.

![Nätverksgränssnitt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Observera den privata IP-adressen för den här servern.

### <a name="configure-the-virtual-network-dns"></a>Konfigurera det virtuella nätverket DNS
När du skapar den första domänkontrollanten och aktiverar DNS på den första servern, kan du konfigurera det virtuella nätverket för att använda den här servern för DNS.

1. Klicka på det virtuella nätverket i Azure-portalen.

2. Under **inställningar**, klickar du på **DNS-Server**.

3. Klicka på **anpassade**, och Skriv den privata IP-adressen för den primära domänkontrollanten.

4. Klicka på **Spara**.

### <a name="configure-the-second-domain-controller"></a>Konfigurera den andra domänkontrollanten
När den primära domänkontrollanten om kan du konfigurera den andra domänkontrollanten. Det här valfria steget är för hög tillgänglighet. Följ dessa steg om du vill konfigurera den andra domänkontrollanten:

1. I portalen, öppnar den **SQL-HA-RG** resource gruppen och välj den **ad-sekundär-dc** dator. På **ad-sekundär-dc**, klickar du på **Connect** att öppna en RDP-fil för fjärråtkomst till skrivbordet.
2. Logga in på den virtuella datorn med hjälp av ditt konfigurerade administratörskonto (**BUILTIN\DomainAdmin**) och lösenord (**Contoso! 0000**).
3. Ändra önskade DNS-serveradressen till adressen för domänkontrollanten.
4. I **nätverks- och delningscenter**, klickar du på nätverksgränssnittet.
   ![Nätverksgränssnitt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Klicka på **Egenskaper**.
6. Välj **Internet Protocol Version 4 (TCP/IPv4)** och klicka på **egenskaper**.
7. Välj **Använd följande DNS-serveradresser** och ange adressen till den primära domänkontrollanten i **önskad DNS-server**.
8. Klicka på **OK**, och sedan **Stäng** att genomföra ändringarna. Du kan nu ansluta den virtuella datorn till **corp.contoso.com**.

   >[!IMPORTANT]
   >Om du förlorar anslutningen till din Fjärrskrivbord när du har ändrat DNS-inställningen kan gå till Azure-portalen och starta om den virtuella datorn.

9. Fjärrskrivbord till den sekundära domänkontrollanten, öppna **Serverhanterarens instrumentpanel**.
10. Klicka på den **Lägg till roller och funktioner** länk på instrumentpanelen.

    ![Serverhanteraren – Lägg till roller](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Välj **nästa** tills du kommer till den **serverroller** avsnittet.
12. Välj den **Active Directory Domain Services** och **DNS-Server** roller. När du uppmanas, lägger du till ytterligare funktioner som krävs av dessa roller.
13. När funktionerna har installerat, gå tillbaka till den **Serverhanteraren** instrumentpanelen.
14. Välj den nya **AD DS** alternativet på den vänstra rutan.
15. Klicka på den **mer** länk på det gula fältet i varningen.
16. I den **åtgärd** kolumnen i den **alla Server-aktivitetsinformation** dialogrutan klickar du på **befordra servern till en domänkontrollant**.
17. Under **distributionskonfiguration**väljer **lägger till en domänkontrollant i en befintlig domän**.
   ![Distributionskonfiguration](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Klicka på **Välj**.
19. Ansluta med hjälp av administratörskontot (**CORP. Contoso.COM\domainadmin**) och lösenord (**Contoso! 0000**).
20. I **Välj en domän i skogen**, klickar du på din domän och klicka sedan på **OK**.
21. I **alternativ för domänkontrollant**, använda standardvärdena och ange DSRM-lösenordet.

   >[!NOTE]
   >Den **DNS-alternativ** sidan kan varna dig att det inte går att skapa en delegering för den här DNS-servern. Du kan ignorera den här varningen i icke-produktionsmiljöer.
22. Klicka på **nästa** tills dialogrutan når den **krav** kontrollera. Klicka på **Installera**.

Starta om servern när servern är klar konfigurationsändringarna.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Lägga till den privata IP-adressen till den andra domänkontrollanten till VPN-DNS-Server

Ändra DNS-servern med IP-adressen för den sekundära domänkontrollanten i Azure-portalen i det virtuella nätverket. Den här inställningen tjänstredundans DNS.

### <a name="DomainAccounts"></a> Konfigurera domänkontona

I nästa steg ska konfigurera du Active Directory-konton. I följande tabell visas konton:

| |Kontot för installation<br/> |sqlserver-0 <br/>Konto för SQL Server och SQL Agent-tjänsten |sqlserver-1<br/>Konto för SQL Server och SQL Agent-tjänsten
| --- | --- | --- | ---
|**Förnamn** |Installera |SQLSvc1 | SQLSvc2
|**Användare SamAccountName** |Installera |SQLSvc1 | SQLSvc2

Använd följande steg för att skapa varje konto.

1. Logga in på den **ad-primary-dc** dator.
2. I **Serverhanteraren**väljer **verktyg**, och klicka sedan på **Active Directory Administrationscenter**.   
3. Välj **corp (lokal)** i den vänstra rutan.
4. Till höger **uppgifter** väljer **New**, och klicka sedan på **användaren**.
   ![Active Directory Administrative Center](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Ange ett komplext lösenord för varje konto.<br/> För icke-produktionsmiljöer, ange användarkontot aldrig upphör att gälla.

5. Klicka på **OK** att skapa användaren.
6. Upprepa föregående steg för var och en av de tre kontona.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Bevilja behörighet att kontot för installation
1. I den **Active Directory Administrationscenter**väljer **corp (lokal)** i den vänstra rutan. I den högra **uppgifter** fönstret klickar du på **egenskaper**.

    ![CORP-användaregenskaper](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Välj **tillägg**, och klicka sedan på den **Avancerat** knappen på den **Security** fliken.
3. I den **avancerade säkerhetsinställningar för corp** dialogrutan klickar du på **Lägg till**.
4. Klicka på **Välj ett huvudkonto**, Sök efter **CORP\Install**, och klicka sedan på **OK**.
5. Välj den **läsa alla egenskaper** markerar du kryssrutan.

6. Välj den **skapa datorobjekt** markerar du kryssrutan.

     ![Corp användarbehörigheter](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Klicka på **OK**, och klicka sedan på **OK** igen. Stäng den **corp** egenskapsfönstret.

Nu när du har konfigurerat Active Directory och användarobjekt, skapa två SQL Server-datorer och en vittnesserver-dator. Sedan ansluta alla tre till domänen.

## <a name="create-sql-server-vms"></a>Skapa SQL Server-datorer

Skapa tre ytterligare virtuella datorer. Lösningen kräver två virtuella datorer med SQL Server-instanser. En tredje virtuell dator fungerar som ett vittne. Windows Server 2016 kan använda en [cloud vittne](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), men för att få konsekvens med tidigare operativsystem det här dokumentet använder en virtuell dator för ett vittne.  

Överväg följande designbeslut innan du fortsätter.

* **Storage – Azure Managed Disks**

   Använd Azure Managed Disks för lagring för virtuell dator. Microsoft rekommenderar Managed Disks för SQL Server-datorer. Managed Disks hanterar lagring i bakgrunden. När virtuella datorer med Managed Disks finns i samma tillgänglighetsuppsättning, distribuerar Azure dessutom lagringsresurser för att tillhandahålla rätt redundans. Mer information finns i [Översikt över Azure Managed Disks](../managed-disks-overview.md). Mer information om hanterade diskar i en tillgänglighetsuppsättning finns i [Använd hanterade diskar för virtuella datorer i en tillgänglighetsuppsättning](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Nätverk – privata IP-adresser i produktion**

   Den här självstudien använder offentliga IP-adresser för de virtuella datorerna. En offentlig IP-adress tillåter anslutning direkt till den virtuella datorn via internet – det gör konfigurationssteg enklare. Microsoft rekommenderar endast privata IP-adresser för att minska sårbarhet för SQL Server-instansen VM-resurs i produktionsmiljöer.

### <a name="create-and-configure-the-sql-server-vms"></a>Skapa och konfigurera SQL Server-datorer
Sedan skapar du tre virtuella datorer – två SQL Server-datorer och en virtuell dator för en ny klusternod. Om du vill skapa var och en av de virtuella datorerna går du tillbaka till den **SQL-HA-RG** resursgruppen, klickar du på **Lägg till**, Sök efter lämplig galleri-objekt, klickar du på **VM**, och klicka sedan på  **Från galleriet**. Använd informationen i följande tabell för att skapa de virtuella datorerna:


| Sidan | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Välj lämplig galleri-objekt |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise på Windows Server 2016** |**SQL Server 2016 SP1 Enterprise på Windows Server 2016** |
| Konfiguration av virtuell dator **grunderna** |**Namn på** = kluster fsw<br/>**Användarnamn** = DomainAdmin<br/>**Lösenord** = Contoso! 0000<br/>**Prenumeration** = din prenumeration<br/>**Resursgrupp** = SQL-HA-RG<br/>**Plats** = din azure-plats |**Namn på** = sqlserver-0<br/>**Användarnamn** = DomainAdmin<br/>**Lösenord** = Contoso! 0000<br/>**Prenumeration** = din prenumeration<br/>**Resursgrupp** = SQL-HA-RG<br/>**Plats** = din azure-plats |**Namn på** = sqlserver-1<br/>**Användarnamn** = DomainAdmin<br/>**Lösenord** = Contoso! 0000<br/>**Prenumeration** = din prenumeration<br/>**Resursgrupp** = SQL-HA-RG<br/>**Plats** = din azure-plats |
| Konfiguration av virtuell dator **storlek** |**STORLEK** = DS1\_V2 (1 vCPU, 3,5 GB) |**STORLEK** = DS2\_V2 (2 virtuella processorer, 7 GB)</br>Storleken måste ha stöd för SSD-lagring (Premium-diskstöd. )) |**STORLEK** = DS2\_V2 (2 virtuella processorer, 7 GB) |
| Konfiguration av virtuell dator **inställningar** |**Storage**: Använda hanterade diskar.<br/>**Virtuellt nätverk** = autoHAVNET<br/>**Undernät** = sqlsubnet(10.1.1.0/24)<br/>**Offentlig IP-adress** skapas automatiskt.<br/>**Nätverkssäkerhetsgrupp** = None<br/>**Övervakning diagnostik** = aktiverat<br/>**Diagnostiklagringskonto** = Använd en automatiskt genererad storage-konto<br/>**Tillgänglighetsuppsättning** = sqlAvailabilitySet<br/> |**Storage**: Använda hanterade diskar.<br/>**Virtuellt nätverk** = autoHAVNET<br/>**Undernät** = sqlsubnet(10.1.1.0/24)<br/>**Offentlig IP-adress** skapas automatiskt.<br/>**Nätverkssäkerhetsgrupp** = None<br/>**Övervakning diagnostik** = aktiverat<br/>**Diagnostiklagringskonto** = Använd en automatiskt genererad storage-konto<br/>**Tillgänglighetsuppsättning** = sqlAvailabilitySet<br/> |**Storage**: Använda hanterade diskar.<br/>**Virtuellt nätverk** = autoHAVNET<br/>**Undernät** = sqlsubnet(10.1.1.0/24)<br/>**Offentlig IP-adress** skapas automatiskt.<br/>**Nätverkssäkerhetsgrupp** = None<br/>**Övervakning diagnostik** = aktiverat<br/>**Diagnostiklagringskonto** = Använd en automatiskt genererad storage-konto<br/>**Tillgänglighetsuppsättning** = sqlAvailabilitySet<br/> |
| Konfiguration av virtuell dator **SQL Server-inställningar** |Inte tillämpligt |**SQL-anslutning** = privat (inom virtuellt nätverk)<br/>**Port** = 1433<br/>**SQL-autentisering** = inaktivera<br/>**Lagringskonfiguration** = Allmänt<br/>**Automatisk uppdatering** = söndag 2:00<br/>**Automatisk säkerhetskopiering** = inaktiverad</br>**Azure Key Vault-integrering** = inaktiverad |**SQL-anslutning** = privat (inom virtuellt nätverk)<br/>**Port** = 1433<br/>**SQL-autentisering** = inaktivera<br/>**Lagringskonfiguration** = Allmänt<br/>**Automatisk uppdatering** = söndag 2:00<br/>**Automatisk säkerhetskopiering** = inaktiverad</br>**Azure Key Vault-integrering** = inaktiverad |

<br/>

> [!NOTE]
> Storlekar för datorer som föreslås här är avsedda för att testa Tillgänglighetsgrupper i Azure virtuella datorer. För bästa prestanda på produktionsarbetsbelastningar finns rekommendationer för SQL Server-datorstorlek och konfiguration i [prestandametodtips för SQL Server i Azure virtual machines](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

När de tre virtuella datorerna är helt etablerad, måste du koppla dem till den **corp.contoso.com** domän och bevilja CORP\Install administratörsbehörighet på datorerna.

### <a name="joinDomain"></a>Anslut servrarna till domänen

Nu är du kunna ansluta till de virtuella datorerna ska **corp.contoso.com**. Utför följande steg för både SQL Server-datorer och vittne för servern:

1. Fjärransluta till den virtuella datorn med **BUILTIN\DomainAdmin**.
2. I **Serverhanteraren**, klickar du på **lokal Server**.
3. Klicka på den **arbetsgrupp** länk.
4. I den **datornamn** klickar du på **ändra**.
5. Välj den **domän** markerar du kryssrutan och skriv **corp.contoso.com** i textrutan. Klicka på **OK**.
6. I den **Windows Security** popup-fönstret dialogrutan Ange autentiseringsuppgifterna för standard-domänadministratörskonto (**CORP\DomainAdmin**) och lösenordet (**Contoso! 0000**) .
7. När du ser meddelandet ”Välkommen till domänen corp.contoso.com” klickar du på **OK**.
8. Klicka på **Stäng**, och klicka sedan på **starta om nu** i popup-dialogruta.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Lägg till Corp\Install användaren som administratör på varje VM-kluster

När varje virtuell dator startas om som en medlem i domänen, lägga till **CORP\Install** som en medlem i gruppen lokala administratörer.

1. Vänta tills den virtuella datorn har startats om och starta RDP-filen igen från den primära domänkontrollanten för att logga in på **sqlserver-0** med hjälp av den **CORP\DomainAdmin** konto.
   >[!TIP]
   >Kontrollera att du loggar in med domänadministratörskontot. I föregående steg använde BYGGER IN administratörskontot. Nu när servern är i domänen kan du använda domänkonto. Ange i RDP-session *domän*\\*användarnamn*.

2. I **Serverhanteraren**väljer **verktyg**, och klicka sedan på **Datorhantering**.
3. I den **Datorhantering** fönstret expanderar **lokala användare och grupper**, och välj sedan **grupper**.
4. Dubbelklicka på den **administratörer** grupp.
5. I den **egenskaper för administratörer** dialogrutan klickar du på den **Lägg till** knappen.
6. Ange användaren **CORP\Install**, och klicka sedan på **OK**.
7. Klicka på **OK** att Stäng den **Administratörsegenskaper** dialogrutan.
8. Upprepa föregående steg på **sqlserver-1** och **kluster fsw**.

### <a name="setServiceAccount"></a>Ange SQL Server service-konton

Ange SQL Server-tjänstkontot på varje SQL Server-dator. Använd de konton som du skapade när du har konfigurerat domänkontona.

1. Öppna **SQL Server Configuration Manager**.
2. Högerklicka på SQL Server-tjänsten och klicka sedan på **egenskaper**.
3. Ange konto och lösenord.
4. Upprepa dessa steg på den andra SQL Server-VM.  

Varje SQL Server-dator måste köras som ett domänkonto för SQL Server-Tillgänglighetsgrupper.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Skapa en inloggning på varje SQL Server-VM för kontot för installation

Använda kontot för installation (CORP\install) för att konfigurera tillgänglighetsgruppen. Det här kontot måste vara medlem i den **sysadmin** fasta serverrollen på varje SQL Server-VM. Följande steg skapar en inloggning för kontot för installation:

1. Ansluta till servern via Remote Desktop Protocol (RDP) med hjälp av den  *\<MachineName\>\DomainAdmin* konto.

1. Öppna SQL Server Management Studio och Anslut till den lokala instansen av SQL Server.

1. I **Object Explorer**, klickar du på **Security**.

1. Högerklicka på **inloggningar**. Klicka på **ny inloggning**.

1. I **inloggning – ny**, klickar du på **Search**.

1. Klicka på **platser**.

1. Ange autentiseringsuppgifter som domänadministratör nätverk.

1. Använda kontot för installation.

1. Ange inloggning ska vara medlem i den **sysadmin** fasta serverrollen.

1. Klicka på **OK**.

Upprepa föregående steg på den andra SQL Server-VM.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Lägga till funktioner för redundanskluster i både SQL Server-datorer

Om du vill lägga till funktioner för redundanskluster, gör du följande steg på både SQL Server-datorer:

1. Ansluta till SQL Server-dator via Remote Desktop Protocol (RDP) med hjälp av den *CORP\install* konto. Öppna **Serverhanterarens instrumentpanel**.
2. Klicka på den **Lägg till roller och funktioner** länk på instrumentpanelen.

    ![Serverhanteraren – Lägg till roller](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Välj **nästa** tills du kommer till den **serverfunktioner** avsnittet.
4. I **funktioner**väljer **Redundansklustring**.
5. Lägg till eventuella ytterligare nödvändiga funktioner.
6. Klicka på **installera** att lägga till funktionerna.

Upprepa stegen på den andra SQL Server-VM.

  >[!NOTE]
  > Det här steget, tillsammans med faktiskt ansluter till SQL Server-datorer i failover-kluster kan nu automatiseras med [Azure SQL-dator med CLI](virtual-machines-windows-sql-availability-group-cli.md) och [Azure-Snabbstartsmallar](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> Konfigurera brandväggen på varje SQL Server VM

Lösningen kräver följande TCP-portar är öppna i brandväggen:

- **SQLServer-dator**:<br/>
   Port 1433 för en standardinstans av SQL Server.
- **Azure belastningsutjämnaravsökning:**<br/>
   Alla tillgängliga portar. Exemplen använder ofta 59999.
- **Slutpunkten för databasspegling:** <br/>
   Alla tillgängliga portar. Exemplen använder ofta 5022.

Brandväggsportar måste vara öppna på både SQL Server-datorer.

Metoden för att öppna portarna som är beroende av brandvägg som du använder. Nästa avsnitt beskrivs hur du öppnar portarna i Windows-brandväggen. Öppna portarna som krävs på varje SQL Server-datorer.

### <a name="open-a-tcp-port-in-the-firewall"></a>Öppna en TCP-port i brandväggen

1. På den första SQL-servern **starta** skärmen, starta **Windows-brandväggen med avancerad säkerhet**.
2. I det vänstra fönstret, Välj **regler för inkommande trafik**. I den högra rutan klickar du på **ny regel**.
3. För **regeltyp**, Välj **Port**.
4. Port, ange **TCP** och anger rätt portnummer. Se följande exempel:

   ![SQL-brandvägg](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Klicka på **Nästa**.
6. På den **åtgärd** behåller **Tillåt anslutningen** markerad och klicka sedan på **nästa**.
7. På den **profil** sidan, accepterar du standardinställningarna och klicka sedan på **nästa**.
8. På den **namn** anger du ett namn för regeln (till exempel **Azure LB Probe**) i den **namn** textrutan och klicka sedan på **Slutför**.

Upprepa dessa steg på den andra SQL Server-dator.

## <a name="configure-system-account-permissions"></a>Konfigurera behörighet för systemkonto

Om du vill skapa ett konto för system-kontot och bevilja behörighet, utför du följande steg på varje SQL Server-instans:

1. Skapa ett konto för `[NT AUTHORITY\SYSTEM]` på varje SQL Server-instansen. Följande skript skapar det här kontot:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Bevilja följande behörigheter till `[NT AUTHORITY\SYSTEM]` på varje SQL Server-instans:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Följande skript beviljar följande behörigheter:

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
