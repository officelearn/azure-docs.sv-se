---
title: Konfigurera haveriberedskap för en IIS-webbapp med Azure Site Recovery
description: Lär dig hur du replikerar virtuella IIS-webbgruppsdatorer med Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 513a0f28fc03cbf24e35112245c9756d5ce00783
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954670"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Konfigurera haveriberedskap för ett IIS-baserat webbprogram på flera nivåer

Programvara är motorn i företagets produktivitet i en organisation. Olika webbapplikationer kan tjäna olika syften i en organisation. Vissa program, till exempel program som används för lönebearbetning, ekonomiska program och kundinriktade webbplatser, kan vara avgörande för en organisation. För att förhindra förlust av produktivitet är det viktigt för organisationen att ha dessa program igång kontinuerligt. Ännu viktigare är att ha dessa program konsekvent tillgängliga kan bidra till att förhindra skador på varumärket eller bilden av organisationen.

Kritiska webbprogram ställs vanligtvis in som program på flera nivåer: webben, databasen och programmet finns på olika nivåer. Förutom att spridas över olika nivåer kan programmen också använda flera servrar på varje nivå för att läsa in trafiken. Dessutom kan mappningarna mellan olika nivåer och på webbservern baseras på statiska IP-adresser. Vid redundans måste vissa av dessa mappningar uppdateras, särskilt om flera webbplatser är konfigurerade på webbservern. Om webbprogram använder SSL måste du uppdatera certifikatbindningar.

Traditionella återställningsmetoder som inte baseras på replikering innebär säkerhetskopiering av olika konfigurationsfiler, registerinställningar, bindningar, anpassade komponenter (COM eller .NET), innehåll och certifikat. Filer återställs genom en uppsättning manuella steg. De traditionella återställningsmetoderna för säkerhetskopiering och återställning av filer manuellt är besvärliga, felbenägna och inte skalbara. Du kan till exempel lätt glömma att säkerhetskopiera certifikat. Efter redundans, du har inget annat val än att köpa nya certifikat för servern.

En bra lösning för haveriberedskap stöder modellering av återställningsplaner för komplexa programarkitekturer. Du bör också kunna lägga till anpassade steg i återställningsplanen för att hantera programmappningar mellan nivåer. Om det finns en katastrof, programmappningar ger en enda klick, säker-shot lösning som hjälper till att leda till en lägre RTO.

I den hÃ¤r artikeln beskrivs hur du skyddar ett webbprogram som baseras på Internet Information Services (IIS) med hjälp av [Azure Site Recovery](site-recovery-overview.md). Artikeln beskriver metodtips för att replikera ett IIS-baserat webbprogram med tre nivåer till Azure, hur du utför en borrmaskin för haveriberedskap och hur du växlar över programmet till Azure.

## <a name="prerequisites"></a>Krav

Innan du börjar bör du se till att du vet hur du utför följande uppgifter:

