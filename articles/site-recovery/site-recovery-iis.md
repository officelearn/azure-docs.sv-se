---
title: Replikera en flera nivåer IIS-baserade webbapp med Azure Site Recovery | Microsoft Docs
description: Lär dig mer om att replikera IIS web grupp virtuella datorer med hjälp av Azure Site Recovery.
services: site-recovery
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: nisoneji
ms.openlocfilehash: 1ed0184ac76b5fb3e607458559327da5e8fe90c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
ms.locfileid: "29809428"
---
# <a name="replicate-a-multi-tier-iis-based-web-application"></a>Replikera en IIS-baserade webbprogram med flera nivåer

Programvara är motorn av företagsproduktiviteten i en organisation. Olika program kan ha olika syften i en organisation. Vissa program, t.ex. program som används för kund-riktade webbplatser, ekonomiprogram och löneuppgifter bearbetning kan vara viktiga för en organisation. Det är viktigt för organisationen har dessa program kontinuerligt igång för att förhindra förlust av produktivitet. Viktigare, kan med dessa program som är konsekvent tillgängliga förhindra skada varumärken eller avbildning av organisationen.

Kritiska webbprogram vanligtvis är inställda som flernivåprogram: webb-, databas- och program som finns på olika nivåer. Förutom att sprida över olika nivåer, kan programmen också använda flera servrar i varje nivå för att belastningsutjämna trafiken. Dessutom kan mappningar mellan olika nivåer och på webbservern vara baserad på statiska IP-adresser. Vid redundans måste vissa av dessa mappningar uppdateras, särskilt om flera webbplatser har konfigurerats på webbservern. Om webbprogram använder SSL, måste du uppdatera certifikatbindningar.

Traditionella återställningsmetoder som inte är baserade på replikering involverar säkerhetskopiera olika configuration-filer, registerinställningar, bindningar, anpassade komponenter (COM eller .NET), innehåll och certifikat. Filer har återställts via en uppsättning manuella steg. Traditionella återställningsmetoder för säkerhetskopiering och återställning av filer manuellt är besvärlig felbenägna och inte skalbara. Du kan till exempel enkelt glömma att säkerhetskopiera certifikat. Efter växling vid fel, är du lämnade ingen urval men köpa nya certifikat för servern.

En bra lösning för haveriberedskap har stöd för modellering återställning planer för komplexa program arkitekturerna. Du bör även kunna lägga till anpassade steg i återställningsplanen så att hantera mappning mellan nivåer. Om det finns en katastrof, Ange programavbildningar en enkelklickning att som visar lösning som hjälper till att leda till en lägre Återställningstidsmål.

Den här artikeln beskriver hur du skyddar ett webbprogram som baseras på Internet Information Services (IIS) med hjälp av [Azure Site Recovery](site-recovery-overview.md). Artikeln innehåller metodtips för att replikera en tre nivåer, IIS-baserade webbappen till Azure, hur du gör en katastrof återställningsgranskning och hur du växla över programmet till Azure.

## <a name="prerequisites"></a>Förutsättningar

Se till att du vet hur du gör följande innan du börjar:

* [Replikera en virtuell dator till Azure](vmware-azure-tutorial.md)
* [Utforma ett nätverk för återställning](site-recovery-network-design.md)
* [Gör ett redundanstest till Azure](site-recovery-test-failover-to-azure.md)
* [Gör en redundansväxling till Azure](site-recovery-failover.md)
* [Replikera en domänkontrollant](site-recovery-active-directory.md)
* [Replikera SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Mönster för distribution
Ett IIS-baserade webbprogram följer vanligtvis något av följande distribution mönster:

**Distribution av mönstret 1**

Ett IIS-baserade webbservergrupp med Routning av programbegäran (ARR), en IIS-server och SQL Server.

![Diagram över en IIS-baserade webbservergrupp som har tre nivåer](./media/site-recovery-iis/deployment-pattern1.png)

**Distribution av mönstret 2**

Ett IIS-baserade webbservergrupp med ARR, en IIS-server, en programserver och SQL Server.

![Diagram över en IIS-baserade webbservergrupp som har fyra nivåer](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery-stöd

I exemplen i den här artikeln använder vi virtuella VMware-datorer med IIS 7.5 på Windows Server 2012 R2 Enterprise. Eftersom Site Recovery replikering inte programspecifika, förväntas rekommendationerna i den här artikeln gäller i de scenarier som beskrivs i följande tabell och för olika versioner av IIS.

### <a name="source-and-target"></a>Källa och mål

Scenario | Till en sekundär plats | Till Azure
--- | --- | ---
Hyper-V | Ja | Ja
VMware | Ja | Ja
Fysisk server | Nej | Ja
Azure|Ej tillämpligt|Ja

## <a name="replicate-virtual-machines"></a>Replikera virtuella datorer

Om du vill starta replikera alla IIS web grupp virtuella datorer till Azure, följer du anvisningarna i [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

Om du använder en statisk IP-adress kan du ange IP-adressen som du vill att den virtuella datorn ska börja. Om du vill ange IP-adress, gå till **beräknings- och nätverksinställningar inställningar** > **mål-IP**.

![Skärmbild som visar hur du ställer in mål-IP i fönstret Site Recovery beräkning och nätverk](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan
En återställningsplan stöder Sekvenseringen av olika nivåer i en flernivåapp under en växling vid fel. Sekvensering upprätthåller programkonsekvens. När du skapar en återställningsplan för ett webbprogram med flera nivåer fullständig steg som beskrivs i [skapa en återställningsplan med Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Lägg till virtuella datorer till grupper för växling vid fel
En typisk IIS web flernivåapp består av följande komponenter:
* En databasnivå som SQL virtuella datorer.
* Webbnivå som består av en IIS-server och en program-nivå. 

Lägg till virtuella datorer i olika grupper baserat på nivå:

1. Skapa en återställningsplan. Lägg till databas nivå virtuella datorer under grupp 1. Detta säkerställer att databasen virtuella skiktdatorer stängs av senaste och tas först.
1. Lägg till program nivå virtuella datorer under Grupp2. Detta säkerställer att programmet nivån virtuella datorer förs upp när databasnivån växa upp.
1. Lägg till virtuella datorer för web-nivån i grupp 3. Detta säkerställer att web nivå virtuella datorer förs upp när programmet nivån växa upp.
1. Lägg till belastningen belastningsutjämna virtuella datorer i gruppen 4. Detta säkerställer att belastningen belastningsutjämna virtuella datorer förs upp efter webbnivån har trätt.

Mer information finns i [anpassa återställningsplanen](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Lägga till ett skript i återställningsplanen
Du kan behöva göra vissa åtgärder på virtuella Azure-datorer postredundans eller under ett redundanstest för den IIS-webbservergruppen ska fungera korrekt. Du kan automatisera vissa åtgärder för postredundans. Du kan till exempel uppdatera DNS-posten, ändra en bindning för webbplats eller ändra en anslutningssträng genom att lägga till motsvarande skript återställningsplanen. [Lägga till en VMM-skript till en återställningsplan](site-recovery-how-to-add-vmmscript.md) beskriver hur du ställer in automatiserade åtgärder med hjälp av ett skript.

#### <a name="dns-update"></a>DNS-uppdatering
Om DNS är konfigurerat för dynamisk DNS-uppdatering, uppdatera virtuella datorer vanligtvis DNS med den nya IP-adressen när de startar. Om du vill lägga till ett explicit steg för att uppdatera DNS med de nya IP-adresserna för virtuella datorer, lägga till en [skript för att uppdatera IP-Adressen i DNS](https://aka.ms/asr-dns-update) som en postredundans-åtgärd på recovery planeringsgrupper.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Anslutningssträngen i web.config för ett program
Anslutningssträngen anger som webbplatsen kommunicerar med databasen. Om anslutningssträngen har namnet på den virtuella datorn på databasen, finns inga ytterligare steg krävs postredundans. Programmet kan automatiskt kommunicera med databasen. Om IP-adressen för den virtuella datorn för databasen sparas att det inte uppdatera anslutningssträngen. 

Om anslutningssträngen refererar till den virtuella datorn i databasen med hjälp av en IP-adress, måste det vara uppdaterade postredundans. Till exempel adressen följande kopplingspunkter av strängen till databasen med IP-127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Om du vill uppdatera anslutningssträngen i webbnivån, lägger du till en [skript för IIS-anslutning att uppdatera](https://aka.ms/asr-update-webtier-script-classic) efter grupp 3 i återställningsplanen.

#### <a name="site-bindings-for-the-application"></a>Bindningar för webbplats för programmet
Varje plats består av bindningsinformation. Bindningsinformationen innehåller typen av bindning, IP-adressen som IIS-servern lyssnar på begäranden för webbplatsen, portnumret och värdnamn för platsen. Under växling vid fel, kan du behöva uppdatera dessa bindningar om det finns en ändring i IP-adress som är associerad med dem.

> [!NOTE]
>
> Om du anger webbplatsbindning **alla otilldelade**, behöver du inte uppdatera den här bindningen postredundans. Även om IP-adressen som är kopplad till en plats inte ändrade postredundans, behöver du inte uppdatera webbplatsbindning. (Kvarhållning av IP-adress beror på de nätverksarkitektur och undernät som har tilldelats till de primära platsen och återställningsplatsen platserna. Uppdatera dem kanske inte fungerar i din organisation.)

![Skärmbild som visar ange SSL-bindning](./media/site-recovery-iis/sslbinding.png)

Om du har associerat IP-adressen till en plats kan du uppdatera alla bindningar för webbplats med den nya IP-adressen. Ändra bindningar för webbplats genom att lägga till en [av IIS-skript för uppdatering av nivå](https://aka.ms/asr-web-tier-update-runbook-classic) efter grupp 3 i återställningsplanen.

#### <a name="update-the-load-balancer-ip-address"></a>Uppdatera IP-adressen för belastningsutjämnaren
Om du har en ARR-dator, att uppdatera IP-adress, lägga till en [IIS ARR redundans skriptet](https://aka.ms/asr-iis-arrtier-failover-script-classic) efter grupp 4.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>SSL-certifikat-bindning för en HTTPS-anslutning
En webbplats kan ha ett tillhörande SSL-certifikat som gör att en säker kommunikation mellan webbservern och användarens webbläsare. Om webbplatsen har en HTTPS-anslutning, och även har en associerad HTTPS plats bindning till IP-adressen för IIS-servern med en bindning för SSL-certifikat, måste du lägga till en ny plats-bindning för certifikatet med IP-adressen för IIS virtuella datorns postredundans.

SSL-certifikatet kan utfärdas mot dessa komponenter:

* Det fullständigt kvalificerade domännamnet för webbplatsen.
* Namnet på servern.
* Ett jokerteckencertifikat för domännamnet.  
* En IP-adress. Om SSL-certifikatet har utfärdats mot IP-adressen för IIS-servern, måste en annan SSL-certifikat utfärdas mot IP-adressen för IIS-servern på Azure-webbplatsen. En ytterligare SSL-bindning för det här certifikatet måste skapas. Därför bör du inte använda ett SSL-certifikat som utfärdats för IP-adress. Det här alternativet används mindre ofta och snart kommer att inaktualiseras i enlighet med nya certifikat authority/webbläsare forum ändringar.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Uppdatera beroendet mellan webbnivån och programmet nivån
Om du har en programspecifika beroende som baseras på IP-adressen för de virtuella datorerna, måste du uppdatera det här beroendet postredundans.

## <a name="run-a-test-failover"></a>Köra ett redundanstest

1. Välj Recovery Services-valvet i Azure-portalen.
2. Välj återställningsplanen som du skapade för IIS-webbservergrupp.
3. Välj **Redundanstestningen**.
4. Om du vill börja testa redundans, Välj återställningspunkten och virtuella Azure-nätverket.
5. Om den sekundära miljön är igång, kan du utföra verifieringar.
6. När verifieringar har slutförts kan välja om du vill rensa redundanstestmiljön, **verifieringar slutföra**.

Mer information finns i [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Köra en redundansväxling

1. Välj Recovery Services-valvet i Azure-portalen.
1. Välj återställningsplanen som du skapade för IIS-webbservergrupp.
1. Välj **redundans**.
1. Välj återställningspunkt för att starta processen för växling vid fel.

Mer information finns i [redundans i Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [replikering av andra program](site-recovery-workload.md) genom att använda Site Recovery.
