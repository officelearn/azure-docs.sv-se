---
title: "Hantera användarbehörighet på fil- och programnivå - Azure HDInsight | Microsoft Docs"
description: "Hur du hanterar fil- och mappbehörigheter för domänanslutna HDInsight-kluster."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 42d617ffeb8c2fee6be6d747b39d80b09774a1c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-permissions-at-the-file-and-folder-levels"></a>Hantera användarbehörighet på fil- och programnivå

[Domänanslutna HDInsight-kluster](hdinsight-domain-joined-introduction.md) använder stark autentisering med Azure Active Directory (AD Azure) användare, och även *rollbaserad åtkomstkontroll* (RBAC)-principer för olika tjänster, till exempel YARN och Hive. Om data standardplatsen för klustret är Azure Storage, eller Windows Azure Storage BLOB (WASB), kan du även tillämpa fil- och mapp-behörighet. Du kan använda Apache Ranger för att styra åtkomsten till klustrets filer för en synkroniserad Azure AD-användare och grupper.
<!-- [synchronized Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md). -->

Apache Ranger instansen för domänanslutna HDInsight-kluster innehåller förkonfigurerade med Ranger WASB-tjänsten. Ranger WASB-tjänsten är en principmodulen management som liknar Ranger HDFS, men med en annan tillämpning av principer för Rangers åtkomst. I tjänsten Ranger WASB om en inkommande resursbegäran om inte har en matchande Ranger princip standard svaret är NEKA. Tjänsten Ranger klarar inte på kontrollen till WASB behörighet.

## <a name="permission-and-policy-model"></a>Behörighet och policy-modellen

Resursen åtkomstbegäranden utvärderas med hjälp av följande flöde:

![Apache Ranger princip utvärdering flöde](./media/hdinsight-add-acls-at-file-folder-levels/ranger-policy-evaluation-flow.png)

NEKA regler utvärderas först, följt av TILLÅT-reglerna. Om inga principer matchas i slutet av matchande returneras en NEKA.

### <a name="user-variable"></a>Variabeln användare

Du kan använda den `{USER}` variabel när tilldelning av principer för en användare att komma åt en `/{username}` underkatalogen, till exempel:

```
resource: path=/app-logs/{USER}, user: {USER}, recursive=true, permissions: all, delegateAdmin=true
```

Principen ovan ger åtkomst till sin egen undermapp under den `/app-logs/` directory. Här är den här principen ser ut i användargränssnittet Ranger:

![Exempel användning av variabeln användare](./media/hdinsight-add-acls-at-file-folder-levels/user-variable.png)

### <a name="policy-model-examples"></a>Exempel på modellen

I följande tabell visas några exempel på hur modellen princip fungerar:

