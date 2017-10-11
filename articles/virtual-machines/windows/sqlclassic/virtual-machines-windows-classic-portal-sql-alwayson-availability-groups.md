---
title: "Konfigurera Always On-tillgänglighetsgrupp i Azure Virtual Machines (klassisk) | Microsoft Docs"
description: "Skapa en Always On-tillgänglighetsgrupp med Azure virtuella datorer. Den här kursen används främst användargränssnittet och verktyg i stället för skript."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 8d48a3d2-79f8-4ab0-9327-2f30ee0b2ff1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: b360fe9f28eeb9b10c82fce729165b1b572ac3c6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="configure-always-on-availability-group-in-azure-virtual-machines-classic"></a>Konfigurera Always On-tillgänglighetsgrupp i Azure Virtual Machines (klassisk)
> [!div class="op_single_selector"]
> * [Klassiska: UI](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klassiska: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Innan du börjar bör du överväga att du kan nu slutföra den här aktiviteten i Azure Resource Manager-modellen. Vi rekommenderar Azure Resource Manager-modellen för nya distributioner. Se [SQL Server Always On-Tillgänglighetsgrupper på Azure virtual machines](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT] 
> Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln förklarar hur du använder den klassiska distributionsmodellen. 

Om du vill slutföra uppgiften med Azure Resource Manager-modellen finns [SQL Server Always On-Tillgänglighetsgrupper på Azure virtual machines](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

Här slutpunkt till slutpunkt-självstudierna visar hur du implementerar Tillgänglighetsgrupper med hjälp av SQL Server Always On körs på Azure Virtual Machines.

I slutet av kursen består lösningen SQL Server alltid aktiverad i Azure av följande element:

* Ett virtuellt nätverk som innehåller flera undernät och en frontend och backend-undernät
* En domänkontrollant med en domän i Active Directory (AD Azure)
* Två virtuella datorer som kör SQL Server distribueras till backend-undernät och är ansluten till Azure AD-domänen
* Ett redundanskluster med tre noder med kvorummodellen Nodmajoritet
* En tillgänglighetsgrupp som har två replikerna med synkront genomförande av en tillgänglighetsdatabas

Följande bild är en grafisk representation av lösningen.

![Test lab-arkitektur för Tillgänglighetsgrupper i Azure](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Observera att detta är en konfiguration som möjligt. Exempelvis kan du minimera antalet virtuella datorer för en två-replik-tillgänglighetsgrupp. Den här konfigurationen sparar beräkningstimmar i Azure med hjälp av domänkontrollanten som filresursvittne för kvorum i ett kluster med två noder. Den här metoden minskar antalet virtuella datorer med ett från illustrerade konfigurationen.

Den här kursen förutsätter följande:

* Du har redan ett Azure-konto.
* Du vet redan hur du använder det grafiska Användargränssnittet i galleriet för virtuella datorer för att etablera en klassisk virtuell dator som kör SQL Server.
* Du har redan en god förståelse av Always On-Tillgänglighetsgrupper. Mer information finns i [Always On-Tillgänglighetsgrupper (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Om du vill använda Always On-Tillgänglighetsgrupper med SharePoint finns även [Konfigurera SQL Server 2012 Always On-Tillgänglighetsgrupper för SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).
> 
> 

## <a name="create-the-virtual-network-and-domain-controller-server"></a>Skapa den virtuella nätverks- och domain controller-servern
Du börjar med ett nytt Azure utvärderingskonto. När du har skapat ditt konto ska på startsidan i den klassiska Azure-portalen.

1. Klicka på den **ny** knappen längst till vänster i längst ned på sidan som visas i följande skärmbild.
   
    ![Klicka på ny i portalen](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)
2. Klicka på **nätverkstjänster** > **virtuellt nätverk** > **skapa anpassade**som visas i följande skärmbild.
   
    ![Skapa ett virtuellt nätverk](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)
3. I den **skapa ett VIRTUELLT nätverk** dialogrutan skapar ett nytt virtuellt nätverk genom att gå igenom sidorna och inställningarna i följande tabell. 
   
   | Sidan | Inställningar |
   | --- | --- |
   | Information om virtuellt nätverk |**NAME = ContosoNET**<br/>**REGION = västra USA** |
   | DNS-servrar och VPN-anslutning |Ingen |
   | Virtuellt nätverk adressutrymmen |Inställningarna visas i följande skärmbild: ![Skapa ett virtuellt nätverk](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png) |
4. Skapa den virtuella datorn som ska användas som domänkontrollant (DC). Klicka på **ny** > **Compute** > **virtuella** > **från galleriet**, enligt den följande skärmbild.
   
    ![Skapa en virtuell dator](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)
5. I den **skapa en virtuell dator** dialogrutan konfigurerar du en ny virtuell dator genom att gå igenom sidorna och inställningarna i följande tabell. 
   
   | Sidan | Inställningar |
   | --- | --- |
   | Välj operativsystem för virtuell dator |Windows Server 2012 R2 Datacenter |
   | Konfiguration av virtuell dator |**VERSION LANSERINGSDATUMET** = (senaste)<br/>**NAMN på virtuell dator** = ContosoDC<br/>**NIVÅN** = STANDARD<br/>**STORLEK** = A2 (2 kärnor)<br/>**NYTT användarnamn** = AzureAdmin<br/>**NYTT lösenord** = Contoso! 000<br/>**BEKRÄFTA** = Contoso! 000 |
   | Konfiguration av virtuell dator |**MOLNTJÄNSTEN** = skapa en ny molntjänst<br/>**DNS-MOLNTJÄNSTNAMNET** = en unik molntjänstnamnet<br/>**DNS-namnet** = ett unikt namn (ex: ContosoDC123)<br/>**REGION/TILLHÖRIGHETSGRUPP/VIRTUELLT nätverk** = ContosoNET<br/>**VIRTUELLA UNDERNÄT** = Back(10.10.2.0/24)<br/>**STORAGE-konto** = Använd en automatiskt genererad storage-konto<br/>**TILLGÄNGLIGHETSUPPSÄTTNINGEN** = (ingen) |
   | Alternativ för virtuell dator |Använd standardvärden |

När du konfigurerar den nya virtuella datorn, vänta tills den virtuella datorn ska provsioned. Den här processen tar en stund. Om du klickar på den **virtuella** fliken i den klassiska Azure-portalen kan du se ContosoDC reglering tillstånd från **start (etablering)** till **stoppad**,  **Starta**, **körs (etablering)**, och slutligen **kör**.

DC-servern är nu etablerats. Därefter konfigurerar du Active Directory-domänen på den här DC-servern.

## <a name="configure-the-domain-controller"></a>Konfigurera en domänkontrollant
I följande steg ska konfigurera du den ContosoDC datorn som en domänkontrollant för corp.contoso.com.

1. I portalen, väljer du den **ContosoDC** datorn. På den **instrumentpanelen** klickar du på **Anslut** att öppna en anslutning till fjärrskrivbord (RDP)-fil för fjärråtkomst till skrivbordet.
   
    ![Ansluta till datorn Vritual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)
2. Logga in med ditt konfigurerade administratörskonto (**\AzureAdmin**) och lösenord (**Contoso! 000**).
3. Som standard den **Serverhanteraren** instrumentpanelen ska visas.
4. Klicka på den **Lägg till roller och funktioner** länk på instrumentpanelen.
   
    ![Server Explorer Lägg till roller](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)
5. Klicka på **nästa** tills du kommer till den **serverroller** avsnitt.
6. Välj den **Active Directory Domain Services** och **DNS-Server** roller. När du uppmanas, lägger du till fler funktioner som kräver dessa roller.
   
   > [!NOTE]
   > Du får en varning om att det finns ingen statisk IP-adress verifiering. Om du vill testa konfigurationen, klickar du på **Fortsätt**. För produktion scenarier [använda PowerShell för att ange statiska IP-adressen för den domain controller datorn](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   > 
   > 
   
    ![Lägg till roller dialogrutan](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)
7. Klicka på **nästa** tills du når den **bekräftelse** avsnitt. Välj den **starta om målservern automatiskt om det behövs** kryssrutan.
8. Klicka på **Installera**.
9. När funktionerna installeras tillbaka till den **Serverhanteraren** instrumentpanelen.
10. Välj den nya **AD DS** alternativet i den vänstra rutan.
11. Klicka på den **mer** länk på den gula varning-fältet.
    
     ![Dialogrutan för AD DS på virtuell dator med DNS-server](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)
12. I den **åtgärd** kolumnen i den **alla Server aktivitetsinformation** dialogrutan klickar du på **befordra servern till en domänkontrollant**.
13. I den **konfigurationsguiden Active Directory Domain Services**, använder du följande värden:
    
    | Sidan | Inställning |
    | --- | --- |
    | Distributionskonfiguration |**Lägg till en ny skog** = valda<br/>**Rotdomännamn** = corp.contoso.com |
    | Alternativ för domänkontrollant |**Lösenordet** = Contoso! 000<br/>**Bekräfta lösenord** = Contoso! 000 |
14. Klicka på **nästa** gå igenom andra sidor i guiden. På den **Kravkontroll** kontrollerar du att du ser följande meddelande: **alla kravkontroller har lyckats**. Observera att bör du granska alla meddelanden, men det är möjligt att fortsätta med installationen.
15. Klicka på **Installera**. Den **ContosoDC** virtuella datorn ska startas om automatiskt.

## <a name="configure-domain-accounts"></a>Konfigurera domänkonton
Nästa steg konfigurera Active Directory-konton för senare användning.

1. Logga in på den **ContosoDC** datorn.
2. I **Serverhanteraren**, klickar du på **verktyg** > **Active Directory Administrationscenter**.
   
    ![Active Directory Administrationscenter](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)
3. I den **Active Directory Administrationscenter**väljer **corp (lokal)** i den vänstra rutan.
4. Till höger **uppgifter** rutan klickar du på **ny** > **användaren**. Använd följande inställningar:
   
   | Inställning | Värde |
   | --- | --- |
   | **Förnamn** |Installera |
   | **Användare SamAccountName** |Installera |
   | **Lösenord** |Contoso! 000 |
   | **Bekräfta lösenord** |Contoso! 000 |
   | **Andra alternativ för lösenord** |Vald |
   | **Lösenordet upphör aldrig att gälla** |Markerad |
5. Klicka på **OK** att skapa den **installera** användare. Det här kontot används för att konfigurera failover-kluster och tillgänglighetsgruppen.
6. Skapa två ytterligare användare **CORP\SQLSvc1** och **CORP\SQLSvc2**, med samma steg. Dessa konton används för SQL Server-instanser. Därefter måste du ge **CORP\Install** tillräcklig behörighet för att konfigurera Windows-redundanskluster.
7. I den **Active Directory Administrationscenter**, klickar du på **corp (lokal)** i den vänstra rutan. I den **uppgifter** rutan klickar du på **egenskaper**.
   
    ![Egenskaper för CORP-användare](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)
8. Välj **tillägg**, och klicka sedan på den **Avancerat** knappen på den **säkerhet** fliken.
9. I den **avancerade säkerhetsinställningar för corp** dialogrutan klickar du på **Lägg till**.
10. Klicka på **Välj en huvudansvarig**, söka efter **CORP\Install**, och klicka sedan på **OK**.
11. Välj den **läsa alla egenskaper** och **skapa datorobjekt** behörigheter.
    
     ![Corp användarbehörighet](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)
12. Klicka på **OK**, och klicka sedan på **OK** igen. Stäng fönstret corp egenskaper.

Nu när du har konfigurerat Active Directory och användarobjekt kan du skapa tre virtuella datorer för SQL Server och Anslut dem till den här domänen.

## <a name="create-the-sql-server-virtual-machines"></a>Skapa de virtuella datorerna för SQL Server
Skapa tre virtuella datorer. En är en klusternod och två är för SQL Server. Om du vill skapa var och en av de virtuella datorerna, gå tillbaka till den klassiska Azure-portalen klickar du på **ny** > **Compute** > **virtuella**  >  **Från galleriet**. Använd sedan mallarna i följande tabell för att skapa de virtuella datorerna.

| Sidan | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Välj operativsystem för virtuell dator |**Windows Server 2012 R2 Datacenter** |**SQL Server 2014 RTM Enterprise** |**SQL Server 2014 RTM Enterprise** |
| Konfiguration av virtuell dator |**VERSION LANSERINGSDATUMET** = (senaste)<br/>**NAMN på virtuell dator** = ContosoWSFCNode<br/>**NIVÅN** = STANDARD<br/>**STORLEK** = A2 (2 kärnor)<br/>**NYTT användarnamn** = AzureAdmin<br/>**NYTT lösenord** = Contoso! 000<br/>**BEKRÄFTA** = Contoso! 000 |**VERSION LANSERINGSDATUMET** = (senaste)<br/>**NAMN på virtuell dator** = ContosoSQL1<br/>**NIVÅN** = STANDARD<br/>**STORLEK** = A3 (4 kärnor)<br/>**NYTT användarnamn** = AzureAdmin<br/>**NYTT lösenord** = Contoso! 000<br/>**BEKRÄFTA** = Contoso! 000 |**VERSION LANSERINGSDATUMET** = (senaste)<br/>**NAMN på virtuell dator** = ContosoSQL2<br/>**NIVÅN** = STANDARD<br/>**STORLEK** = A3 (4 kärnor)<br/>**NYTT användarnamn** = AzureAdmin<br/>**NYTT lösenord** = Contoso! 000<br/>**BEKRÄFTA** = Contoso! 000 |
| Konfiguration av virtuell dator |**MOLNTJÄNSTEN** = tidigare skapade unikt DNS-Molntjänstnamnet (ex: ContosoDC123)<br/>**REGION/TILLHÖRIGHETSGRUPP/VIRTUELLT nätverk** = ContosoNET<br/>**VIRTUELLA UNDERNÄT** = Back(10.10.2.0/24)<br/>**STORAGE-konto** = Använd en automatiskt genererad storage-konto<br/>**TILLGÄNGLIGHETSUPPSÄTTNING** = skapa en tillgänglighet ange<br/>**NAMNET PÅ TILLGÄNGLIGHETSUPPSÄTTNINGEN** = SQLHADR |**MOLNTJÄNSTEN** = tidigare skapade unikt DNS-Molntjänstnamnet (ex: ContosoDC123)<br/>**REGION/TILLHÖRIGHETSGRUPP/VIRTUELLT nätverk** = ContosoNET<br/>**VIRTUELLA UNDERNÄT** = Back(10.10.2.0/24)<br/>**STORAGE-konto** = Använd en automatiskt genererad storage-konto<br/>**TILLGÄNGLIGHETSUPPSÄTTNING** = SQLHADR (du kan också konfigurera tillgänglighetsuppsättning när datorn har skapats. Alla tre datorer ska tilldelas till tillgänglighetsuppsättningen SQLHADR.) |**MOLNTJÄNSTEN** = tidigare skapade unikt DNS-Molntjänstnamnet (ex: ContosoDC123)<br/>**REGION/TILLHÖRIGHETSGRUPP/VIRTUELLT nätverk** = ContosoNET<br/>**VIRTUELLA UNDERNÄT** = Back(10.10.2.0/24)<br/>**STORAGE-konto** = Använd en automatiskt genererad storage-konto<br/>**TILLGÄNGLIGHETSUPPSÄTTNING** = SQLHADR (du kan också konfigurera tillgänglighetsuppsättning när datorn har skapats. Alla tre datorer ska tilldelas till tillgänglighetsuppsättningen SQLHADR.) |
| Alternativ för virtuell dator |Använd standardvärden |Använd standardvärden |Använd standardvärden |

<br/>

> [!NOTE]
> Den tidigare konfigurationen föreslår standardnivån virtuella datorer eftersom grundnivån datorer inte stöder belastningsutjämnade slutpunkter. Du måste belastningsutjämnade slutpunkter senare för att skapa en tillgänglighetsgruppslyssnare. Dessutom är datorstorlekar som föreslås här avsedda för att testa Tillgänglighetsgrupper i Azure Virtual Machines. För bästa prestanda på produktionsarbetsbelastningar finns rekommendationer för SQL Server-datorstorlekar och konfigurationen i [prestandarelaterade Metodtips för SQL Server i Azure Virtual Machines](../sql/virtual-machines-windows-sql-performance.md).
> 
> 

När de tre virtuella datorerna är helt etablerad, måste du koppla dem till den **corp.contoso.com** domän och bevilja CORP\Install administratörsbehörighet till datorer. Gör detta genom att använda följande steg för var och en av tre virtuella datorer.

1. Ändra önskad DNS-serveradress. Ladda ned RDP-filen för varje virtuell dator till din lokala katalog genom att välja den virtuella datorn i listan och klicka på den **Anslut** knappen. Om du vill välja en virtuell dator, klicka på var som helst men den första cellen på raden som visas i följande skärmbild.
   
    ![Hämta RDP-filen](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)
2. Öppna RDP-filen som du hämtade och logga in på den virtuella datorn med hjälp av ditt konfigurerade administratörskonto (**BUILTIN\AzureAdmin**) och lösenord (**Contoso! 000**).
3. När du loggar in, bör du se den **Serverhanteraren** instrumentpanelen. Klicka på **lokal Server** i den vänstra rutan.
4. Klicka på den **IPv4-adress av DHCP, IPv6 aktiverat** länk.
5. I den **nätverksanslutningar** dialogrutan klickar du på nätverksikonen.
   
    ![Ändra den virtuella dator som önskad DNS-servern](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)
6. Klicka på kommandofältet **ändra inställningarna för den här anslutningen**. (Beroende på storleken på fönstret kan du kanske måste klicka på högerpilen för att se det här kommandot dubbla).
7. Välj **Internet Protocol Version 4 (TCP/IPv4)**, och klicka sedan på **egenskaper**.
8. Välj **Använd följande DNS-serveradresser** och sedan ange **10.10.2.4** i **önskad DNS-server**.
9. Den **10.10.2.4** adressen är den adress som är tilldelad till en virtuell dator i 10.10.2.0/24 undernät i Azure-nätverk. Den virtuella datorn är **ContosoDC**. Att verifiera **ContosoDC**'s IP-adress, Använd **nslookup contosodc** i Kommandotolkens fönster som visas i följande skärmbild.
   
    ![Använda NSLOOKUP för att hitta IP-adress för domänkontrollant](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)
10. Klicka på **OK** > **Stäng** att genomföra ändringarna. Du kan nu koppla den virtuella datorn till **corp.contoso.com**.
11. I den **lokal Server** -fönstret klickar du på den **arbetsgrupp** länk.
12. I den **datornamn** klickar du på **ändra**.
13. Välj den **domän** kryssrutan, skriver **corp.contoso.com** i textrutan och klicka sedan på **OK**.
14. I den **Windows-säkerhet** dialogrutan Ange autentiseringsuppgifter för standardkontot för administratör (**CORP\AzureAdmin**) och lösenordet (**Contoso! 000**).
15. När meddelandet ”Välkommen till domänen corp.contoso.com”, klickar du på **OK**.
16. Klicka på **Stäng** > **starta om nu** i dialogrutan.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-virtual-machine"></a>Lägg till Corp\Install användaren som administratör på varje virtuell dator
1. Vänta tills virtuella datorn har startats om och sedan öppna RDP-filen igen om du vill logga in på den virtuella datorn med hjälp av den **BUILTIN\AzureAdmin** konto.
2. I **Serverhanteraren** klickar du på **verktyg** > **Datorhantering**.
   
    ![Datorhantering](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)
3. I den **Datorhantering** dialogrutan Expandera **lokala användare och grupper**, och klicka sedan på **grupper**.
4. Dubbelklicka på den **administratörer** grupp.
5. I den **egenskaper för administratörer** dialogrutan klickar du på den **Lägg till** knappen.
6. Ange användaren **CORP\Install**, och klicka sedan på **OK**. När du tillfrågas om autentiseringsuppgifter, Använd den **AzureAdmin** med den **Contoso! 000** lösenord.
7. Klicka på **OK** att stänga den **Administratörsegenskaper** dialogrutan.

### <a name="add-the-failover-clustering-feature-to-each-virtual-machine"></a>Lägga till funktionen kluster för växling vid fel i varje virtuell dator
1. I den **Serverhanteraren** instrumentpanelen, klickar du på **Lägg till roller och funktioner**.
2. I den **guiden Lägg till roller och funktioner**, klickar du på **nästa** tills du kommer till den **funktioner** sidan.
3. Välj **redundanskluster**. När du uppmanas att lägga till andra beroende funktioner.
   
    ![Lägg till funktionen kluster för växling vid fel till den virtuella datorn](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)
4. Klicka på **nästa**, och klicka sedan på **installera** på den **bekräftelse** sidan.
5. När den **Redundansklustring** funktionsinstallationen är klar klickar du på **Stäng**.
6. Logga ut från den virtuella datorn.
7. Upprepa stegen i det här avsnittet för alla tre servrar: **ContosoWSFCNode**, **ContosoSQL1**, och **ContosoSQL2**.

SQL Server virtuella datorer har nu etablerats och körs, men varje har alternativ för SQL Server.

## <a name="create-the-failover-cluster"></a>Skapa failover-kluster
I det här avsnittet skapar du redundansklustret som är värd för tillgänglighetsgruppen som du vill skapa senare. Nu, bör du har gjort följande för att var och en av de tre virtuella datorer som du ska använda i klustret:

* Helt etablerad den virtuella datorn i Azure
* Den virtuella datorn ansluten till domänen
* Lägga till **CORP\Install** till den lokala gruppen Administratörer
* Lägga till funktionen kluster för växling vid fel

Alla dessa är krav på varje virtuell dator innan du kan ansluta till klustret.

Observera också att virtuella Azure-nätverket inte fungerar på samma sätt som ett lokalt nätverk. Du behöver skapa klustret i följande ordning:

1. Skapa ett enkelnods kluster på en nod (**ContosoSQL1**).
2. Ändra klustrets IP-adress till en oanvänd IP-adress (**10.10.2.101**).
3. Sätta klusternamnet.
4. Lägga till andra noder (**ContosoSQL2** och **ContosoWSFCNode**).

Använd följande steg för att utföra uppgifter som helt konfigurerar klustret.

1. Öppna RDP-filen för **ContosoSQL1**, och logga in med hjälp av domänkontot **CORP\Install**.
2. I den **Serverhanteraren** instrumentpanelen, klickar du på **verktyg** > **Klusterhanteraren**.
3. I det vänstra fönstret högerklickar du på **Klusterhanteraren**, och klicka sedan på **skapa ett kluster**som visas i följande skärmbild.
   
    ![Skapa kluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)
4. Skapa ett kluster med en nod i guiden Skapa kluster genom att gå igenom sidorna och inställningarna i följande tabell:
   
   | Sidan | Inställningar |
   | --- | --- |
   | Innan du börjar |Använd standardvärden |
   | Välj servrar |Typen **ContosoSQL1** i **ange servernamnet** och på **Lägg till** |
   | Verifieringsvarning |Välj **Nej jag inte behöver support från Microsoft för det här klustret och därför inte vill köra verifieringstesterna. När jag klickar på nästa fortsätter att skapa klustret**. |
   | Åtkomstpunkt för administration av klustret |Typen **Cluster1** i **klusternamn** |
   | Bekräftelse |Använd standardvärden om du inte använder lagringsutrymmen. Se varning nedan. |
   
   > [!WARNING]
   > Om du använder [lagringsutrymmen](https://technet.microsoft.com/library/hh831739)som grupperar flera diskar i lagringspooler, måste du ta bort den **lägga till alla tillgängliga lagringsenheter i klustret** kryssrutan på den **bekräftelse**sidan. Om du inte avmarkerar det här alternativet kommer att frånkoppla de virtuella diskarna under klustringsprocessen. Därför kan visas de också inte i Diskhantering eller Explorer förrän lagringsutrymmen tas bort från klustret och anbringas på nytt med hjälp av PowerShell.
   > 
   > 
5. I den vänstra rutan, expanderar **Klusterhanteraren**, och klicka sedan på **Cluster1.corp.contoso.com**.
6. I mittenfönstret rulla ned till den **klustrets kärnresurser** avsnittet och expandera den **namn: Clutser1** information. Du bör se både den **namn** och **IP-adress** resurser i den **misslyckades** tillstånd. IP-adressresurs kunde inte försättas online eftersom klustret tilldelas samma IP-adress som dator, vilket är en dubblett-adress.
7. Högerklicka på den misslyckade **IP-adress** resursen och klickar sedan på **egenskaper**.
   
    ![Egenskaper för klustret](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)
8. Välj **statisk IP-adress**, ange **10.10.2.101** i den **adress** textrutan och klicka sedan på **OK**.
9. I den **klustrets kärnresurser** avsnittet högerklickar du på **namn: Cluster1**, och klicka sedan på **Anslut**. Vänta tills båda resurser är online. När namnet klusterresursen är online, har DC-servern uppdaterats med en ny Active Directory-datorkontot. Den här Active Directory-konto används för att köra tillgänglighet grupp klustrade tjänsten senare.
10. Lägga till övriga noder i klustret. Högerklicka i webbläsarträdet **Cluster1.corp.contoso.com**, och klicka sedan på **Lägg till nod**som visas i följande skärmbild.
    
     ![Lägg till nod i klustret](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)
11. I den **guiden Lägg till nod**, klickar du på **nästa** på den **Välj servrar** lägger du till **ContosoSQL2** och **ContosoWSFCNode**  i listan genom att ange servernamnet i **ange servernamnet** och sedan klicka på **Lägg till**. När du är klar klickar du på **nästa**.
12. På den **verifieringsvarning** klickar du på **nr**, men i ett produktionsscenario, ska du utföra verifieringstesterna. Klicka sedan på **Nästa**.
13. På den **bekräftelse** klickar du på **nästa** att lägga till noder.
    
    > [!WARNING]
    > Om du använder [lagringsutrymmen](https://technet.microsoft.com/library/hh831739)som grupperar flera diskar i lagringspooler, måste du ta bort den **lägga till alla tillgängliga lagringsenheter i klustret** kryssrutan. Om du inte avmarkerar det här alternativet kommer att frånkoppla de virtuella diskarna under klustringsprocessen. Därför kan de också visas inte i Diskhantering eller Explorer förrän lagringsutrymmen tas bort från klustret och anbringas på nytt med hjälp av PowerShell.
    > 
    > 
14. När noder läggs till i klustret, klickar du på **Slutför**. Hanteraren för redundanskluster visas nu att klustret har tre noder och visa dem i den **noder** behållare.
15. Logga ut från fjärrskrivbords-sessionen.

## <a name="prepare-the-sql-server-instances-for-availability-groups"></a>Förbereda SQL Server-instanser för Tillgänglighetsgrupper
I det här avsnittet ska du göra följande på både **ContosoSQL1** och **contosoSQL2**:

* Lägg till en inloggning för **NT AUTHORITY\System** med nödvändiga behörigheter till standardinstansen för SQL Server.
* Lägg till **CORP\Install** som en sysadmin-roll till standardinstansen för SQL Server.
* Öppna i brandväggen för fjärråtkomst av SQL Server.
* Aktivera alltid på tillgänglighet grupper funktionen.
* Ändra SQL Server-tjänstkontot till **CORP\SQLSvc1** och **CORP\SQLSvc2**respektive.

Dessa åtgärder kan utföras i valfri ordning. Följande steg kommer dock gå igenom dem i ordning. Följ stegen för både **ContosoSQL1** och **ContosoSQL2**:

1. Om du inte har loggat ut från fjärrskrivbords-sessionen för den virtuella datorn, ska du göra det nu.
2. Öppna RDP-filer för **ContosoSQL1** och **ContosoSQL2**, och logga in som **BUILTIN\AzureAdmin**.
3. Lägg till **NT AUTHORITY\System** SQL Server-inloggningar med nödvändiga behörigheter. Öppna **SQL Server Management Studio**.
4. Klicka på **Anslut** att ansluta till SQL Server-instansen.
5. I **Object Explorer**, expandera **säkerhet**, och expandera sedan **inloggningar**.
6. Högerklicka på den **NT AUTHORITY\System** inloggning och klicka sedan på **egenskaper**.
7. På den **objekt** sidan för den lokala servern, Välj **bevilja** följande behörigheter och klicka sedan på **OK**.
   
   * ALTER någon tillgänglighetsgrupp
   * Ansluta SQL
   * Visa-Servertillstånd
8. Lägg till **CORP\Install** som en **sysadmin** roll till standardinstansen för SQL Server. I **Object Explorer**, högerklicka på **inloggningar**, och klicka sedan på **ny inloggning**.
9. Typen **CORP\Install** i **inloggningsnamnet**.
10. På den **serverroller** väljer **sysadmin**, och klicka sedan på **OK**. När du har skapat inloggningen som du kan se den genom att expandera **inloggningar** i **Object Explorer**.
11. Att skapa en brandväggsregel för SQL Server på den **starta** skärmen öppnar **Windows-brandväggen med avancerad säkerhet**.
12. I den vänstra rutan, Välj **regler för inkommande trafik**. I den högra rutan, klickar du på **ny regel**.
13. På den **regeltyp** klickar du på **programmet** > **nästa**.
14. På den **programmet** väljer **följande programsökväg**, typen **%ProgramFiles%\Microsoft SQL Server\MSSQL12. MSSQLSERVER\MSSQL\Binn\sqlservr.exe** i textrutan och klicka sedan på **nästa**. Om du följer de här anvisningarna men använder SQL Server 2012, SQL Server-katalogen är **MSSQL11. MSSQLSERVER**.
15. På den **åtgärd** behåller **tillåter anslutningen** markerad och klicka sedan på **nästa**.
16. På den **profil** , accepterar du standardinställningarna och klicka sedan på **nästa**.
17. På den **namn** anger du ett namn för regeln som **SQL Server (programregel)**i den **namn** text och sedan klicka på **Slutför**.
18. Så här aktiverar du den **Always On-Tillgänglighetsgrupper** funktion, på den **starta** skärmen öppnar **SQL Server Configuration Manager**.
19. I webbläsarträdet klickar du på **SQL Server Services**, högerklicka på den **SQL Server (MSSQLSERVER)** tjänst och klicka sedan på **egenskaper**.
20. Klicka på den **alltid på hög tillgänglighet** väljer **aktivera Always On-Tillgänglighetsgrupper**, så som visas i följande skärmbild och klicka sedan på **tillämpa**. Klicka på **OK** i dialogrutan, och Stäng inte gör det **egenskaper** dialogrutan ännu. Du startar om SQL Server-tjänsten när du har ändrat tjänstkontot.
    
     ![Aktivera alltid på Tillgänglighetsgrupper](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)
21. Om du vill ändra SQL Server-tjänstkontot, klickar du på den **logga in** Skriv **CORP\SQLSvc1** (för **ContosoSQL1**) eller **CORP\SQLSvc2** (för **ContosoSQL2**) i **kontonamn**, Fyll i och bekräfta lösenordet och klicka sedan på **OK**.
22. I dialogrutan som öppnas klickar du på **Ja** att starta om SQL Server-tjänsten. När SQL Server-tjänsten har startats om ändringar som du gjort i den **egenskaper** dialogrutan är effektiva.
23. Logga ut från de virtuella datorerna.

## <a name="create-the-availability-group"></a>Skapa tillgänglighetsgruppen
Du är nu redo att konfigurera en tillgänglighetsgrupp. Nedan visas en sammanfattning av vad du ska göra:

* Skapa en ny databas (**MyDB1**) på **ContosoSQL1**.
* Ta både en fullständig säkerhetskopia och en transaktion säkerhetskopia av databasen.
* Återställa hela och loggsäkerhetskopior till **ContosoSQL2** med den **NORECOVERY** alternativet.
* Skapa tillgänglighetsgruppen (**AG1**) med synkront genomförande och automatisk redundans läsbara sekundära repliker.

### <a name="create-the-mydb1-database-on-contososql1"></a>Skapa MyDB1 databasen på ContosoSQL1
1. Om du redan inte har registrerat utanför för fjärrskrivbordssessioner **ContosoSQL1** och **ContosoSQL2**, gör du det nu.
2. Öppna RDP-filen för **ContosoSQL1**, och logga in som **CORP\Install**.
3. I **Utforskaren**under **C:\\**, skapa en katalog med namnet **säkerhetskopiering**. Du använder den här katalogen för att säkerhetskopiera och återställa databasen.
4. Högerklicka på den nya katalogen, peka på **dela med**, och klicka sedan på **specifika personer**som visas i följande skärmbild.
   
    ![Skapa en mapp för säkerhetskopiering](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)
5. Lägg till **CORP\SQLSvc1**, och ge det de **läsning och skrivning** behörighet. Lägg till **CORP\SQLSvc2**, och ge det de **Läs** behörighet, som visas i följande skärmbild och klicka sedan på **resursen**. När fildelning-processen är klar klickar du på **klar**.
   
    ![Bevilja behörigheter för mapp för säkerhetskopiering](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)
6. Att skapa databasen från den **starta** öppna menyn **SQL Server Management Studio**, och klicka sedan på **Anslut** att ansluta till SQL Server-instansen.
7. I **Object Explorer**, högerklicka på **databaser**, och klicka sedan på **ny databas**.
8. I **databasnamnet**, typen **MyDB1**, och klicka sedan på **OK**.

### <a name="make-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Skapa en fullständig säkerhetskopia av MyDB1 och återställa det på ContosoSQL2
1. Att göra en fullständig säkerhetskopiering av databasen i **Object Explorer**, expandera **databaser**, högerklicka på **MyDB1**, peka på **uppgifter**, och sedan Klicka på **säkerhetskopiera**.
2. I den **källa** och hålla **typ av säkerhetskopiering** inställd på **fullständig**. I den **mål** klickar du på **ta bort** att ta bort standardsökvägen för säkerhetskopian.
3. I den **mål** klickar du på **Lägg till**.
4. I den **filnamn** skriver  **\\ContosoSQL1\backup\MyDB1.bak**, klickar du på **OK**, och klicka sedan på **OK** igen för att säkerhetskopiera upp i databasen. När säkerhetskopieringen är klar klickar du på **OK** igen för att stänga dialogrutan.
5. Att göra en säkerhetskopia av databasen, transaktion i **Object Explorer**, expandera **databaser**, högerklicka på **MyDB1**, peka på **uppgifter**, och klicka sedan på **säkerhetskopiera**.
6. I **typ av säkerhetskopiering**väljer **transaktionsloggen**. Behåll den **mål** filen sökväg angiven till den som du angav tidigare och klicka sedan på **OK**. När säkerhetskopieringen är klar klickar du på **OK** igen.
7. För att återställa full och transaktionen loggsäkerhetskopior **ContosoSQL2**, Öppna RDP-filen för **ContosoSQL2**, och logga in som **CORP\Install**. Lämna fjärrskrivbords-sessionen för **ContosoSQL1** öppna.
8. Från den **starta** öppna menyn **SQL Server Management Studio**, och klicka sedan på **Anslut** att ansluta till SQL Server-instansen.
9. I **Object Explorer**, högerklicka på **databaser**, och klicka sedan på **Restore Database**.
10. I den **källa** väljer **enhet**, och klicka på knappen **...** till.
11. I **Välj säkerhetskopieringsenheter**, klickar du på **Lägg till**.
12. I **säkerhetskopiera filplats**, typen  **\\ContosoSQL1\backup**, klickar du på **uppdatera**väljer **MyDB1.bak**, klickar du på  **OK**, och klicka sedan på **OK** igen. Du bör nu se en fullständig säkerhetskopiering och loggsäkerhetskopiering i den **säkerhetskopiorna för att återställa** fönstret.
13. Gå till den **alternativ** väljer **RESTORE WITH NORECOVERY** i **återställningstillstånd**, och klicka sedan på **OK** att återställa databasen. När återställningen är klar klickar du på **OK**.

### <a name="create-the-availability-group"></a>Skapa tillgänglighetsgruppen
1. Gå tillbaka till fjärrskrivbords-sessionen för **ContosoSQL1**. I **Object Explorer** i SQL Server Management Studio högerklickar du på **alltid på hög tillgänglighet**, och klicka sedan på **guiden Ny tillgänglighetsgrupp**, enligt den följande skärmbild.
   
    ![Starta guiden Ny tillgänglighetsgrupp](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)
2. På den **introduktion** klickar du på **nästa**. På den **ange tillgänglighetsgruppens namn** anger **AG1** i **tillgänglighetsgruppens namn**, klicka på **nästa** igen.
   
    ![Guiden Ny tillgänglighetsgrupp, ange tillgänglighetsgruppens namn](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)
3. På den **Välj databaser** väljer **MyDB1**, och klicka sedan på **nästa**. Databasen uppfyller kraven för en tillgänglighetsgrupp eftersom du har utfört en fullständig säkerhetskopiering på den avsedda primära repliken.
   
    ![Guiden Ny Availabilty grupp, Välj databaser](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)
4. på den **ange repliker** klickar du på **lägga till replik**.
   
    ![Guiden Ny Availabilty grupp, ange repliker](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)
5. I den **Anslut till Server** skriver **ContosoSQL2** i **servernamn**, och klicka sedan på **Anslut**.
   
    ![Ny Availabilty grupp guiden Anslut till server](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)
6. Tillbaka på den **ange repliker** sidan du bör nu se **ContosoSQL2** som anges i **tillgängliga repliker**. Konfigurera replikerna som visas i följande skärmbild. När du är klar klickar du på **nästa**.
   
    ![Guiden Ny Availabilty grupp, ange repliker (slutför)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)
7. På den **Välj inledande datasynkronisering** väljer **Anslut endast**, och klicka sedan på **nästa**. Du redan utfört datasynkronisering manuellt när du har gjort fullständig och transaktionen säkerhetskopieringar på **ContosoSQL1** och återställa dem på **ContosoSQL2**. Kan du inte utföra säkerhetskopiering och återställning på din databas och i stället väljer **fullständig** så att i guiden Ny tillgänglighetsgrupp utföra datasynkronisering åt dig. Vi rekommenderar dock inte det här alternativet för mycket stora databaser som hittas i vissa företag.
   
    ![Guiden Ny Availabilty, Välj inledande datasynkronisering](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)
8. På den **validering** klickar du på **nästa**. Den här sidan bör likna följande skärmbild. Det finns en varning för Lyssnarkonfigurationen eftersom du inte har konfigurerat en tillgänglighetsgruppslyssnare. Du kan ignorera den här varningen, eftersom den här kursen inte konfigurera en lyssnare. Om du vill konfigurera lyssnaren när du har slutfört den här självstudiekursen, se [konfigurera en ILB-lyssnare för Always On-Tillgänglighetsgrupper i Azure](../classic/ps-sql-int-listener.md).
   
    ![Guiden Ny Availabilty servergrupp, validering](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)
9. På den **sammanfattning** klickar du på **Slutför**, och sedan vänta medan guiden konfigurerar nya tillgänglighetsgruppen. På den **förlopp** sidan som du kan klicka på **mer** att visa detaljerad förloppet. När guiden har slutförts kan du granska den **resultat** för att kontrollera att tillgänglighetsgruppen har skapats som visas i följande skärmbild och klicka sedan på **Stäng** vill avsluta guiden.
   
    ![Guiden Ny Availabilty servergrupp, resultat](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)
10. I **Object Explorer**, expandera **alltid på hög tillgänglighet**, och expandera sedan **Tillgänglighetsgrupper**. Du bör nu se nya tillgänglighetsgruppen i den här behållaren. Högerklicka på **AG1 (primär)**, och klicka sedan på **visa instrumentpanelen**.
    
     ![Visa Availability Group-instrumentpanelen](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)
11. Din **alltid på instrumentpanelen** bör likna den som finns i följande skärmbild. Du kan se replikerna, redundansläge för varje replik- och tillstånd för synkronisering.
    
     ![Tillgänglighetsgruppen instrumentpanelen](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)
12. Gå tillbaka till **Serverhanteraren**, klickar du på **verktyg**, och sedan öppna **Klusterhanteraren**.
13. Expandera **Cluster1.corp.contoso.com**, och expandera sedan **tjänster och program**. Välj **roller** och Observera att den **AG1** tillgänglighet grupp rollen har skapats. Observera att AG1 inte har en IP-adress genom vilken databas som klienter kan ansluta till tillgänglighetsgruppen, eftersom du inte konfigurerar en lyssnare. Du kan ansluta direkt till den primära noden för Läs-och skrivåtgärder och den sekundära noden för skrivskyddade frågor.
    
     ![AG i hanteraren för redundanskluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

> [!WARNING]
> Försök inte att redundansväxla tillgänglighetsgruppen från hanteraren för redundanskluster. Alla redundansåtgärder ska utföras inifrån **alltid på instrumentpanelen** i SQL Server Management Studio. Mer information finns i [begränsningar på med hjälp av i hanteraren för redundanskluster med Tillgänglighetsgrupper](https://msdn.microsoft.com/library/ff929171.aspx).
> 
> 

## <a name="next-steps"></a>Nästa steg
Du har nu har implementerat SQL Server alltid aktiverad genom att skapa en tillgänglighetsgrupp i Azure. Om du vill konfigurera en lyssnare för den här tillgänglighetsgruppen finns [konfigurera en ILB-lyssnare för Always On-Tillgänglighetsgrupper i Azure](../classic/ps-sql-int-listener.md).

Annan information om hur du använder SQL Server i Azure, se [SQL Server på Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

