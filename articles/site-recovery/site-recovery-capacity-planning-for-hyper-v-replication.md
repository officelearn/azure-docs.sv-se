---
title: "Hyper-V kapacitetsplaneringsverktyget för Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du kör Hyper-V kapacitetsplaneringsverktyget för Azure Site Recovery"
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
ms.date: 10/30/2017
ms.author: nisoneji
ms.openlocfilehash: db790f9dc56605b5b752e7ab797903e32b2fc675
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="hyper-v-capacity-planner-tool-for-site-recovery"></a>Hyper-V kapacitetsplaneringsverktyget för Site Recovery

Som en del av Azure Site Recovery-distributionen måste du ta reda på din replikering och krav på bandbredd. Hyper-V kapacitetsplaneringsverktyget för Site Recovery hjälper dig att göra detta, för replikering av Hyper-V virtuella datorer.

Den här artikeln beskriver hur du kör kapacitetsplaneringsverktyget för Hyper-V. Det här verktyget kan användas tillsammans med informationen i [kapacitetsplanering för Site Recovery](site-recovery-capacity-planner.md).

## <a name="before-you-start"></a>Innan du börjar
Du kör verktyget på en Hyper-V-server eller kluster nod på din primära plats. Hyper-V-värdservrar måste köra verktyget:

* Operativsystem: Windows Server 2012 eller 2012 R2
* Minne: 20 MB (minst)
* : 5 procent processorbelastning (minst)
* Diskutrymme: 5 MB (minst)

Innan du kör verktyget, måste du förbereda den primära platsen. Om du replikerar mellan lokala platser och du vill kontrollera bandbredd, måste du förbereda en replikserver.

## <a name="step-1-prepare-the-primary-site"></a>Steg 1: Förbered den primära platsen

1. Gör en lista över alla Hyper-V virtuella datorer du vill replikera och Hyper-V-värdar/kluster där de är placerade på den primära platsen. Verktyget kan köras på flera fristående värdar eller ett enskilt kluster, men inte båda tillsammans. Det måste köras separat för varje operativsystem, så du bör samla in information om Hyper-V-servrar på följande sätt:

   * Windows Server 2012 fristående servrar
   * Windows Server 2012-kluster
   * Windows Server 2012 R2 fristående servrar
   * Windows Server 2012 R2-kluster
2. Aktivera fjärråtkomst till WMI på alla Hyper-V-värdar och kluster. Kör kommandot på varje server/klustret att kontrollera brandväggsregler och behörigheter har angetts:

        netsh firewall set service RemoteAdmin enable
3. Aktivera övervakning av programprestanda på servrar och kluster, enligt följande:

   * Öppna Windows-brandväggen med den **avancerad säkerhet** snapin-modulen och sedan aktivera inkommande följande regler: **COM + nätverksåtkomst (DCOM-IN)** och alla regler i den **fjärrhantering av händelseloggen grupp**.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>Steg 2: Förbered en replikserver (lokal till lokal replikering)
Du behöver inte göra detta om du replikerar till Azure.

Vi rekommenderar att du ställer in en enskild Hyper-V-värd som en återställningsserver, så att en dummy virtuell dator kan replikeras till den för att kontrollera bandbredd.  Du kan hoppa över detta, men du kommer inte att kunna mäta bandbredd, om du inte gör.

1. Om du vill använda en klusternod som repliken Konfigurera Hyper-V Replica broker:

   * I **Serverhanteraren**öppnar **Klusterhanteraren**.
   * Anslut till klustret, markera klusternamnet och klickar på **åtgärder** > **konfigurera roll** att öppna guiden hög tillgänglighet.
   * I **Välj roll**, klickar du på **Hyper-V Replica Broker**. I guiden anger du en **NetBIOS-namnet** och **IP-adress** som ska användas som anslutningspunkt till klustret (kallas en klientåtkomstpunkt). Den **Hyper-V Replica Broker** ska konfigureras, vilket resulterar i en klientåtkomstpunktens namn som du bör anteckna.
   * Kontrollera att rollen Hyper-V Replica Broker har är online och kan växlas mellan alla noder i klustret. Gör du genom att högerklicka på rollen, peka på **flytta**, och klicka sedan på **Välj nod**. Välj en nod > **OK**.
   * Om du använder certifikatbaserad autentisering, se till att varje klusternod och klientåtkomstpunkten har certifikatet installerat-klienten.
2. Aktivera en replikserver:

   * För ett kluster Öppna Hanteraren för fel, ansluta till klustret och klicka på **roller** > Välj rolltjänster > **replikeringsinställningarna** > **aktivera det här klustret som en replikserver**. Om du använder ett kluster som repliken måste du ha rollen Hyper-V Replica Broker finns på klustret i den primära platsen.
   * Öppna Hyper-V Manager för en fristående server. I den **åtgärder** rutan klickar du på **Hyper-V-inställningar** för den server du vill aktivera och i **replikeringskonfiguration** klickar du på **aktivera den här datorn som en replikserver**.
3. Konfigurera autentisering:

   * I **autentisering och portar**, Välj hur att autentisera den primära servern och autentiseringen portarna. Om du använder ett certifikat klickar du på **Välj certifikat** att välja en. Använda Kerberos om de primära platsen och återställningsplatsen Hyper-V-värdarna finns i samma domän eller i betrodda domäner. Använd certifikat för olika domäner eller en arbetsgrupp-distribution.
   * I **auktorisering och lagring**, Tillåt **alla** autentiserade (primära) servrar för att skicka replikeringsdata till den här replikservern.

     ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)
   * Kör **netsh http show servicestate**, för att kontrollera att lyssnaren körs på porten eller det protokoll du angav:  
