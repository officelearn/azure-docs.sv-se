---
title: SaaS SQL-app video
description: Den här artikeln indexerar olika tidpunkter i vår 81 minuter video om SaaS DB hyresrätt app design, från Ignite konferensen hölls 11 oktober 2017. Du kan hoppa framåt till den del som intresserar dig. Minst 3 mönster beskrivs. Azure-funktioner som förenklar utveckling och hantering beskrivs.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 1ee8f2fff958045f652b72358ab928f82920fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067621"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Videoindexerad och kommenterad för SaaS-app med flera innehavare med Azure SQL Database

Den här artikeln är ett kommenterat index till tidsplatserna för en 81 minuters video om SaaS hyresmodeller eller mönster. Med den här artikeln kan du hoppa bakåt eller framåt i videon som del intresserar dig. Videon förklarar de viktigaste designalternativen för ett databasprogram för flera innehavare i Azure SQL Database. Videon innehåller demos, genomgångar av hanteringskod, och ibland mer detaljerad information informeras av erfarenhet än vad som kan vara i vår skriftliga dokumentation.

Videon förstärker informationen i vår skriftliga dokumentation, finns på: 
- *Konceptuell:* [SaaS-databashyresmönster för flera innehavare][saas-concept-design-patterns-563e]
- *Tutorials:* [Wingtip Biljetter SaaS ansökan][saas-how-welcome-wingtip-app-679t]

Videon och artiklarna beskriver de många faserna för att skapa ett program med flera innehavare på Azure SQL Database i molnet. Särskilda funktioner i Azure SQL Database gör det enklare att utveckla och implementera appar med flera innehavare som är både enklare att hantera och på ett tillförlitligt sätt.

Vi uppdaterar rutinmässigt vår skriftliga dokumentation. Videon är inte redigerad eller uppdaterad, så så småningom mer av dess detaljer kan bli föråldrad.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Sekvens av 38 tidsindexerade skärmdumpar

I det här avsnittet indexeras tidsplatsen för 38 diskussioner under hela 81 minuters video. Varje gång index kommenteras med en skärmdump från videon, och ibland med ytterligare information.

Varje gång index är i formatet *h:mm:ss*. Den andra indexerade tidsplatsen, med namnet **Sessionsmål,** börjar till exempel vid den ungefärliga tidsplatsen **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Kompakta länkar till videoindexerade tidsplatser

Följande titlar är länkar till motsvarande kommenterade avsnitt senare i den här artikeln:

