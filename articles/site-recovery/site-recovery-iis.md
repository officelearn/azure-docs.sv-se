---
title: "Replikera en flera nivåer IIS baserade webbapp med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du replikera IIS web grupp virtuella datorer med hjälp av Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: nisoneji
ms.openlocfilehash: 4ac79df703de00ac009d9845772d8be740e74f29
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="replicate-a-multi-tier-iis-based-web-application-using-azure-site-recovery"></a>Replikera en IIS-baserad webbtjänst flernivåapp med hjälp av Azure Site Recovery

## <a name="overview"></a>Översikt


Programvara är motorn av företagsproduktiviteten i en organisation. Olika program kan ha olika syften i en organisation. Vissa av dessa som löneuppgifter bearbetning, ekonomiprogram och kundinriktade webbplatser kan vara ytterst viktigt för organisationen. Ska det vara viktigt för organisationen för att låta och körs på alla gånger för att förhindra förlust av produktivitet och viktigare förhindra skador på varumärken avbildningen av organisationen.

Kritiska webbprogram anges vanligtvis som program på flera nivåer med webb-, databas och program på olika nivåer. Förutom att sprida över olika nivåer, kan programmen också använda flera servrar i varje nivå för att belastningsutjämna trafiken. Dessutom kan mappningar mellan olika nivåer och på webbservern vara baserad på statiska IP-adresser. Vid redundans måste vissa av dessa mappningar uppdateras, särskilt om du har flera webbplatser som är konfigurerade på webbservern. Vid webbprogram med hjälp av SSL måste-certifikatbindningar uppdateras.

Traditionella icke-baserat replikeringsåterställning metoder innebär säkerhetskopiering för olika configuration-filer, registerinställningar, bindningar, anpassade komponenter (COM eller .NET), innehåll och även certifikat och återställer filerna via en uppsättning manuella steg. Dessa tekniker är tydligt besvärlig fel felbenägna och inte skalbara. Det är till exempel enkelt möjlig för du glömmer säkerhetskopiera certifikat och lämnas med inga val men köpa nya certifikat för servern efter växling vid fel.

En bra lösning för haveriberedskap, ska tillåta modellering av återställningsplaner runt ovanstående programarkitekturer för komplexa och har också möjlighet att lägga till anpassade steg för att hantera mappning mellan olika nivåer som därför att tillhandahålla en enkel klickning att lösningen vid en katastrof som leder till en lägre Återställningstidsmål.


Den här artikeln beskriver hur du skyddar en IIS baserat webbprogram som använder en [Azure Site Recovery](site-recovery-overview.md). Den här artikeln beskriver bästa praxis för att replikera en tre nivå IIS baserat webbprogram till Azure, hur du kan göra en katastrof återställningsgranskning och hur du kan redundansväxla programmet till Azure.


## <a name="prerequisites"></a>Krav

Innan du börjar bör du kontrollera att du känna till följande:

