---
title: "Hyper-V-kapacitetsplanering verktyget för Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du kör Hyper-V kapacitetsplaneringsverktyg för Azure Site Recovery"
services: site-recovery
documentationcenter: na
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 2bc3832f-4d6e-458d-bf0c-f00567200ca0
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: nisoneji
ms.openlocfilehash: 80c3dcb65bd74bcfa3acc5f12dd5c45cd1c06455
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="hyper-v-capacity-planning-tool-for-site-recovery"></a>Hyper-V-kapacitetsplanering verktyget för Site Recovery

En ny utökad version av [Azure Site Recovery distribution Planner för Hyper-V till Azure-distribution](site-recovery-hyper-v-deployment-planner.md) är nu tillgänglig. Ersätter verktyget gamla. Använd verktyget ny för att planera din distribution. Verktyget ger följande riktlinjer:

* VM är behörig assessment, baserat på antalet diskar, diskstorlek, IOPS, omsättning och några VM-egenskaper
* Nätverkets bandbredd måste jämfört med Återställningspunktsmål assessment
* Krav för Azure-infrastrukturen
* Lokala infrastrukturkrav
* Den inledande replikeringen batchbearbetning vägledning
* Uppskattad totala disaster recovery kostnad för Azure

Som en del av Azure Site Recovery-distributionen måste du ta reda på din replikering och krav på bandbredd. Verktyget Hyper-V kapacitetsplanering för Site Recovery hjälper dig att avgöra kraven för Hyper-V VM-replikering.

Den här artikeln beskriver hur du kör Hyper-V-verktyget för kapacitetsplanering. Du använder verktyget med informationen i [kapacitetsplanering för Site Recovery](site-recovery-capacity-planner.md).

## <a name="before-you-start"></a>Innan du börjar
Du kör verktyget på en Hyper-V-server eller kluster nod på din primära plats. Om du vill köra verktyget behöver Hyper-V-värdservrar:

* Operativsystem: Windows Server 2012 eller 2012 R2
* Minne: 20 MB (minst)
* : 5 procent processorbelastning (minst)
* Diskutrymme: 5 MB (minst)

Innan du kör verktyget, måste du förbereda den primära platsen. Om du replikerar mellan två lokala platser och du vill kontrollera bandbredd, måste du förbereda en replikserver.

## <a name="step-1-prepare-the-primary-site"></a>Steg 1: Förbered den primära platsen

1. Gör en lista över alla Hyper-V-datorer som du vill replikera på den primära platsen. Lista Hyper-V-värdar eller kluster placering. Verktyget kan köras för flera fristående värdar eller ett enskilt kluster, men inte båda tillsammans. Det måste också köras separat för varje operativsystem. Samla in följande information om Hyper-V-servrar:

   * Windows Server 2012 standalone servers
   * Windows Server 2012-kluster
   * Windows Server 2012 R2 standalone servers
   * Windows Server 2012 R2-kluster

2. Aktivera fjärråtkomst till Windows Management Instrumentation på alla Hyper-V-värdar och kluster. Kör det här kommandot på varje server eller kluster för att se till att brandväggsreglerna och behörigheterna för användaren:

        netsh firewall set service RemoteAdmin enable
3. Aktivera övervakning av programprestanda på servrar och kluster på följande sätt:

   a. Öppna Windows-brandväggen med den **avancerad säkerhet** snapin-modulen. 
   
   b. Välj en regel för inkommande **COM + nätverksåtkomst (DCOM-IN)**. Välj alla regler i den **fjärrhantering av händelseloggen** grupp.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>Steg 2: Förbered en replikserver (lokal till lokal replikering)
Om du replikerar till Azure, behöver du inte göra det här steget.

Vi rekommenderar att du ställer in en enskild Hyper-V-värd som en återställningsserver så att en dummy virtuell dator kan replikeras till den för att kontrollera bandbredd. Du kan hoppa över det här steget, men du kan mäta bandbredd om du inte gör.

