---
title: LDAP-synkronisering i Ranger och Apache Ambari i Azure HDInsight
description: Ta itu med LDAP-synkroniseringen i Ranger och Ambari och ge allmänna riktlinjer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463224"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>LDAP-synkronisering i Ranger och Apache Ambari i Azure HDInsight

ESP-kluster (HDInsight Enterprise Security Package) använder Ranger för auktorisering. Apache Ambari och Ranger både synkronisera användare och grupper självständigt och fungerar lite annorlunda. Den här artikeln är avsedd att ta itu med LDAP-synkroniseringen i Ranger och Ambari.

## <a name="general-guidelines"></a>Allmänna riktlinjer

* Distribuera alltid kluster med grupper.
* I stället för att ändra gruppfilter i Ambari och Ranger kan du försöka hantera alla dessa i Azure AD och använda kapslade grupper för att ta in de användare som krävs.
* När en användare har synkroniserats tas den inte bort även om användaren inte ingår i grupperna.
* Om du behöver ändra LDAP-filtren direkt använder du användargränssnittet först eftersom det innehåller vissa valideringar.

## <a name="users-are-synced-separately"></a>Användare synkroniseras separat

Ambari och Ranger delar inte användardatabasen eftersom de tjänar två olika syften. Om en användare behöver använda Ambari-användargränssnittet måste användaren synkroniseras med Ambari. Om användaren inte synkroniseras med Ambari kommer Ambari UI / API att avvisa det, men andra delar av systemet kommer att fungera (dessa skyddas av Ranger eller Resource Manager och inte Ambari). Om du vill inkludera användaren i en Ranger-princip synkroniserar du användaren med Ranger.

När ett säkert kluster distribueras synkroniseras gruppmedlemmar transitivt (alla undergrupper och deras medlemmar) till både Ambari och Ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Ambari användarsynk och konfiguration

Från huvudnoderna körs ett `/opt/startup_scripts/start_ambari_ldap_sync.py`cron-jobb varje timme för att schemalägga användarsynkroniseringen. Cron-jobbet anropar Ambari-vila API:erna för att utföra synkroniseringen. Skriptet skickar en lista över användare och grupper som ska synkroniseras (eftersom användarna kanske inte tillhör de angivna grupperna anges båda individuellt). Ambari synkroniserar sAMAccountName som användarnamn och alla gruppmedlemmar, transitivt.

Loggarna ska vara `/var/log/ambari-server/ambari-server.log`i . Mer information finns i [Konfigurera Ambari-loggningsnivå](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

I DataSjökluster används mappen efter användarens skapande för att skapa hemmapparna för de synkroniserade användarna och de är inställda som ägare till hemmapparna. Om användaren inte synkroniseras med Ambari korrekt kan användaren drabbas av fel i åtkomsten till mellanlagring och andra temporära mappar.

### <a name="update-groups-to-be-synced-to-ambari"></a>Uppdatera grupper som ska synkroniseras till Ambari

Om du inte kan hantera gruppmedlemskap i Azure AD har du två alternativ:

* Utför en engångssynkronisering enligt beskrivningen mer fullständigt på [Synkronisera LDAP-användare och grupper](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html). När gruppmedlemskapet ändras måste du synkronisera det här alternativet igen.

* Skriv ett cron-jobb, anropa [Ambari API regelbundet](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) med de nya grupperna.

## <a name="ranger-user-sync-and-configuration"></a>Synkronisering och konfiguration av Ranger-användare

Ranger har en inbyggd synkroniseringsmotor som körs varje timme för att synkronisera användarna. Den delar inte användardatabasen med Ambari. HDInsight konfigurerar sökfiltret så att administratörsanvändaren, watchdog-användaren och medlemmarna i gruppen som angavs under klustergenereringen synkroniseras. Gruppmedlemmarna synkroniseras transitivt:

* Inaktivera inkrementell synkronisering.
* Aktivera synkroniseringskarta för användargrupper.
* Ange sökfiltret om du vill inkludera de transitiva gruppmedlemmarna.
* Synkronisera sAMAccountName för användare och namnattribut för grupper.

### <a name="group-or-incremental-sync"></a>Grupp- eller inkrementell synkronisering

Ranger stöder ett gruppsynkroniseringsalternativ, men det fungerar som en skärningspunkt med användarfilter. Inte en union mellan gruppmedlemskap och användarfilter. Ett vanligt användningsfall för gruppsynkroniseringsfilter i Ranger är - gruppfilter: (dn=clusteradmingroup), användarfilter: (city=seattle).

Inkrementell synkronisering fungerar bara för de användare som redan är synkroniserade (första gången). Inkrementell synkroniserar inte nya användare som läggs till i grupperna efter den första synkroniseringen.

### <a name="update-ranger-sync-filter"></a>Synkroniseringsfilter för Uppdatera ranger

LDAP-filtret finns i Ambari-användargränssnittet under konfigurationsavsnittet för Ranger-användarsynkronisering. Det befintliga filtret kommer `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`att finnas i formuläret . Se till att du lägger till predikat `net ads` i slutet och testa filtret med hjälp av sökkommandot eller ldp.exe eller något liknande.

## <a name="ranger-user-sync-logs"></a>Synkroniseringsloggar för Ranger-användare

Ranger-användarsynkronisering kan ske ur någon av headnodes. Loggarna är `/var/log/ranger/usersync/usersync.log`i . Så här ökar du loggarnas verbositet:

1. Logga in på Ambari.
1. Gå till avsnittet Ranger-konfiguration.
1. Gå till avsnittet **Avancerat usersync-log4j.**
1. Ändra `log4j.rootLogger` till `DEBUG` nivå (Efter ändring `log4j.rootLogger = DEBUG,logFile,FilterLog`ska det se ut ).
1. Spara konfigurations- och omstartsdistansen.

## <a name="next-steps"></a>Nästa steg

* [Autentiseringsproblem i Azure HDInsight](./domain-joined-authentication-issues.md)
* [Synkronisera Azure AD-användare till ett HDInsight-kluster](../hdinsight-sync-aad-users-to-cluster.md)