1. [Replikera en virtuell dator till Azure](site-recovery-vmware-to-azure.md)
1. Så här [utforma ett nätverk för återställning](site-recovery-network-design.md)
1. [Gör ett redundanstest till Azure](./site-recovery-test-failover-to-azure.md)
1. [Gör en redundansväxling till Azure](site-recovery-failover.md)
1. Så här [replikera en domänkontrollant](site-recovery-active-directory.md)
1. Så här [replikera SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Mönster för distribution
Ett IIS-baserade webbprogram följer vanligtvis något av följande distribution mönster:

** Distribution mönstret 1 ** ett IIS-baserade webbgrupp med programmet begär Routing(ARR), IIS-Server och Microsoft SQL Server.

![Mönster för distribution](./media/site-recovery-iis/deployment-pattern1.png)

**Distribution av mönstret 2** en IIS baserade webbservergrupp med programmet begär Routing(ARR), IIS-servern, programserver och Microsoft SQL Server.


![Mönster för distribution](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery-stöd

För att skapa den här artikeln virtuella VMware-datorer med IIS-servern version 7.5 på Windows Server 2012 R2 Enterprise användes. Site recovery replikering är oberoende av programmet, förväntas de rekommendationer som anges här för följande scenarier samt och för olika versioner av IIS.

### <a name="source-and-target"></a>Källa och mål

**Scenario** | **Till en sekundär plats** | **Till Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja
**Fysisk server** | Nej | Ja

## <a name="replicate-virtual-machines"></a>Replikera virtuella datorer

Följ [vägledningen](site-recovery-vmware-to-azure.md) att starta replikering av alla IIS web grupp virtuella datorer till Azure.

Om du använder en statisk IP-adress och sedan ange IP-adress som du vill att den virtuella datorn i den [ **mål-IP** ](./site-recovery-replicate-vmware-to-azure.md#view-and-manage-vm-properties) i beräknings-och nätverksinställningar.

![Mål-IP](./media/site-recovery-active-directory/dns-target-ip.png)


## <a name="creating-a-recovery-plan"></a>Skapa en återställningsplan

En återställningsplan kan ordningsföljd för växling vid fel på olika nivåer i en flernivåapp, därför kan upprätthålla programkonsekvens. Följ de nedanstående steg när du skapar en återställningsplan för ett webbprogram med flera nivåer.  [Lär dig mer om hur du skapar en återställningsplan](./site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer till grupper för växling vid fel
Ett typiskt flera nivåer IIS-webbprogram utgörs av en databasnivå med SQL virtuella datorer, webbnivå utgörs av en IIS-server och en program-nivå. Lägg till dessa virtuella datorer till annan grupp baserat på nivå som nedan. [Mer information om att anpassa återställningsplan](site-recovery-runbook-automation.md#customize-the-recovery-plan).

1. Skapa en återställningsplan. Lägg till databas nivå virtuella datorer under grupp 1 för att säkerställa att de är avstängning senast och tas först.

1. Lägg till program nivå virtuella datorer under Grupp2 så att de förs upp när databasnivån växa upp.

1. Lägga till virtuella skiktdatorer web i grupp 3 så att de förs upp när programmet nivån växa upp.

1. Lägg till belastningen belastningsutjämna virtuella datorer i gruppen 4 så att de förs upp efter webbnivån har trätt.


### <a name="adding-scripts-to-the-recovery-plan"></a>Att lägga till skript återställningsplanen
Du kan behöva göra vissa åtgärder på virtuella Azure-datorer efter redundans och testning redundansen för att se IIS web servergruppen funktionen korrekt. Du kan automatisera efter redundansväxlingen som uppdaterar DNS-posten ändrar webbplatsbindning, ändra i anslutningssträngen genom att lägga till motsvarande skript i återställningsplanen som nedan. [Lär dig mer om att lägga till skript återställningsplan](./site-recovery-create-recovery-plans.md#add-scripts).

#### <a name="dns-update"></a>DNS-uppdatering
Om DNS-servern har konfigurerats för dynamisk DNS-uppdatering och virtuella datorer vanligtvis uppdatera DNS med den nya IP när de startar. Om du vill lägga till ett explicit steg för att uppdatera DNS med den nya IP-adresser för virtuella datorer och lägger till detta [skript för att uppdatera IP-Adressen i DNS](https://aka.ms/asr-dns-update) som en post-åtgärd på recovery planeringsgrupper.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Anslutningssträngen i web.config för ett program
Anslutningssträngen anger som webbplatsen kommunicerar med databasen.

Om anslutningssträngen har namnet på den virtuella datorn på databasen, inga ytterligare steg krävs efter växling vid fel och programmet kommer att kunna kommunicera automatiskt DB. Även om IP-adressen för den virtuella datorn för databasen sparas krävs det inte för att uppdatera anslutningssträngen. Om anslutningssträngen refererar till databasen virtuella datorer som använder en IP-adress, måste den vara uppdaterade efter växling vid fel. T.ex. den nedan sträng anslutningspunkter DB med IP-127.0.1.2

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Du kan uppdatera anslutningssträngen i webbnivå genom att lägga till [skript för IIS-anslutning att uppdatera](https://aka.ms/asr-update-webtier-script-classic) efter grupp 3 i återställningsplanen.

#### <a name="site-bindings-for-the-application"></a>Bindningar för webbplats för programmet
Varje plats består av bindning information som innehåller typen av bindning, IP-adressen som IIS-servern lyssnar på begäranden för webbplatsen, portnumret och värdnamn för platsen. Vid tidpunkten för en växling vid fel, kan dessa bindningar måste uppdateras om det finns en ändring i IP-adress som är kopplade till.

> [!NOTE]
>
> Om du har markerat 'alla otilldelade' för webbplatsbindning som i exemplet nedan, behöver du inte uppdatera den här bindningen efter redundansen. Även om IP-adress som är kopplad till en plats inte ändras efter växling vid fel, webbplatsbindning behöver inte har uppdaterats (kvarhållning av IP-adress beror på nätverksarkitekturen och undernät som har tilldelats till de primära platsen och återställningsplatsen platserna och kan därför eller kanske inte är möjligt för din organisation.)

![SSL-bindning](./media/site-recovery-iis/sslbinding.png)

Om IP-adressen har kopplats till en plats, måste du uppdatera alla bindningar för webbplats med den nya IP-adressen. Du kan lägga till [webbserver nivå uppdateringsskriptet](https://aka.ms/asr-web-tier-update-runbook-classic) efter grupp 3 i återställningsplan att ändra bindningarna för webbplatsen.


#### <a name="update-load-balancer-ip-address"></a>Uppdatera IP-adressen för belastningsutjämnaren
Om du har routning av programbegäran virtuell dator, lägger du till [IIS ARR redundans skriptet](https://aka.ms/asr-iis-arrtier-failover-script-classic) efter grupp 4 att uppdatera IP-adressen.

#### <a name="the-ssl-cert-binding-for-an-https-connection"></a>SSL-certifikat-bindning för en https-anslutning
Webbplatser kan ha ett associerat SSL-certifikat som hjälper till att säkerställa en säker kommunikation mellan webbserver och användarens webbläsare. Om webbplatsen har en https-anslutning och en tillhörande https webbplatsbindning IP-adressen för IIS-servern med en SSL-certifikat-bindning, måste en ny bindning för webbplats som ska läggas till efter certifikat med IP-adress för IIS virtuella datorn efter redundans.

SSL-certifikat kan utfärdas mot-

a) det fullständigt kvalificerade domännamnet för webbplatsen<br>
b) namnet på servern<br>
c) ett jokerteckencertifikat för domännamnet<br>
d) en IP-adress – om SSL-certifikat utfärdas mot IP-Adressen för IIS-server, en annan SSL-certifikat måste utfärdas mot IP-adressen för IIS-servern på Azure-webbplatsen och en ytterligare SSL-bindning för det här certifikatet måste skapas. Därför rekommenderas att inte använda ett SSL-certifikat som utfärdats för IP. Detta är ett alternativ för mindre vanliga och snart kommer att inaktualiseras enligt nya CA/webbläsare forum ändringar.

#### <a name="update-the-dependency-between-the-web-and-the-application-tier"></a>Uppdatera beroende mellan webbservern och program-nivå
Om du har ett specifikt tillämpningsprogramberoende baserat på IP-adressen för de virtuella datorerna som du behöver uppdatera den här beroende efter redundansen.

## <a name="doing-a-test-failover"></a>Gör ett redundanstest
Följ [vägledningen](site-recovery-test-failover-to-azure.md) att göra ett redundanstest.

1.  Gå till Azure-portalen och välj Recovery-tjänsten-valvet.
1.  Klicka på återställningsplan som skapats för IIS-webbservergrupp.
1.  Klicka på Testa redundans.
1.  Välj återställningspunkten och Azure-nätverk för att starta processen testa redundans.
1.  Du kan utföra dina verifieringar när den sekundära miljön är upp.
1.  När validering har slutförts, kan du välja verifieringar slutföra och redundanstestmiljön kommer att rensas.

## <a name="doing-a-failover"></a>Genomför en redundansväxling enligt
Följ [vägledningen](site-recovery-failover.md) när du gör en redundansväxling.

1.  Gå till Azure-portalen och välj Recovery-tjänsten-valvet.
1.  Klicka på återställningsplan som skapats för IIS-webbservergrupp.
1.  Klicka på 'Redundans'.
1.  Välj återställningspunkt för att starta processen för växling vid fel.

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om [replikera andra program](site-recovery-workload.md) med Site Recovery.