1. Om du vill använda en klusternod som repliken Konfigurera Hyper-V Replica Broker:

   a. I **Serverhanteraren**öppnar **Klusterhanteraren**.

   b. Anslut till klustret och markera klusternamnet. Välj **åtgärder** > **konfigurera roll** att öppna guiden hög tillgänglighet.

   c. I **Välj roll**väljer **Hyper-V Replica Broker**. I guiden anger du ett namn för **NetBIOS-namnet**. Ange en adress för **IP-adress** som ska användas som anslutningspunkt till klustret (kallas en klientåtkomstpunkt). Den **Hyper-V Replica Broker** är konfigurerad, vilket resulterar i en klientåtkomstpunktens namn som du bör anteckna.

   d. Kontrollera att rollen Hyper-V Replica Broker har är online och kan växlas mellan alla noder i klustret. Högerklicka på rollen och välj **flytta** > **Välj nod**. Välj en nod och välj sedan **OK**.

   e. Om du använder certifikatbaserad autentisering, se till att varje klusternod och klientåtkomstpunkten har certifikatet installerat-klienten.

2. Om du vill aktivera en replikserver, gör du så här:

   a. Ett kluster, öppna **fel Klusterhanterare** och ansluta till klustret. Välj **roller**, och välj sedan en roll. Välj **replikeringsinställningarna** > **aktivera det här klustret som en replikserver**. Om du använder ett kluster som repliken måste du ha rollen Hyper-V Replica Broker finns på klustret i den primära platsen.

   b. En fristående server, öppna **Hyper-V Manager**. I den **åtgärder** väljer **Hyper-V-inställningar** för den server som du vill aktivera. I **replikeringskonfiguration**väljer **aktivera den här datorn som en replikserver**.

3. Om du vill konfigurera autentisering måste du göra följande:

   a. Under **autentisering och portar**, Välj hur att autentisera den primära servern och autentiseringen portarna. Om du använder ett certifikat väljer **Välj certifikat** att välja en. Använda Kerberos om de primära platsen och återställningsplatsen Hyper-V-värdarna finns i samma domän eller i betrodda domäner. Använd certifikat för olika domäner eller en arbetsgrupp-distribution.

   b. Under **auktorisering och lagring**, Tillåt **alla** autentiserade (primära) servrar för att skicka replikeringsdata till den här replikservern.

   ![Inställningar för replikering](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

   c. Kontrollera att lyssnaren körs för protokoll och port som du angav att köra **netsh http show servicestate**. 

4. Konfigurera brandväggar. Under installationen av Hyper-V skapas brandväggsregler för att tillåta trafik på standard-portarna (HTTPS på 443) och Kerberos på 80. Vill du aktivera dessa regler på följande sätt:

    - Autentisering med datorcertifikat på klustret (443):``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}``
    - Kerberos-autentisering på klustret (80):``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}``
    - Autentisering med datorcertifikat på fristående server:``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"``
    - Kerberos-autentisering på fristående server:``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"``

## <a name="step-3-run-the-capacity-planning-tool"></a>Steg 3: Kör verktyget för kapacitetsplanering
När du förbereder din primära plats och ställa in en återställningsserver kan köra du verktyget.