| Ranger princip | Befintliga filsystemet | Användarens begäran | Resultat |
| -- | -- | -- | -- |
| / data/ekonomi /, bob, Skriv | / data | Bob, skapa filen /data/finance/mydatafile.txt | TILLÅT - mellanliggande mappen 'ekonomirelaterad' skapas på grund av överordnad kontroll |
| / data/ekonomi /, bob, Skriv | / data | Alice, skapa filen /data/finance/mydatafile.txt | NEKA - ingen matchande princip |
| / data/ekonomi *, bob, Skriv | / data | Bob, skapa filen /data/finance/mydatafile.txt | TILLÅT – i det här fallet valfria rekursiv principen (`*`) är finns, se [jokertecken](#wildcards) |
| /data/Finance/mydatafile.txt bob, Skriv | / data | Bob, skapa filen /data/finance/mydatafile.txt | NEKA - överordnad kontroll ' / data' misslyckas eftersom ingen princip |
| /data/Finance/mydatafile.txt bob, Skriv | / data/ekonomi | Bob, skapa filen /data/finance/mydatafile.txt | NEKA - ingen princip för överordnad '/ data/ekonomi-kontroll |

Behörigheter som krävs på mappnivå eller på filnivå, baserat på typ av åtgärd. Exempelvis kräver ett anrop för ”Läs/öppna” läsbehörighet på filnivå, medan ett anrop för ”skapa” kräver behörighet på den överordnade mappnivån.

### <a name="wildcards-"></a>Jokertecken (*)

När ett jokertecken (`*`) finns i sökvägen för en princip jokertecknet avser sökvägen och dess underkataloger. Den här rekursion är detsamma som att använda en `recurse-flag`. I Ranger WASB anger rekursion och partiellt namn som matchar jokertecknet.

## <a name="manage-file-and-folder-level-permissions-with-apache-ranger"></a>Hantera fil- och mappnivå behörigheter med Apache Ranger

Om du inte redan har gjort det, Följ [instruktionerna](hdinsight-domain-joined-configure.md) att etablera ett nytt kluster som ingår i domänen.

Öppna Ranger WASB genom att bläddra till `https://<YOUR CLUSTER NAME>.azurehdinsight.net/ranger/`. Ange klustrets administratörsanvändarnamn och lösenord som du definierade när du skapar klustret.

När du har loggat in kan se du Ranger instrumentpanelen:

![Ranger instrumentpanelen](./media/hdinsight-add-acls-at-file-folder-levels/ranger-dashboard.png)

Om du vill visa aktuella fil- och mappbehörigheter för klustret Tjänstadministratörens Azure Storage-konto, klickar du på den  ***CLUSTERNAME*_wasb** länken i rutan WASB kontroll.

![Ranger instrumentpanelen](./media/hdinsight-add-acls-at-file-folder-levels/wasb-dashboard-link.png)

Den aktuella principlistan visas. Flera vanliga principer ingår som en startpunkt - kontrollera information om varje princip för att se exempel användningsområden.

Du kan se om principen är aktiverad, om granskningsloggning konfigureras, och eventuella tilldelade grupper och användare för varje princip. Det finns två åtgärdsknappar för varje princip: redigera och ta bort.

![Principlistan](./media/hdinsight-add-acls-at-file-folder-levels/policy-list.png)

### <a name="adding-a-new-policy"></a>Lägga till en ny princip

1. Högst upp höger på sidan för principer WASB Välj **Lägg till ny princip**.

    ![Lägg till ny princip](./media/hdinsight-add-acls-at-file-folder-levels/add-new.png)

2. Ange ett beskrivande **principnamn**. Ange Azure **Lagringskonto** för klustret (*kontonamn*. blob.core.windows.net), och **konto lagringsbehållaren** angav när du skapade din kluster. Ange den **relativa sökvägen** (i förhållande till kluster) för att komma åt mappen eller filen.

    ![Den nya principen formuläret](./media/hdinsight-add-acls-at-file-folder-levels/new-policy.png)

3. Under formuläret, ange din **Tillåt villkor** för den här nya resursen. Välj tillämpliga grupper och användare och ange deras behörigheter. I exemplet nedan vi tillåta alla användare i den `sales` gruppen ska ha läs-/ skrivbehörighet.

    ![Tillåt försäljning](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales.png)

4. Välj **Spara**.

### <a name="example-policy-conditions"></a>Exempel Principvillkor

Apache Ranger [princip utvärdering flödet](#permission-and-policy-model) kan du ange valfri kombination av Tillåt och neka villkor som passar dina behov. Några exempel:

1. Tillåt alla användare av försäljning, men inga praktikanter:

    ![Tillåt försäljning, neka praktikanter](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns.png)

2. Tillåt alla användare av försäljning och neka alla praktikanter, förutom en intern med namnet ”hiveuser3”, som ska ha läsbehörighet:

    ![Tillåt försäljning, neka praktikanter förutom hiveuser3](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns-except-hiveuser3.png)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera principer för Hive i HDInsight-domänansluten](hdinsight-domain-joined-run-hive.md)
* [Hantera domänanslutna HDInsight-kluster](hdinsight-domain-joined-manage.md)
* [Hantera Ambari - auktorisera användare till Ambari](hdinsight-authorize-users-to-ambari.md)

<!-- * [Synchronize Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md) -->