4. Konfigurera brandväggar. Under installationen av Hyper-V skapas brandväggsregler för att tillåta trafik på standard-portar (HTTPS på 443, Kerberos på 80). Vill du aktivera dessa regler på följande sätt:
  - Autentisering med datorcertifikat på klustret (443):``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}``
  - Kerberos-autentisering på klustret (80):``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}``
  - Autentisering med datorcertifikat på fristående server:``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"``
  - Kerberos-autentisering på fristående server:``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"``

## <a name="step-3-run-the-capacity-planner-tool"></a>Steg 3: Kör kapacitetsplaneringsverktyget för
När du har förberett din primära plats och konfigurera en återställningsserver kan du köra verktyget.

1. [Hämta](https://www.microsoft.com/download/details.aspx?id=39057) verktyget från Microsoft Download Center.
2. Kör verktyget från en av de primära servrarna (eller en nod från det primära klustret). Högerklicka på filen .exe och välj sedan **kör som administratör**.
3. I **innan du börjar**, ange hur länge du vill samla in data. Vi rekommenderar att du kör verktyget under produktionstider så att data är ombud. Om du bara vill validera nätverksanslutningen, du kan samla in bara en minut.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)
4. I **primära platsinformation**, ange servernamnet eller fullständigt domännamn för en fristående värd eller för ett kluster kan du ange FQDN för klienten accepterar punkt, kluster namn eller en nod i klustret och klicka sedan på **nästa**. Namnet på den server som kör på upptäcks automatiskt. Verktyget fångar upp virtuella datorer som kan övervakas för de angivna servrarna.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)
5. I **replik platsinformation**, om du replikerar till Azure, eller om du replikerar till ett sekundärt datacenter och inte har konfigurerat en replikserver väljer **hoppa över testerna som rör replikeringsplatsen**. Om du replikerar till ett sekundärt datacenter och du har konfigurerat en Repliktyp, anger FQDN för den fristående servern eller klientåtkomstpunkten för klustret, i **Server name (eller) Hyper-V Replica Broker CAP**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)
6. I **utökad replik information**, aktivera **hoppa över de tester som involverar den utökade repliken plats**. Dessa tester stöds inte av Site Recovery.
7. I **Välj virtuella datorer för att replikera**, verktyg som ansluter till servern eller klustret och visar virtuella datorer och diskar som körs på den primära servern, i enlighet med inställningarna du angav i den **primära platsinformation** sidan. Virtuella datorer som redan har aktiverats för replikering eller som inte körs, visas inte. Välj de virtuella datorerna som du vill samla in mått. Markera de virtuella hårddiskarna automatiskt samlar in data för virtuella datorer för.
8. Om du har konfigurerat en replikserver eller ett kluster i **nätverksinformation**anger den ungefärliga WAN-bandbredd som du tror används mellan den primära servern och repliken platser och Välj certifikat om du har konfigurerat certifikatautentisering.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)
9. I **sammanfattning**, kontrollera inställningarna och klickar på **nästa** att börja samla in mått. Verktyget förlopp och status visas på den **beräkna kapacitet** sidan. När verktyget är klar klickar du på **Visa rapport** du visar utdata. Som standard, rapporter och loggar lagras i **%systemdrive%\Users\Public\Documents\Capacity Planner**.

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

## <a name="step-4-interpret-the-results"></a>Steg 4: Tolka resultaten

Här är viktigt mått. Du kan ignorera mått som inte visas här. De är inte relevanta för Site Recovery.

### <a name="on-premises-to-on-premises-replication"></a>Lokal till lokal replikering

* Effekten av replikering på den primära värddatorn beräkning, minne
* Effekten av replikering på primärt recovery värdar diskutrymme för lagring, IOPS
* Totala bandbredd som krävs för deltareplikering (Mbps)
* Observerade nätverksbandbredden mellan den primära värddatorn och återställningsvärden (Mbps)
* Förslag för bästa antalet aktiva parallella överföringar mellan två värdar/kluster

### <a name="on-premises-to-azure-replication"></a>Lokalt till Azure-replikering

* Effekten av replikering på den primära värddatorn beräkning, minne
* Effekten av replikering på den primära värddatorn disk lagringsutrymme IOPS
* Totala bandbredd som krävs för deltareplikering (Mbps)

## <a name="more-resources"></a>Fler resurser
* Läsa dokumentet som medföljer verktyget Hämta detaljerad information om verktyget.
* Titta på en genomgång av verktyget i Keith Mayers [TechNet-blogg](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx).
* [Hämta resultatet](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) av våra prestandatestning för lokal till lokal Hyper-V-replikering

## <a name="next-steps"></a>Nästa steg

När du är klar kapacitetsplanering kan du börja distribuera Site Recovery:

* [Replikera virtuella Hyper-V-datorer i VMM-moln till Azure](site-recovery-vmm-to-azure.md)
* [Replikera virtuella Hyper-V-datorer (utan VMM) till Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replikera virtuella Hyper-V-datorer mellan VMM-platser](site-recovery-vmm-to-vmm.md)
