---
title: "Grupper tillgänglighet för SQL Server - virtuella datorer i Azure - förutsättningar för | Microsoft Docs"
description: "Den här kursen visar hur du konfigurerar kraven för att skapa en SQL Server Always On availability group på virtuella Azure-datorer."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/09/2017
ms.author: mikeray
ms.openlocfilehash: 3d508877928e033f24dae62c1042745ea7250033
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="complete-the-prerequisites-for-creating-always-on-availability-groups-on-azure-virtual-machines"></a>Uppfylla förutsättningar för att skapa Always On-Tillgänglighetsgrupper på virtuella Azure-datorer

Den här kursen visar hur du utför de nödvändiga förutsättningarna för att skapa en [SQL Server Always On-tillgänglighetsgruppen på virtuella Azure-datorer (VM)](virtual-machines-windows-portal-sql-availability-group-tutorial.md). När du är klar kraven har en domänkontrollant, två virtuella SQL Server-datorer och ett vittnesserver i en enskild resursgrupp.

**Tid uppskattning**: Det kan ta ett par timmar att slutföra förutsättningarna. Mycket av den här tiden går åt för att skapa virtuella datorer.

Följande diagram illustrerar hur du skapar under kursen.

![Tillgänglighetsgruppen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Läs dokumentationen om tillgänglighet

Den här kursen förutsätter att du har en grundläggande förståelse för SQL Server Always On-Tillgänglighetsgrupper. Om du inte är bekant med den här tekniken finns [översikt över alltid på Tillgänglighetsgrupper (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Skapa ett Azure-konto
Du behöver ett Azure-konto. Du kan [öppna ett kostnadsfritt Azure-konto](/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
1. Logga in på [Azure Portal](http://portal.azure.com).
2. Klicka på  **+**  att skapa ett nytt objekt i portalen.

   ![Nytt objekt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Typen **resursgruppen** i den **Marketplace** sökfönstret.

   ![Resursgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Klicka på **resursgruppen**.
5. Klicka på **Skapa**.
6. På den **resursgruppen** bladet under **resursgruppens namn**, Skriv ett namn för resursgruppen. Skriv till exempel **sql-hög tillgänglighet-rg**.
7. Kontrollera att prenumerationen är Azure-prenumeration som du vill skapa tillgänglighetsgruppen i om du har flera Azure-prenumerationer.
8. Välj en plats. Platsen är Azure-regionen där du vill skapa tillgänglighetsgruppen. Den här självstudiekursen kommer vi att skapa alla resurser i en Azure-plats.
9. Kontrollera att **fäst på instrumentpanelen** är markerad. Den här valfria inställningen placeras en genväg för resursgruppen på instrumentpanelen för Azure-portalen.

   ![Resursgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Klicka på **skapa** att skapa resursgruppen.

Azure skapar resursgruppen och PIN-koder som en genväg till resursgruppen i portalen.

## <a name="create-the-network-and-subnets"></a>Skapa nätverk och undernät
Nästa steg är att skapa nätverk och undernät i Azure-resursgrupp.

Lösningen använder ett virtuellt nätverk med två undernät. Den [översikt över virtuella nätverk](../../../virtual-network/virtual-networks-overview.md) innehåller mer information om nätverk i Azure.

För att skapa det virtuella nätverket:

1. I Azure-portalen i resursgruppen, klickar du på **+ Lägg till**. Azure öppnas den **allt** bladet.

   ![Nytt objekt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Sök efter **virtuellt nätverk**.

     ![Sökningen virtuellt nätverk](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Klicka på **för virtuella nätverk**.
4. På den **för virtuella nätverk** bladet, klickar du på den **Resource Manager** distributionsmodell och klicka sedan på **skapa**.

    I följande tabell visas inställningarna för det virtuella nätverket:

   | **Fält** | Värde |
   | --- | --- |
   | **Namn** |autoHAVNET |
   | **Adressutrymme** |10.33.0.0/24 |
   | **Namn på undernät** |Admin |
   | **Adressintervall för undernätet** |10.33.0.0/29 |
   | **Prenumeration** |Ange den prenumeration som du tänker använda. **Prenumerationen** är tom om du bara har en prenumeration. |
   | **Resursgrupp** |Välj **använda befintliga** och välj namnet på resursgruppen. |
   | **Plats** |Ange platsen för Azure. |

   Adress adressutrymmet och undernätsegenskaperna adressintervallet kan skilja sig från tabellen. Beroende på din prenumeration föreslår portalen en tillgängliga adressutrymmet och motsvarande adressintervallet i undernätet. Om det finns inte tillräckligt med adressutrymme Använd en annan prenumeration.

   I exemplet används undernätnamnet **Admin**. Det här undernätet är för domänkontrollanter.

5. Klicka på **Skapa**.

   ![Konfigurera det virtuella nätverket](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

Azure återgår till portalens instrumentpanel och meddelar dig när det nya nätverket har skapats.

### <a name="create-a-second-subnet"></a>Skapa ett andra undernät
Nytt virtuellt nätverk har ett undernät, med namnet **Admin**. Domänkontrollanter använder det här undernätet. Använda SQL Server-datorer ett andra undernät med namnet **SQL**. Konfigurera det här undernätet:

1. På instrumentpanelen, klickar du på den resursgrupp som du skapade **SQL-hög tillgänglighet-RG**. Leta upp nätverket i resursgruppen under **resurser**.

    Om **SQL-hög tillgänglighet-RG** inte visas, hitta genom att klicka på **resursgrupper** och filtrering av resursgruppens namn.
2. Klicka på **autoHAVNET** i listan över resurser. Azure öppnas bladet nätverket konfiguration.
3. På den **autoHAVNET** virtuellt nätverksblad under **inställningar** , klickar du på **undernät**.

    Observera att det undernät som du redan skapat.

   ![Konfigurera det virtuella nätverket](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Skapa en andra undernät. Klicka på **+ undernät**.
6. På den **Lägg till undernät** bladet konfigurera undernätet genom att skriva **sqlsubnet** under **namn**. Azure automatiskt anger en giltig **adressintervall**. Kontrollera att detta adressintervall har minst 10 adresser i den. Du kan behöva flera adresser i en produktionsmiljö.
7. Klicka på **OK**.

    ![Konfigurera det virtuella nätverket](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

I följande tabell sammanfattas inställningarna för nätverk:

| **Fält** | Värde |
| --- | --- |
| **Namn** |**autoHAVNET** |
| **Adressutrymme** |Detta värde beror på tillgängliga adressutrymmen i din prenumeration. Ett vanligt värde är 10.0.0.0/16. |
| **Namn på undernät** |**Admin** |
| **Adressintervall för undernätet** |Detta värde beror på tillgängliga adressintervallen i din prenumeration. Ett vanligt värde är 10.0.0.0/24. |
| **Namn på undernät** |**sqlsubnet** |
| **Adressintervall för undernätet** |Detta värde beror på tillgängliga adressintervallen i din prenumeration. Ett vanligt värde är 10.0.1.0/24. |
| **Prenumeration** |Ange den prenumeration som du tänker använda. |
| **Resursgrupp** |**SQL-HÖG TILLGÄNGLIGHET-RG** |
| **Plats** |Ange samma plats som du valde för resursgruppen. |

## <a name="create-availability-sets"></a>Skapa tillgänglighetsuppsättningar

Innan du skapar virtuella datorer måste du skapa tillgänglighetsuppsättningar. Tillgänglighetsuppsättningar minska avbrottstiden för planerat eller oplanerat underhållshändelser. En Azure tillgänglighetsuppsättning är en logisk grupp med resurser som Azure placerar fysiska feldomäner och update-domäner. En feldomän säkerställer att medlemmarna i tillgänglighetsuppsättningen separat power och nätverksresurser. En uppdateringsdomän säkerställer att medlemmar i tillgänglighetsuppsättningen inte återspeglas för underhåll på samma gång. Mer information finns i [hantera tillgängligheten för virtuella datorer](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Du behöver två tillgänglighetsuppsättningar. En är för domänkontrollanter. Andra är för SQL Server-datorer.

Om du vill skapa en tillgänglighetsuppsättning, gå till resursgruppen och klicka **Lägg till**. Filtrera resultaten genom att skriva **tillgänglighetsuppsättning**. Klicka på **Tillgänglighetsuppsättning** i resultaten och klickar sedan på **skapa**.

Konfigurera två tillgänglighetsuppsättningar enligt parametrarna i följande tabell:

| **Fält** | Domain controller tillgänglighetsuppsättning | Tillgänglighetsuppsättningen för SQL Server |
| --- | --- | --- |
| **Namn** |adavailabilityset |sqlavailabilityset |
| **Resursgrupp** |SQL-HÖG TILLGÄNGLIGHET-RG |SQL-HÖG TILLGÄNGLIGHET-RG |
| **Feldomäner** |3 |3 |
| **Uppdatera domäner** |5 |3 |

När du har skapat tillgänglighetsuppsättningarna tillbaka till resursgruppen i Azure-portalen.

## <a name="create-domain-controllers"></a>Skapa domänkontrollanter
När du har skapat nätverket, undernät, tillgänglighetsuppsättningar och en Internetriktade belastningsutjämnare, är du redo att skapa de virtuella datorerna för domänkontrollanter.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Skapa virtuella datorer för domänkontrollanter
Om du vill skapa och konfigurera domänkontrollanterna kan återgå till den **SQL-hög tillgänglighet-RG** resursgruppen.

1. Klicka på **Lägg till**. Den **allt** blad öppnas.
2. Typen **Windows Server 2016 Datacenter**.
3. Klicka på **Windows Server 2016 Datacenter**. I den **Windows Server 2016 Datacenter** bladet, kontrollera att distributionsmodellen är **Resource Manager**, och klicka sedan på **skapa**. Azure öppnas den **Skapa virtuell dator** bladet.

Upprepa föregående steg för att skapa två virtuella datorer. Namn på två virtuella datorer:

* AD-primära-dc
* AD-sekundär-dc

  > [!NOTE]
  > Den **ad-sekundär-dc** virtuella datorn är valfritt att tillhandahålla hög tillgänglighet för Active Directory Domain Services.
  >
  >

I följande tabell visas inställningarna för dessa två datorer:

| **Fält** | Värde |
| --- | --- |
| **Namn** |Första domänkontrollant: *ad primär domänkontrollant*.</br>Andra domänkontrollanten *ad-sekundär-dc*. |
| **Typ av virtuell datordisk** |SSD |
| **Användarnamn** |DomainAdmin |
| **Lösenord** |Contoso! 0000 |
| **Prenumeration** |*Din prenumeration* |
| **Resursgrupp** |SQL-HÖG TILLGÄNGLIGHET-RG |
| **Plats** |*Din plats* |
| **Storlek** |DS1_V2 |
| **Storage** | **Använda hanterade diskar** - **Ja** |
| **Virtuellt nätverk** |autoHAVNET |
| **Undernät** |Admin |
| **Offentlig IP-adress** |*Samma namn som den virtuella datorn* |
| **Nätverkssäkerhetsgrupp** |*Samma namn som den virtuella datorn* |
| **Tillgänglighetsuppsättning** |adavailabilityset </br>**Fault domäner**: 2</br>**Uppdatera domäner**: 2|
| **Diagnostik** |Enabled |
| **Diagnostiklagringskonto** |*Skapas automatiskt* |

   >[!IMPORTANT]
   >Du kan bara placera en virtuell dator i en tillgänglighetsuppsättning när du skapar den. Du kan inte ändra tillgänglighetsuppsättning när en virtuell dator skapas. Se [hantera tillgängligheten för virtuella datorer](../manage-availability.md).

Azure skapar de virtuella datorerna.

När de virtuella datorerna har skapats kan konfigurera en domänkontrollant.

### <a name="configure-the-domain-controller"></a>Konfigurera en domänkontrollant
I följande steg ska du konfigurera den **ad primär domänkontrollant** maskin som en domänkontrollant för corp.contoso.com.

1. I portalen, öppna den **SQL-hög tillgänglighet-RG** resurs gruppen och välj den **ad primär domänkontrollant** datorn. På den **ad primär domänkontrollant** bladet, klickar du på **Anslut** att öppna en RDP-fil för fjärråtkomst till skrivbordet.

    ![Ansluta till en virtuell dator](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Logga in med ditt konfigurerade administratörskonto (**\DomainAdmin**) och lösenord (**Contoso! 0000**).
3. Som standard den **Serverhanteraren** instrumentpanelen ska visas.
4. Klicka på den **Lägg till roller och funktioner** länk på instrumentpanelen.

    ![Server Manager - Lägg till roller](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Välj **nästa** tills du kommer till den **serverroller** avsnitt.
6. Välj den **Active Directory Domain Services** och **DNS-Server** roller. När du uppmanas att lägga till eventuella ytterligare funktioner som krävs av rollerna.

   > [!NOTE]
   > Windows varnar dig om att det finns ingen statisk IP-adress. Om du testar konfigurationen klickar du på **Fortsätt**. Ange IP-adress till statisk i Azure-portalen för produktion scenarier eller [använda PowerShell för att ange statiska IP-adressen för den domain controller datorn](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Lägg till roller dialogrutan](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Klicka på **nästa** tills du når den **bekräftelse** avsnitt. Välj den **starta om målservern automatiskt om det behövs** kryssrutan.
8. Klicka på **Installera**.
9. När funktionerna är klar installerar du tillbaka till den **Serverhanteraren** instrumentpanelen.
10. Välj den nya **AD DS** alternativet i den vänstra rutan.
11. Klicka på den **mer** länk på den gula varning-fältet.

    ![Dialogrutan för AD DS på den virtuella datorn DNS-Server](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. I den **åtgärd** kolumnen i den **alla Server aktivitetsinformation** dialogrutan klickar du på **befordra servern till en domänkontrollant**.
13. I den **konfigurationsguiden Active Directory Domain Services**, använder du följande värden:

    | **Sidan** | Inställning |
    | --- | --- |
    | **Distributionskonfiguration** |**Lägg till en ny skog**<br/> **Rotdomännamn** = corp.contoso.com |
    | **Alternativ för domänkontrollant** |**DSRM-lösenordet** = Contoso! 0000<br/>**Bekräfta lösenord** = Contoso! 0000 |
14. Klicka på **nästa** gå igenom andra sidor i guiden. På den **Kravkontroll** kontrollerar du att du ser följande meddelande: **alla kravkontroller har lyckats**. Du kan granska alla meddelanden, men det är möjligt att fortsätta med installationen.
15. Klicka på **Installera**. Den **ad primär domänkontrollant** virtuella datorn startar om automatiskt.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Observera IP-adressen för den primära domänkontrollanten

Använd den primära domänkontrollanten för DNS. Observera controller IP-adressen för primär domän.

Ett sätt att hämta domänkontrollant IP-adressen för primär domän är via Azure-portalen.

1. Öppna resursgruppen på Azure-portalen.

2. Klicka på den primära domänkontrollanten.

3. Klicka på bladet primär domän controller **nätverksgränssnitt**.

![Nätverksgränssnitt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Observera den privata IP-adressen för den här servern.

### <a name="configure-the-virtual-network-dns"></a>Konfigurera det virtuella nätverket DNS
När du skapar den första domänkontrollanten och aktivera DNS på den första servern, konfigurera det virtuella nätverket för att använda den här servern för DNS.

1. Klicka på det virtuella nätverket i Azure-portalen.

2. Under **inställningar**, klickar du på **DNS-Server**.

3. Klicka på **anpassade**, och Skriv privata IP-adressen för den primära domänkontrollanten.

4. Klicka på **Spara**.

### <a name="configure-the-second-domain-controller"></a>Konfigurera andra domänkontrollanter
Du kan konfigurera andra domänkontrollanten när den primära domänkontrollanten startar om. Det här valfria steget är för hög tillgänglighet. Följ dessa steg för att konfigurera andra domänkontrollanten:

1. I portalen, öppna den **SQL-hög tillgänglighet-RG** resurs gruppen och välj den **ad-sekundär-dc** datorn. På den **ad-sekundär-dc** bladet, klickar du på **Anslut** att öppna en RDP-fil för fjärråtkomst till skrivbordet.
2. Logga in på den virtuella datorn med hjälp av ditt konfigurerade administratörskonto (**BUILTIN\DomainAdmin**) och lösenord (**Contoso! 0000**).
3. Ändra önskad DNS-serveradress till adressen för domänkontrollanten.
4. I **nätverks- och delningscenter**, klickar du på nätverksgränssnittet.
   ![Nätverksgränssnitt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Klicka på **Egenskaper**.
6. Välj **Internet Protocol Version 4 (TCP/IPv4)** och på **egenskaper**.
7. Välj **Använd följande DNS-serveradresser** och ange adressen till den primära domänkontrollanten i **önskad DNS-server**.
8. Klicka på **OK**, och sedan **Stäng** att genomföra ändringarna. Du är nu kunna ansluta till den virtuella datorn till **corp.contoso.com**.

   >[!IMPORTANT]
   >Om du förlorar anslutningen till din Fjärrskrivbord när du har ändrat inställningen för DNS-gå till Azure-portalen och starta om den virtuella datorn.

9. Fjärrskrivbord till den sekundära domänkontrollanten öppna **instrumentpanelen Serverhanteraren**.
10. Klicka på den **Lägg till roller och funktioner** länk på instrumentpanelen.

    ![Server Manager - Lägg till roller](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Välj **nästa** tills du kommer till den **serverroller** avsnitt.
12. Välj den **Active Directory Domain Services** och **DNS-Server** roller. När du uppmanas att lägga till eventuella ytterligare funktioner som krävs av rollerna.
13. När funktionerna är klar installerar du tillbaka till den **Serverhanteraren** instrumentpanelen.
14. Välj den nya **AD DS** alternativet i den vänstra rutan.
15. Klicka på den **mer** länk på den gula varning-fältet.
16. I den **åtgärd** kolumnen i den **alla Server aktivitetsinformation** dialogrutan klickar du på **befordra servern till en domänkontrollant**.
17. Under **distributionskonfigurationen**väljer **lägger till en domänkontrollant i en befintlig domän**.
   ![Distributionskonfiguration](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Klicka på **Välj**.
19. Ansluta med hjälp av administratörskontot (**CORP. Contoso.COM\domainadmin**) och lösenord (**Contoso! 0000**).
20. I **Välj en domän från skogen**, klickar du på din domän och klicka sedan på **OK**.
21. I **alternativ för domänkontrollant**, Använd standardvärdena och ange DSRM-lösenordet.

   >[!NOTE]
   >Den **DNS-alternativ** sida kan varna dig att det inte går att skapa en delegering för den här DNS-servern. Du kan ignorera den här varningen i icke-produktionsmiljöer.
22. Klicka på **nästa** tills dialogrutan når den **krav** kontrollera. Klicka på **Installera**.

Starta om servern när servern har slutförts ändringar i konfigurationen.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Lägga till privata IP-adressen till den andra domänkontrollanten till VPN-DNS-Server

Ändra DNS-servern med IP-adressen för den sekundära domänkontrollanten i Azure-portalen under virtuellt nätverk. Detta gör att DNS-tjänsten redundans.

### <a name=DomainAccounts></a>Konfigurera domänkontona

I nästa steg ska konfigurera du Active Directory-konton. I följande tabell visas kontona:

| |Kontot för installation<br/> |SQLServer-0 <br/>Konto för SQL Server och SQL Agent-tjänsten |SQLServer-1<br/>Konto för SQL Server och SQL Agent-tjänsten
| --- | --- | --- | ---
|**Förnamn** |Installera |SQLSvc1 | SQLSvc2
|**Användare SamAccountName** |Installera |SQLSvc1 | SQLSvc2

Använd följande steg för att skapa varje konto.

1. Logga in på den **ad primär domänkontrollant** datorn.
2. I **Serverhanteraren**väljer **verktyg**, och klicka sedan på **Active Directory Administrationscenter**.   
3. Välj **corp (lokal)** i den vänstra rutan.
4. Till höger **uppgifter** väljer **ny**, och klicka sedan på **användaren**.
   ![Active Directory Administrationscenter](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Ange ett komplext lösenord för varje konto.<br/> För icke-produktionsmiljöer, ange användarkontot som ska aldrig går ut.

5. Klicka på **OK** att skapa användaren.
6. Upprepa föregående steg för var och en av tre konton.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Bevilja behörighet till kontot för installation av
1. I den **Active Directory Administrationscenter**väljer **corp (lokal)** i den vänstra rutan. I den högra **uppgifter** rutan klickar du på **egenskaper**.

    ![Egenskaper för CORP-användare](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Välj **tillägg**, och klicka sedan på den **Avancerat** knappen på den **säkerhet** fliken.
3. I den **avancerade säkerhetsinställningar för corp** dialogrutan klickar du på **Lägg till**.
4. Klicka på **Välj en huvudansvarig**, söka efter **CORP\Install**, och klicka sedan på **OK**.
5. Välj den **läsa alla egenskaper** kryssrutan.

6. Välj den **skapa datorobjekt** kryssrutan.

     ![Corp användarbehörighet](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Klicka på **OK**, och klicka sedan på **OK** igen. Stäng den **corp** egenskapsfönstret.

Nu när du har konfigurerat Active Directory och användarobjekt, skapa två virtuella SQL Server-datorer och en vittnesserver som VM. Ansluta alla tre till domänen.

## <a name="create-sql-server-vms"></a>Skapa virtuella SQL Server-datorer

Skapa tre ytterligare virtuella datorer. Lösningen kräver två virtuella datorer med SQL Server-instanser. En tredje virtuell dator fungerar som ett vittne. Windows Server 2016 kan använda en [molnet vittne](http://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), men adressöverensstämmelse tidigare operativsystem för det här dokumentet använder en virtuell dator för ett vittne.  

Överväg följande beslut deisign innan du fortsätter.

* **Lagring – Azure hanterade diskar**

   Använd Azure hanterade diskar för lagring för virtuell dator. Microsoft rekommenderar hanterade diskar för virtuella datorer för SQL Server. Managed Disks hanterar lagring i bakgrunden. När virtuella datorer med Managed Disks finns i samma tillgänglighetsuppsättning, distribuerar Azure dessutom lagringsresurser för att tillhandahålla rätt redundans. Mer information finns i [Översikt över Azure Managed Disks](../managed-disks-overview.md). Närmare information om hanterade diskar i en tillgänglighetsuppsättning, se [använda hanterade diskar för virtuella datorer i en tillgänglighetsuppsättning](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Nätverk - privata IP-adresser i produktion**

   Den här kursen använder offentliga IP-adresser för de virtuella datorerna. Detta gör att fjärranslutning direkt till den virtuella datorn via internet - det underlättar konfigurationssteg. Microsoft rekommenderar endast privata IP-adresser i produktionsmiljöer, för att minska sårbarhet av SQL Server-instansen Virtuella datorresursen.

### <a name="create-and-configure-the-sql-server-vms"></a>Skapa och konfigurera SQL Server-datorer
Skapa därefter tre virtuella datorer – två virtuella SQL Server-datorer och en virtuell dator för en ny klusternod. Om du vill skapa var och en av de virtuella datorerna, gå tillbaka till den **SQL-hög tillgänglighet-RG** resursgrupp, klickar du på **Lägg till**, Sök efter lämplig galleriobjektet, klicka på **virtuella**, och klicka sedan på **från galleriet**. Använd informationen i följande tabell för att skapa de virtuella datorerna:


| Sidan | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Välj lämplig galleriobjektet |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise på Windows Server 2016** |**SQL Server 2016 SP1 Enterprise på Windows Server 2016** |
| Konfiguration av virtuell dator **grunderna** |**Namnet** = klustret fsw<br/>**Användarnamnet** = DomainAdmin<br/>**Lösenordet** = Contoso! 0000<br/>**Prenumerationen** = din prenumeration<br/>**Resursgruppen** = SQL-hög tillgänglighet-RG<br/>**Plats** = azure-plats |**Namnet** = sqlserver-0<br/>**Användarnamnet** = DomainAdmin<br/>**Lösenordet** = Contoso! 0000<br/>**Prenumerationen** = din prenumeration<br/>**Resursgruppen** = SQL-hög tillgänglighet-RG<br/>**Plats** = azure-plats |**Namnet** = sqlserver-1<br/>**Användarnamnet** = DomainAdmin<br/>**Lösenordet** = Contoso! 0000<br/>**Prenumerationen** = din prenumeration<br/>**Resursgruppen** = SQL-hög tillgänglighet-RG<br/>**Plats** = azure-plats |
| Konfiguration av virtuell dator **storlek** |**STORLEK** = DS1\_V2 (1 kärna, 3.5 GB) |**STORLEK** = DS2\_V2 (2 kärnor, 7 GB)</br>Storleken måste ha stöd för SSD-lagring (stöd för Premium-diskar. )) |**STORLEK** = DS2\_V2 (2 kärnor, 7 GB) |
| Konfiguration av virtuell dator **inställningar** |**Lagring**: Använd hanterade diskar.<br/>**Virtuellt nätverk** = autoHAVNET<br/>**Undernät** = sqlsubnet(10.1.1.0/24)<br/>**Offentliga IP-adressen** skapas automatiskt.<br/>**Nätverkssäkerhetsgruppen** = None<br/>**Övervaka diagnostik** = aktiverat<br/>**Diagnostiklagringskonto** = Använd en automatiskt genererad storage-konto<br/>**Tillgänglighetsuppsättningen** = sqlAvailabilitySet<br/> |**Lagring**: Använd hanterade diskar.<br/>**Virtuellt nätverk** = autoHAVNET<br/>**Undernät** = sqlsubnet(10.1.1.0/24)<br/>**Offentliga IP-adressen** skapas automatiskt.<br/>**Nätverkssäkerhetsgruppen** = None<br/>**Övervaka diagnostik** = aktiverat<br/>**Diagnostiklagringskonto** = Använd en automatiskt genererad storage-konto<br/>**Tillgänglighetsuppsättningen** = sqlAvailabilitySet<br/> |**Lagring**: Använd hanterade diskar.<br/>**Virtuellt nätverk** = autoHAVNET<br/>**Undernät** = sqlsubnet(10.1.1.0/24)<br/>**Offentliga IP-adressen** skapas automatiskt.<br/>**Nätverkssäkerhetsgruppen** = None<br/>**Övervaka diagnostik** = aktiverat<br/>**Diagnostiklagringskonto** = Använd en automatiskt genererad storage-konto<br/>**Tillgänglighetsuppsättningen** = sqlAvailabilitySet<br/> |
| Konfiguration av virtuell dator **SQL Server-inställningar** |Inte tillämpligt |**SQL-anslutningen** = privat (inom Virtual Network)<br/>**Port** = 1433<br/>**SQL-autentisering** = inaktivera<br/>**Lagringskonfigurationen** = Allmänt<br/>**Automatisk uppdatering** = söndag 2:00<br/>**Automatisk säkerhetskopiering** = inaktiverad</br>**Azure Key Vault-integrering** = inaktiverad |**SQL-anslutningen** = privat (inom Virtual Network)<br/>**Port** = 1433<br/>**SQL-autentisering** = inaktivera<br/>**Lagringskonfigurationen** = Allmänt<br/>**Automatisk uppdatering** = söndag 2:00<br/>**Automatisk säkerhetskopiering** = inaktiverad</br>**Azure Key Vault-integrering** = inaktiverad |

<br/>

> [!NOTE]
> Datorstorlekar som föreslås här är avsedda för att testa Tillgänglighetsgrupper i virtuella Azure-datorer. För bästa prestanda på produktionsarbetsbelastningar finns rekommendationer för SQL Server-datorstorlekar och konfigurationen i [prestandarelaterade Metodtips för SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

När de tre virtuella datorerna är helt etablerad, måste du koppla dem till den **corp.contoso.com** domän och bevilja CORP\Install administratörsbehörighet till datorer.

### <a name="joinDomain"></a>Anslut servrarna till domänen

Du är nu kunna ansluta till de virtuella datorerna till **corp.contoso.com**. Gör följande för både SQL Server-datorer och vittne för servern:

1. Fjärransluta till den virtuella datorn med **BUILTIN\DomainAdmin**.
2. I **Serverhanteraren**, klickar du på **lokal Server**.
3. Klicka på den **arbetsgrupp** länk.
4. I den **datornamn** klickar du på **ändra**.
5. Välj den **domän** kryssrutan och ange **corp.contoso.com** i textrutan. Klicka på **OK**.
6. I den **Windows-säkerhet** popup dialogrutan Ange autentiseringsuppgifter för standardkontot för administratör (**CORP\DomainAdmin**) och lösenordet (**Contoso! 0000**).
7. När meddelandet ”Välkommen till domänen corp.contoso.com”, klickar du på **OK**.
8. Klicka på **Stäng**, och klicka sedan på **starta om nu** i dialogrutan popup-fönster.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Lägg till Corp\Install användaren som administratör på varje kluster VM

Varje virtuell dator startar om som en medlem i domänen och lägga till **CORP\Install** som medlem i gruppen lokala administratörer.

1. Vänta tills den virtuella datorn har startats om och sedan starta RDP-filen igen från den primära domänkontrollanten för att logga in på **sqlserver-0** med hjälp av den **CORP\DomainAdmin** konto.
   >[!TIP]
   >Kontrollera att du loggar in med domänadministratörskontot. I de föregående stegen använde du SKAPAT i kontot. Nu när servern är i domänen, kan du använda domänkonto. Ange i RDP-session *domän*\\*användarnamn*.

2. I **Serverhanteraren**väljer **verktyg**, och klicka sedan på **Datorhantering**.
3. I den **Datorhantering** fönstret expanderar **lokala användare och grupper**, och välj sedan **grupper**.
4. Dubbelklicka på den **administratörer** grupp.
5. I den **egenskaper för administratörer** dialogrutan klickar du på den **Lägg till** knappen.
6. Ange användaren **CORP\Install**, och klicka sedan på **OK**.
7. Klicka på **OK** att stänga den **Administratörsegenskaper** dialogrutan.
8. Upprepa föregående steg på **sqlserver-1** och **klustret fsw**.

### <a name="setServiceAccount"></a>Ange SQL Server-tjänstkonton

Ange SQL Server-tjänstkontot på varje SQL Server-VM. Använd de konton som du skapade när du [konfigurerats-domänkonton](#DomainAccounts).

1. Öppna **SQL Server Configuration Manager**.
2. Högerklicka på SQL Server-tjänsten och klicka sedan på **egenskaper**.
3. Ange konto och lösenord.
4. Upprepa dessa steg på den andra SQL Server-VM.  

Varje virtuell dator med SQL Server måste köras som ett domänkonto för SQL Server-Tillgänglighetsgrupper.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Skapa en inloggning på varje SQL Server-VM för kontot för installation av

Använda kontot för installation av (CORP\install) för att konfigurera tillgänglighetsgruppen. Det här kontot måste vara medlem i den **sysadmin** fasta serverrollen på varje SQL Server-VM. Följande steg skapar en inloggning för kontot för installation:

1. Ansluta till servern via Remote Desktop Protocol (RDP) med hjälp av den  *\<MachineName\>\DomainAdmin* konto.

1. Öppna SQL Server Management Studio och Anslut till den lokala instansen av SQL Server.

1. I **Object Explorer**, klickar du på **säkerhet**.

1. Högerklicka på **inloggningar**. Klicka på **ny inloggning**.

1. I **inloggning - ny**, klickar du på **Sök**.

1. Klicka på **platser**.

1. Ange autentiseringsuppgifter som domänadministratör nätverk.

1. Använda kontot för installation.

1. Ange inloggning ska vara medlem i den **sysadmin** fasta serverrollen.

1. Klicka på **OK**.

Upprepa föregående steg på den andra SQL Server-VM.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Lägg till funktioner för redundanskluster i både SQL Server-datorer

Om du vill lägga till funktioner för redundanskluster, gör du följande på både SQL Server-datorer:

1. Ansluta till SQL Server-dator via Remote Desktop Protocol (RDP) med hjälp av *CORP\install* konto. Öppna **instrumentpanelen Serverhanteraren**.
2. Klicka på den **Lägg till roller och funktioner** länk på instrumentpanelen.

    ![Server Manager - Lägg till roller](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Välj **nästa** tills du kommer till den **serverfunktioner** avsnitt.
4. I **funktioner**väljer **redundanskluster**.
5. Lägg till eventuella ytterligare funktioner som krävs.
6. Klicka på **installera** att lägga till funktionerna.

Upprepa steg på den andra SQL Server-VM.

## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall">Konfigurera brandväggen på varje virtuell dator med SQL Server

Lösningen kräver följande TCP-portar vara öppna i brandväggen:

- **SQLServer VM**:<br/>
   Port 1433 för en standardinstans av SQL Server.
- **Azure belastningsutjämningsavsökning:**<br/>
   Alla tillgängliga portar. Exemplen använder ofta 59999.
- **Slutpunkten för databasspegling:** <br/>
   Alla tillgängliga portar. Exemplen använder ofta 5022.

Vilka brandväggsportar måste vara öppna på både SQL Server-datorer.

Metoden för att öppna portarna beror på brandvägg som du använder. I nästa avsnitt beskrivs hur du öppnar portar i Windows-brandväggen. Öppna portarna som krävs på alla virtuella din SQL Server-datorer.

### <a name="open-a-tcp-port-in-the-firewall"></a>Öppna en TCP-port i brandväggen

1. På den första SQL-servern **starta** skärmen, starta **Windows-brandväggen med avancerad säkerhet**.
2. I den vänstra rutan, Välj **regler för inkommande trafik**. I den högra rutan, klickar du på **ny regel**.
3. För **regeltyp**, Välj **Port**.
4. Port, ange **TCP** och ange rätt portnummer. Se följande exempel:

   ![SQL-brandvägg](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Klicka på **Nästa**.
6. På den **åtgärd** behåller **tillåter anslutningen** markerad och klicka sedan på **nästa**.
7. På den **profil** , accepterar du standardinställningarna och klicka sedan på **nästa**.
8. På den **namn** anger du ett namn för regeln (exempelvis **Azure LB avsökning**) i den **namn** textrutan och klicka sedan på **Slutför**.

Upprepa dessa steg på den andra SQL Server-VM.

## <a name="next-steps"></a>Nästa steg

* [Skapa en SQL Server Always On availability group på virtuella Azure-datorer](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