- [1. **(Start)** Välkomstbild, 0:00:03](#anchor-image-wtip-min00001)
- [2. Session mål, 0:03:11](#anchor-image-wtip-min00311)
- [3. Dagordning, 0:04:17](#anchor-image-wtip-min00417)
- [4. Webbapp för flera innehavare, 0:05:05](#anchor-image-wtip-min00505)
- [5. App webbformulär i aktion, 0:05:55](#anchor-image-wtip-min00555)
- [6. Kostnad per klient (skala, isolering, återställning), 0:09:31](#anchor-image-wtip-min00931)
- [7. Databasmodeller för flera innehavare: för- och nackdelar, 0:11:59](#anchor-image-wtip-min01159)
- [8. Hybrid modell blandar fördelarna med MT / ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. Single-tenant vs multi-tenant: för-och nackdelar, 0:16:44](#anchor-image-wtip-min01644)
- [10. Pooler är kostnadseffektiva för oförutsägbara arbetsbelastningar, 0:19:36](#anchor-image-wtip-min01936)
- [11. Demo av databas-per-klient och hybrid ST/MT, 0:20:08](#anchor-image-wtip-min02008)
- [12. Live app formulär som visar Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB och inte en DBA i sikte, 0:28:54](#anchor-image-wtip-min02854)
- [14. EXEMPEL PÅ ANVÄNDNING AV ELASTISK POOL I MYOB, 0:29:40](#anchor-image-wtip-min02940)
- [15. Lärande från MYOB och andra ISVs, 0:31:36](#anchor-image-wtip-min03136)
- [16. Mönster komponera i E2E SaaS scenario, 0:43:15](#anchor-image-wtip-min04315)
- [17. Canonical hybrid multi-tenant SaaS app, 0:47:33](#anchor-image-wtip-min04733)
- [18. Wingtip SaaS exempelapp, 0:48:10](#anchor-image-wtip-min04810)
- [19. Scenarier och mönster utforskas i handledning, 0:49:10](#anchor-image-wtip-min04910)
- [20. Demo av självstudier och GitHub-lagringsplats, 0:50:18](#anchor-image-wtip-min05018)
- [21. GitHub repo Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. Utforska mönstren, 0:56:20](#anchor-image-wtip-min05620)
- [23. Etablering av hyresgäster och introduktion, 0:57:44](#anchor-image-wtip-min05744)
- [24. Etablering av klienter och programanslutning, 0:58:58](#anchor-image-wtip-min05858)
- [25. Demo av hanteringsskript som etablerar en enda klient, 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell till etablering och katalog, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * FRÅN TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Hantera oförutsägbara klientarbetsbelastningar, 1:04:36](#anchor-image-wtip-min10436)
- [29. Elastisk pool övervakning, 1:06:39](#anchor-image-wtip-min10639)
- [30. Lastgenerering och prestandaövervakning, 1:09:42](#anchor-image-wtip-min10942)
- [31. Schemahantering i stor skala, 1:10:33](#anchor-image-wtip-min11033)
- [32. Distribuerad fråga över klientdatabaser, 1:12:21](#anchor-image-wtip-min11221)
- [33. Demo av biljettgenerering, 1:12:32](#anchor-image-wtip-min11232)
- [34. SSMS adhoc analytics, 1:12:46](#anchor-image-wtip-min11246)
- [35. Extrahera klientdata till SQL DW, 1:16:32](#anchor-image-wtip-min11632)
- [36. Diagram över daglig försäljning distribution, 1:16:48](#anchor-image-wtip-min11648)
- [37. Avsluta och uppmaning till handling, 1:19:52](#anchor-image-wtip-min11952)
- [38. Resurser för mer information, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Kommenterade indextidsplatser i videon

Om du klickar på en skärmdumpsbild tar du dig till den exakta tidsplatsen i videon.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Start)* Välkomstbild, 0:00:01

*Lär dig från MYOB: Designmönster för SaaS-program i Azure SQL Database - BRK3120*

[![Välkomstbild][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Titel: Lär dig från MYOB: Designmönster för SaaS-program i Azure SQL Database
- Bill.Gibson@microsoft.com
- Huvudprogramhanterare, Azure SQL-databas
- Microsoft Ignite session BRK3120, Orlando, FL USA, oktober/11 2017


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Session mål, 0:01:53
[![Sessionsmål][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternativa modeller för appar med flera innehavare, med för- och nackdelar.
- SaaS-mönster för att minska utvecklings-, hanterings- och resurskostnader.
- En exempelapp + skript.
- PaaS-funktioner + SaaS-mönster gör SQL Database till en mycket skalbar och kostnadseffektiv dataplattform för SaaS med flera innehavare.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Dagordning, 0:04:09
[![Dagordningen][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. Webbapp för flera innehavare, 0:05:00
[![Wingtip SaaS-app: Webbapp för flera innehavare][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. App webbformulär i aktion, 0:05:39
[![App webbformulär i aktion][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. Kostnad per klient (skala, isolering, återställning), 0:06:58
[![Kostnad per klient, skala, isolering, återställning][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Databasmodeller för flera innehavare: för- och nackdelar, 0:09:52
[![Databasmodeller för flera innehavare: för- och nackdelar][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Hybrid modell blandar fördelarna med MT / ST, 0:12:29
[![Hybridmodell blandar fördelarna med MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Single-tenant vs multi-tenant: för-och nackdelar, 0:13:11
[![Single-tenant vs multi-tenant: för-och nackdelar][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. Pooler är kostnadseffektiva för oförutsägbara arbetsbelastningar, 0:17:49
[![Pooler är kostnadseffektiva för oförutsägbara arbetsbelastningar][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Demo av databas-per-klient och hybrid ST/MT, 0:19:59
[![Demo av databas-per-klient och hybrid ST/ MT][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Live app formulär som visar Dojo, 0:20:10
[![Live app formulär som visar Dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB och inte en DBA i sikte, 0:25:06
[![MYOB och inte en DBA i sikte][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. EXEMPEL PÅ ANVÄNDNING AV ELASTISK POOL I MYOB, 0:29:30
[![EXEMPEL PÅ ANVÄNDNING AV MYOB-elastisk pool][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Lärande från MYOB och andra ISVs, 0:31:25
[![Lära av MYOB och andra ISV:er][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Mönster komponera i E2E SaaS scenario, 0:31:42
[![Mönster komponera i E2E SaaS scenario][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Canonical hybrid multi-tenant SaaS app, 0:46:04
[![Canonical hybrid flera hyresgäster SaaS app][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Wingtip SaaS exempelapp, 0:48:01
[![Exempelapp för Wingtip SaaS][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. Scenarier och mönster utforskas i handledning, 0:49:00
[![Scenarier och mönster utforskas i handledning][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Demo av självstudier och GitHub-lagringsplats, 0:50:12
[![Demo tutorials och GitHub repo][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. GitHub repo Microsoft/WingtipSaaS, 0:50:32
[![GitHub repo Microsoft / WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. Utforska mönstren, 0:56:15
[![Utforska mönstren][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Etablering av hyresgäster och introduktion, 0:56:19
[![Etablering av klienter och introduktion][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Etablering av klienter och programanslutning, 0:57:52
[![Etablera klienter och programanslutning][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Demo av hanteringsskript som etablerar en enda klient, 0:59:36
[![Demo av hanteringsskript som etablerar en enda klient][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell till etablering och katalog, 0:59:56
[![PowerShell till etablering och katalog][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * FRÅN TenantsExtended, 1:03:25
[![T-SQL SELECT * FRÅN TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Hantera oförutsägbara klientarbetsbelastningar, 1:03:34
[![Hantera oförutsägbara klientarbetsbelastningar][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. Elastisk pool övervakning, 1:06:32
[![Övervakning av elastisk pool][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. Lastgenerering och prestandaövervakning, 1:09:37
[![Övervakning av belastningsgenerering och prestanda][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Schemahantering i stor skala, 1:09:40
[![Schemahantering i stor skala][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Distribuerad fråga över klientdatabaser, 1:11:18
[![Distribuerad fråga över klientdatabaser][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Demo av biljettgenerering, 1:12:28
[![Demo av biljettgenerering][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. SSMS adhoc analytics, 1:12:35
[![SSMS-adhoc-analys][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Extrahera klientdata till SQL DW, 1:15:46
[![Extrahera klientdata till SQL DW][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Diagram över daglig försäljning distribution, 1:16:38
[![Graf över daglig försäljning distribution][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Avsluta och uppmaning till handling, 1:17:43
[![Avsluta och uppmaning till handling][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. Resurser för mer information, 1:20:35
[![Resurser för mer information][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Blogginlägg, 22 maj 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Konceptuell:* [SaaS-databashyresmönster för flera innehavare][saas-concept-design-patterns-563e]

- *Tutorials:* [Wingtip Biljetter SaaS ansökan][saas-how-welcome-wingtip-app-679t]

- GitHub-databaser för smaker av Wingtip Tickets SaaS hyresprogram:
    - [GitHub repo för - Fristående programmodell][github-wingtip-standaloneapp].
    - [GitHub repo för - DB Per klientmodell][github-wingtip-dbpertenant].
    - [GitHub repo för - Db-modell med flera innehavare][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Nästa steg

- [Första handledning artikeln][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Välkomstbild"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Sessionsmål."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Dagordningen."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Wingtip SaaS-app: Webbapp för flera innehavare"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "App webbformulär i aktion"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Kostnad per klient, skala, isolering, återställning"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Databasmodeller för flera innehavare: för- och nackdelar"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Hybridmodell blandar fördelarna med MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Single-tenant vs multi-tenant: för-och nackdelar"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Pooler är kostnadseffektiva för oförutsägbara arbetsbelastningar"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Demo av databas-per-klient och hybrid ST/ MT"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Live app formulär som visar Dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB och inte en DBA i sikte"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "EXEMPEL PÅ ANVÄNDNING AV MYOB-elastisk pool"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Lära av MYOB och andra ISV:er"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Mönster komponera i E2E SaaS scenario"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Canonical hybrid flera hyresgäster SaaS app"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Exempelapp för Wingtip SaaS"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Scenarier och mönster utforskas i handledning"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Demo av tutorials och GitHub repo"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "GitHub repo Microsoft / WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Utforska mönstren"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Etablering av klienter och introduktion"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Etablera klienter och programanslutning"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Demo av hanteringsskript som etablerar en enda klient"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "PowerShell till etablering och katalog"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * FRÅN TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Hantera oförutsägbara klientarbetsbelastningar"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Övervakning av elastisk pool"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Övervakning av belastningsgenerering och prestanda"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Schemahantering i stor skala"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Distribuerad fråga över klientdatabaser"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Demo av biljettgenerering"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "SSMS-adhoc-analys"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Extrahera klientdata till SQL DW"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Graf över daglig försäljning distribution"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Avsluta och uppmaning till handling"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "Resurser för mer information"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