1. [Hämta](https://www.microsoft.com/download/details.aspx?id=39057) verktyget från Microsoft Download Center.

2. Kör verktyget från en av de primära servrarna eller en nod från det primära klustret. Högerklicka på filen .exe och välj sedan **kör som administratör**.

3. I **innan du börjar**, ange hur länge du vill samla in data. Vi rekommenderar att du kör verktyget under produktionstider så att data är ombud. Om du vill validera endast nätverksanslutningen du kan samla in bara en minut.

    ![Innan du börjar](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. I **primära platsinformation**, ange servernamnet eller fullständigt domännamn för en fristående värd. Ange FQDN för klientåtkomstpunkten, klusternamn eller en nod i klustret för ett kluster. Välj **Nästa**. Namnet på den server som kör på upptäcks automatiskt. Verktyget fångar upp virtuella datorer som kan övervakas för de angivna servrarna.

    ![Primär platsinformation](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. I **replik platsinformation**, om du replikerar till Azure eller om du replikerar till ett sekundärt datacenter och inte har konfigurerat en replikserver väljer **hoppa över testerna som rör replikeringsplatsen**. Om du replikerar till ett sekundärt datacenter och du ställer in en Repliktyp, anger du FQDN för den fristående servern eller klientåtkomstpunkten för klustret i **Server name (eller) Hyper-V Replica Broker CAP**.

    ![Information för repliker plats](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. I **utökad replik information**väljer **hoppa över de tester som involverar den utökade repliken plats**. Dessa tester stöds inte av Site Recovery.

7. I **Välj virtuella datorer för att replikera**, verktyget ansluter till servern eller klustret. Den visar virtuella datorer och diskar som körs på den primära servern, baserat på inställningarna du angav i den **primära platsinformation** sidan. Virtuella datorer som redan har aktiverats för replikering eller som inte kör visas inte. Välj de virtuella datorerna som du vill samla in mått. Markera de virtuella hårddiskarna automatiskt samlar in data för de virtuella datorerna för.

8. Om du har konfigurerat en replikserver eller ett kluster i **nätverksinformation**, ange den ungefärliga WAN-bandbredden som ska användas mellan platser för den primära servern och repliken. Om du har konfigurerat autentisering med datorcertifikat, Välj certifikaten.

    ![Nätverksinformation](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

9. I **sammanfattning**, kontrollera inställningarna. Välj **nästa** att börja samla in mått. Verktyget förlopp och status visas på den **beräkna kapacitet** sidan. När verktyget är klar väljer du **Visa rapport** du visar utdata. Som standard, rapporter och loggar lagras i **%systemdrive%\Users\Public\Documents\Capacity Planner**.

   ![Beräkna kapacitet](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

## <a name="step-4-interpret-the-results"></a>Steg 4: Tolka resultaten

Här är viktigt mått. Du kan ignorera mått som inte visas här. De är inte relevanta för Site Recovery.

### <a name="on-premises-to-on-premises-replication"></a>Lokal till lokal replikering

* Effekten av replikering på den primära värddatorn beräknings- och minne
* Effekten av replikering på primär värden och återställningsvärden diskutrymme för lagring och IOPS
* Totala bandbredd som krävs för deltareplikering (Mbps)
* Observerade nätverksbandbredden mellan den primära värddatorn och återställningsvärden (Mbps)
* Förslag för bästa antalet aktiva parallella överföringar mellan två värdar eller kluster

### <a name="on-premises-to-azure-replication"></a>Lokalt till Azure-replikering

* Effekten av replikering på den primära värddatorn beräknings- och minne
* Effekten av replikering på den primära värddatorn diskutrymme för lagring och IOPS
* Totala bandbredd som krävs för deltareplikering (Mbps)

## <a name="more-resources"></a>Fler resurser

* Mer information om verktyget läsa dokumentet som medföljer verktyget hämtningen.
* Titta på en genomgång av verktyget i Keith Mayers [TechNet-blogg](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx).
* [Hämta resultatet](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) av prestandatest för lokal till lokal Hyper-V-replikering.

## <a name="next-steps"></a>Nästa steg

När du är klar med kapacitetsplanering kan du distribuera Site Recovery:
* [Replikera virtuella Hyper-V-datorer i VMM-moln till Azure](site-recovery-vmm-to-azure.md)
* [Replikera virtuella Hyper-V-datorer (utan VMM) till Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replikera virtuella Hyper-V-datorer mellan VMM-platser](site-recovery-vmm-to-vmm.md)
