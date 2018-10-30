---
title: Konfigurera haveriberedskap för en flerskiktade IIS-baserade webbprogram med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du replikerar IIS web grupp virtuella datorer med Azure Site Recovery.
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: nisoneji
ms.openlocfilehash: bac995829e90fea35eebf5e5e57a0ffb85bbba0c
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211833"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Konfigurera haveriberedskap för en flerskiktade IIS-baserade webbprogram

Programvara är motorn för verksamhetsproduktiviteten i en organisation. Olika webbprogram kan ha olika syften i en organisation. Vissa program, t.ex. program som används för löneuppgifter bearbetning, affärsprogram och kundinriktade webbplatser, kan vara viktiga för en organisation. Det är viktigt för organisationen ha dessa program kontinuerligt och drift för att förhindra förlust av produktivitet. Ännu viktigare är kan att ha dessa program som är konsekvent tillgängliga förhindra skada varumärke eller bild av organisationen.

Affärskritiska webbprogram vanligtvis är inställda som flernivåprogram: webb-, databas- och program som finns på olika nivåer. Förutom som sprids över de olika nivåerna, kan programmen också använda flera servrar i varje nivå för att belastningsutjämna trafiken. Mappningar mellan olika nivåer och på webbservern kan dessutom vara baserad på statiska IP-adresser. Vid redundans måste några av dessa mappningar uppdateras, särskilt om flera webbplatser som är konfigurerade på webbservern. Om webbprogram använder SSL, måste du uppdatera certifikatbindningar.

Traditionella återställningsmetoder som inte är baserade på replikering handla om säkerhetskopiering av olika configuration-filer, registerinställningar, bindningar, anpassade komponenter (COM eller .NET), innehåll och certifikat. Filer har återställts via en mängd manuella steg. Traditionella recovery-metoder för att säkerhetskopiera och återställa filer manuellt är besvärlig, felbenägna och inte skalbar. Du kan till exempel enkelt glömmer att säkerhetskopiera certifikat. Efter redundansen, är du kvar med inget val men köpa nya certifikat för servern.

En bra lösning för haveriberedskap har stöd för modellering återställning planer för komplexa arkitekturer. Du bör också att kunna lägga till anpassade steg i återställningsplan för att hantera mappning mellan nivåer. Om det finns en katastrof, ger programmappningar en enda musklick, att inleveransen lösning som hjälper till att leda till en lägre RTO.

Den här artikeln beskriver hur du skyddar ett webbprogram som baseras på Internet Information Services (IIS) med hjälp av [Azure Site Recovery](site-recovery-overview.md). Artikeln beskriver Metodtips för att replikera en trelagers-, IIS-baserad webbapp till Azure, hur du gör ett programåterställningstest och hur du växlar över programmet till Azure.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du se till att du vet hur du utföra följande uppgifter:

* [Replikera en virtuell dator till Azure](vmware-azure-tutorial.md)
* [Utforma återställningsnätverk](site-recovery-network-design.md)
* [Gör ett redundanstest till Azure](site-recovery-test-failover-to-azure.md)
* [Gör en redundansväxling till Azure](site-recovery-failover.md)
* [Replikera en domänkontrollant](site-recovery-active-directory.md)
* [Replikera SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Mönster för distribution
Ett IIS-baserade webbprogram följer vanligtvis något av följande distribution mönster:

**Mönster för distribution 1**

En IIS-baserade webbservergrupp med Routning av programbegäran (ARR), en IIS-server och SQL Server.

![Diagram över en IIS-baserad webbplats-servergrupp med tre nivåer](./media/site-recovery-iis/deployment-pattern1.png)

**Mönster för distribution 2**

En IIS-baserade webbservergrupp med ARR, en IIS-server, en programserver och SQL Server.

![Diagram över en IIS-baserade webbservergrupp som har fyra nivåer](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery-stöd

I exemplen i den här artikeln använder vi virtuella VMware-datorer med IIS 7.5 på Windows Server 2012 R2 Enterprise. Eftersom Site Recovery-replikering inte är programspecifika kan förväntas rekommendationerna i den här artikeln gäller i de scenarier som beskrivs i följande tabell och för olika versioner av IIS.

### <a name="source-and-target"></a>Källa och mål

Scenario | Till en sekundär plats | Till Azure
--- | --- | ---
Hyper-V | Ja | Ja
VMware | Ja | Ja
Fysisk server | Nej | Ja
Azure|Ej tillämpligt|Ja

## <a name="replicate-virtual-machines"></a>Replikera virtuella datorer

Om du vill börja replikera alla IIS web grupp virtuella datorer till Azure, följer du riktlinjerna i [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

Om du använder en statisk IP-adress kan ange du IP-adressen som du vill att den virtuella datorn ska börja. Ange IP-adressen genom att gå till **beräkning och nätverk inställningar** > **mål-IP**.

![Skärmbild som visar hur du ställer in mål-IP i fönstret Site Recovery-beräkning och nätverk](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan
En återställningsplan stöder Sekvenseringen av olika nivåer i ett flerskiktat program under en redundansväxling. Sekvensering hjälper till att upprätthålla programkonsekvens. När du skapar en återställningsplan för ett webbprogram med flera nivåer, fullständig stegen som beskrivs i [skapa en återställningsplan med hjälp av Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i grupper för växling vid fel
En typisk flerskiktat IIS-webbprogram som består av följande komponenter:
* En databasnivå som har virtuella datorer med SQL.
* Webbnivå, som består av en IIS-server och en programnivå. 

Lägg till virtuella datorer i olika grupper baserat på vilken nivå:

1. Skapa en återställningsplan. Lägg till databas nivån virtuella datorer under grupp 1. Detta säkerställer att databasen virtuella skiktdatorer är stänga av senaste och gav oss först.
1. Lägg till program nivån virtuella datorer under Grupp2. Detta säkerställer att programmet nivån virtuella datorer förs upp efter databasnivån har trätt.
1. Lägg till virtuella datorer för web-nivå i grupp 3. Detta garanterar att web nivån virtuella datorer förs upp efter programnivån har trätt.
1. Lägg till belastningen belastningsutjämna virtuella datorer i gruppen 4. Detta säkerställer att belastningen belastningsutjämna virtuella datorer förs upp efter webbnivån har trätt.

Mer information finns i [anpassa återställningsplanen](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Lägga till ett skript i en återställningsplan
För IIS-webbservergrupp ska fungera korrekt, kan du behöva göra vissa åtgärder på virtuella Azure-datorer efter en redundansväxling eller under ett redundanstest. Du kan automatisera vissa åtgärder efter en redundansväxling. Du kan till exempel uppdatera DNS-posten, ändra en bindning för webbplats eller ändra en anslutningssträng genom att lägga till motsvarande skript till i återställningsplanen. [Lägg till ett VMM-skript i en återställningsplan](site-recovery-how-to-add-vmmscript.md) beskriver hur du konfigurerar automatiserade åtgärder med hjälp av ett skript.

#### <a name="dns-update"></a>DNS-uppdatering
Om DNS är konfigurerat för dynamisk DNS-uppdatering, uppdatera virtuella datorer vanligtvis DNS med den nya IP-adressen när de startar. Om du vill lägga till ett explicit steg för att uppdatera DNS med de nya IP-adresserna för de virtuella datorerna, lägga till en [skript för att uppdatera IP-adress i DNS](https://aka.ms/asr-dns-update) som en postredundant åtgärd på recovery planeringsgrupper.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Anslutningssträngen i ett programs web.config
Anslutningssträngen anger den databas som webbplatsen kommunicerar med. Om anslutningssträngen har namnet på den virtuella datorn för databas, finns inga ytterligare steg krävs efter en redundansväxling. Programmet kan automatiskt kommunicera med databasen. Om IP-adressen för den virtuella datorn för databasen sparas att det inte uppdatera anslutningssträngen. 

Om anslutningssträngen refererar till den virtuella datorn för databasen med hjälp av en IP-adress, måste den vara uppdaterade efter en redundansväxling. Till exempel-följande kopplingspunkter av strängen till databasen med IP 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Om du vill uppdatera anslutningssträngen på webbnivå, lägger du till en [IIS anslutning uppdateringsskript](https://aka.ms/asr-update-webtier-script-classic) efter grupp 3 i återställningsplanen.

#### <a name="site-bindings-for-the-application"></a>Bindningar för webbplats för programmet
Varje plats består av bindningsinformation. Bindningsinformationen innehåller typen av bindning, IP-adressen som IIS-servern lyssnar efter förfrågningar för platsen, portnumret och värdnamnen för webbplatsen. Du kan behöva uppdatera dessa bindningar om det finns en ändring i IP-adressen som är associerat med dem under redundansväxlingen.

> [!NOTE]
>
> Om du anger webbplatsbindning till **alla otilldelade**, du behöver inte uppdatera den här bindningen efter en redundansväxling. Även om IP-adressen som är associerade med en plats inte är ändrade efter en redundansväxling, behöver du inte att uppdatera webbplatsbindning. (Kvarhållning av IP-adress beror på nätverksarkitektur och undernät som tilldelats de primära och återställningsplatserna. Uppdaterar dem kanske inte fungerar i din organisation.)

![Skärmbild som visar att SSL-bindning](./media/site-recovery-iis/sslbinding.png)

Om du har associerat IP-adress med en plats kan du uppdatera alla bindningar för webbplats med den nya IP-adressen. Om du vill ändra bindningar för webbplats, lägger du till en [IIS web nivån uppdateringsskript](https://aka.ms/asr-web-tier-update-runbook-classic) efter grupp 3 i återställningsplanen.

#### <a name="update-the-load-balancer-ip-address"></a>Uppdatera IP-adressen för load balancer
Om du har en ARR-dator, att uppdatera IP-adress, lägga till en [IIS ARR redundansskriptet](https://aka.ms/asr-iis-arrtier-failover-script-classic) efter grupp 4.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>SSL-certifikatbindning för en HTTPS-anslutning
En webbplats kan ha en associerad SSL-certifikat som hjälper till att säkerställa en säker kommunikation mellan webbservern och användarens webbläsare. Om webbplatsen har en HTTPS-anslutning, och även har en associerad HTTPS webbplatsbindning till IP-adressen för IIS-servern med ett SSL-certifikatbindning, måste du lägga till en ny bindning för webbplats för certifikatet med IP-adressen för IIS virtuella datorn efter en redundansväxling.

SSL-certifikatet kan utfärdas mot dessa komponenter:

* Det fullständigt kvalificerade domännamnet på webbplatsen.
* Namnet på servern.
* Ett jokerteckencertifikat för domännamnet.  
* En IP-adress. Om SSL-certifikatet utfärdas mot IP-adressen för IIS-servern, måste en annan SSL-certifikatet utfärdas mot IP-adressen för den IIS-servern på Azure-webbplatsen. En ytterligare SSL-bindning för det här certifikatet måste skapas. Därför bör du inte använda ett SSL-certifikat som utfärdas mot IP-adressen. Det här alternativet används mindre ofta och snart upphör att gälla i enlighet med nya certificate authority/webbläsares forum ändringar.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Uppdatera beroendet mellan webbnivån och appnivån
Om du har beroende av ett programspecifika som baseras på IP-adressen för de virtuella datorerna, måste du uppdatera det här beroendet efter en redundansväxling.

## <a name="run-a-test-failover"></a>Köra ett redundanstest

1. Välj Recovery Services-valvet i Azure-portalen.
2. Välj återställningsplan som du skapade för IIS-webbservergrupp.
3. Välj **Redundanstest**.
4. Välj återställningspunkten och Azure-nätverket om du vill börja testa redundansprocessen.
5. Om den sekundära miljön är igång, kan du utföra verifieringar.
6. När verifieringar har slutförts, för att rensa redundanstestmiljön, Välj **verifieringar slutföra**.

Mer information finns i [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Köra en redundansväxling

1. Välj Recovery Services-valvet i Azure-portalen.
1. Välj återställningsplan som du skapade för IIS-webbservergrupp.
1. Välj **redundans**.
1. Välj återställningspunkten för att starta redundansprocessen.

Mer information finns i [redundans i Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [replikering av andra program](site-recovery-workload.md) med hjälp av Site Recovery.