* [Replikera en virtuell dator till Azure](vmware-azure-tutorial.md)
* [Utforma ett återställningsnätverk](site-recovery-network-design.md)
* [Gör en testväxling till Azure](site-recovery-test-failover-to-azure.md)
* [Gör en redundans till Azure](site-recovery-failover.md)
* [Replikera en domänkontrollant](site-recovery-active-directory.md)
* [Replikera SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Distributionsmönster
Ett IIS-baserat webbprogram följer vanligtvis något av följande distributionsmönster:

**Distributionsmönster 1**

En IIS-baserad webbgrupp med ARR (Application Request Routing), en IIS-server och SQL Server.

![Diagram över en IIS-baserad webbgrupp som har tre nivåer](./media/site-recovery-iis/deployment-pattern1.png)

**Distributionsmönster 2**

En IIS-baserad webbgrupp med ARR, en IIS-server, en programserver och SQL Server.

![Diagram över en IIS-baserad webbgrupp som har fyra nivåer](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery-stöd

I exemplen i den här artikeln använder vi virtuella VMware-datorer med IIS 7.5 på Windows Server 2012 R2 Enterprise. Eftersom site recovery-replikering inte är programspecifik förväntas rekommendationerna i den här artikeln gälla i scenarierna i följande tabell och för olika versioner av IIS.

### <a name="source-and-target"></a>Källa och mål

Scenario | Till en sekundär plats | Till Azure
--- | --- | ---
Hyper-V | Ja | Ja
VMware | Ja | Ja
Fysisk server | Inga | Ja
Azure|Ej tillämpligt|Ja

## <a name="replicate-virtual-machines"></a>Replikera virtuella datorer

Om du vill börja replikera alla virtuella IIS-webbgruppsdatorer till Azure följer du anvisningarna i [Test redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

Om du använder en statisk IP-adress kan du ange den IP-adress som du vill att den virtuella datorn ska ta. Om du vill ange IP-adressen går du till Target**IP** **för beräknings- och nätverksinställningar** > .

![Skärmbild som visar hur du anger mål-IP i fönstret Beräkna och nätverksrutan För webbplatsåterställning](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan
En återställningsplan stöder sekvensering av olika nivåer i ett program på flera nivåer under en redundansväxling. Sekvensering hjälper till att upprätthålla programmets konsekvens. När du skapar en återställningsplan för ett webbprogram på flera nivåer slutför du stegen som beskrivs i [Skapa en återställningsplan med hjälp av Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i redundansgrupper
Ett typiskt IIS-webbprogram på flera nivåer består av följande komponenter:
* En databasnivå som har virtuella SQL-datorer.
* Webbnivån, som består av en IIS-server och en programnivå. 

Lägg till virtuella datorer i olika grupper baserat på nivån:

1. Skapa en återställningsplan. Lägg till virtuella datorer på databasnivå under grupp 1. Detta säkerställer att virtuella datorer på databasnivå stängs av sist och tas upp först.
1. Lägg till virtuella datorer på programnivå under Grupp 2. Detta säkerställer att virtuella datorer på programnivå tas upp efter att databasnivån har tagits upp.
1. Lägg till virtuella datorer på webbnivå i grupp 3. Detta säkerställer att virtuella datorer på webbnivå tas upp efter att programnivån har tagits upp.
1. Lägg till virtuella belastningsbalansdatorer i grupp 4. Detta säkerställer att virtuella belastningsutjämna datorer tas upp efter att webbnivån har tagits upp.

Mer information finns i [Anpassa återställningsplanen](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Lägga till ett skript i återställningsplanen
För att IIS-webbgruppen ska fungera korrekt kan du behöva göra vissa åtgärder på virtuella Azure-datorer efter redundans eller under en testväxling. Du kan automatisera vissa åtgärder efter redundans. Du kan till exempel uppdatera DNS-posten, ändra en platsbindning eller ändra en anslutningssträng genom att lägga till motsvarande skript i återställningsplanen. [Lägga till ett VMM-skript i en återställningsplan](site-recovery-how-to-add-vmmscript.md) beskriver hur du ställer in automatiserade uppgifter med hjälp av ett skript.

#### <a name="dns-update"></a>DNS-uppdatering
Om DNS är konfigurerat för dynamisk DNS-uppdatering uppdaterar virtuella datorer vanligtvis DNS med den nya IP-adressen när de startar. Om du vill lägga till ett explicit steg för att uppdatera DNS med de nya IP-adresserna för de virtuella datorerna lägger du till ett [skript för att uppdatera IP i DNS](https://aka.ms/asr-dns-update) som en åtgärd efter redundansen för återställningsplangrupper.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Anslutningssträng i ett programs web.config
Anslutningssträngen anger den databas som webbplatsen kommunicerar med. Om anslutningssträngen bär namnet på den virtuella datorn för databasen behövs inga ytterligare steg efter redundansen. Programmet kan automatiskt kommunicera med databasen. Om IP-adressen för den virtuella datorn för databasen behålls behöver den inte uppdatera anslutningssträngen. 

Om anslutningssträngen refererar till databasens virtuella dator med hjälp av en IP-adress måste den uppdateras efter redundans. Följande anslutningssträng pekar till exempel på databasen med IP-adressen 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Om du vill uppdatera anslutningssträngen på webbnivån lägger du till ett [IIS-anslutningsuppdateringsskript](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) efter grupp 3 i återställningsplanen.

#### <a name="site-bindings-for-the-application"></a>Platsbindningar för programmet
Varje webbplats består av bindande information. Bindningsinformationen omfattar typen av bindning, IP-adressen där IIS-servern lyssnar på begäranden för platsen, portnumret och värdnamnen för platsen. Under redundansen kan du behöva uppdatera dessa bindningar om det finns en ändring i IP-adressen som är associerad med dem.

> [!NOTE]
>
> Om du anger webbplatsbindningen till **Alla som inte tilldelats**behöver du inte uppdatera bindningen efter redundansen. Om IP-adressen som är associerad med en webbplats inte ändras efter redundans behöver du inte uppdatera webbplatsbindningen. (Bevarandet av IP-adressen beror på nätverksarkitekturen och undernäten som tilldelats de primära platserna och återställningsplatserna. Det kanske inte är möjligt att uppdatera dem för din organisation.)

![Skärmbild som visar inställningen SSL-bindning](./media/site-recovery-iis/sslbinding.png)

Om du har associerat IP-adressen med en plats uppdaterar du alla platsbindningar med den nya IP-adressen. Om du vill ändra webbplatsbindningar lägger du till ett [IIS-webbnivåuppdateringsskript](https://aka.ms/asr-web-tier-update-runbook-classic) efter grupp 3 i återställningsplanen.

#### <a name="update-the-load-balancer-ip-address"></a>Uppdatera IP-adressen för belastningsutjämnaren
Om du har en virtuell ARR-dator lägger du till ett [IIS ARR-redundanskript](https://aka.ms/asr-iis-arrtier-failover-script-classic) efter grupp 4 för att uppdatera IP-adressen.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>SSL-certifikatbindning för en HTTPS-anslutning
En webbplats kan ha ett associerat SSL-certifikat som säkerställer en säker kommunikation mellan webbservern och användarens webbläsare. Om webbplatsen har en HTTPS-anslutning och även har en associerad HTTPS-platsbindning till IP-adressen för IIS-servern med en SSL-certifikatbindning, måste du lägga till en ny platsbindning för certifikatet med IP-adressen för IIS-virtuella datorn efter redundansen.

SSL-certifikatet kan utfärdas mot dessa komponenter:

* Webbplatsens fullständiga domännamn.
* Namnet på servern.
* Ett jokerteckencertifikat för domännamnet.  
* En IP-adress. Om SSL-certifikatet utfärdas mot IP-adressen för IIS-servern måste ett annat SSL-certifikat utfärdas mot IP-adressen för IIS-servern på Azure-platsen. En ytterligare SSL-bindning för det här certifikatet måste skapas. På grund av detta rekommenderar vi att du inte använder ett SSL-certifikat som utfärdats mot IP-adressen. Detta alternativ är mindre utbredd och kommer snart att föråldras i enlighet med nya ändringar certifikatutfärdare / webbläsare forum.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Uppdatera beroendet mellan webbnivån och programnivån
Om du har ett programspecifikt beroende som baseras på IP-adressen för de virtuella datorerna måste du uppdatera det här beroendet efter redundansen.

## <a name="run-a-test-failover"></a>Köra ett redundanstest

1. Välj ditt Recovery Services-valv i Azure-portalen.
2. Välj den återställningsplan som du skapade för IIS-webbgruppen.
3. Välj **Testa redundans**.
4. Om du vill starta testundanundansprocessen väljer du återställningspunkten och det virtuella Azure-nätverket.
5. När den sekundära miljön är uppe kan du utföra valideringar.
6. När valideringarna är klara väljer du **Valideringar slutförs**för att rensa testundansmiljön .

Mer information finns i [Testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Köra en redundansväxling

1. Välj ditt Recovery Services-valv i Azure-portalen.
1. Välj den återställningsplan som du skapade för IIS-webbgruppen.
1. Välj **Redundans**.
1. Om du vill starta redundansprocessen väljer du återställningspunkten.

Mer information finns [i Redundans i Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [att replikera andra program](site-recovery-workload.md) med hjälp av Site Recovery.
