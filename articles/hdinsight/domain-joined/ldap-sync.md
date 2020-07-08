---
title: LDAP-synkronisering i Ranger-och Apache-Ambari i Azure HDInsight
description: Adressera LDAP-synkroniseringen i Ranger-och Ambari och ange allmänna rikt linjer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77463224"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>LDAP-synkronisering i Ranger-och Apache-Ambari i Azure HDInsight

HDInsight Enterprise Security Package-kluster (ESP) använder Ranger för auktorisering. Apache Ambari och Ranger både synkroniserar användare och grupper oberoende av varandra och fungerar lite annorlunda. Den här artikeln är avsedd att användas för att adressera LDAP-synkroniseringen i Ranger-och Ambari.

## <a name="general-guidelines"></a>Allmänna riktlinjer

* Distribuera alltid kluster med grupper.
* I stället för att ändra grupp filter i Ambari och Ranger, försök att hantera alla dessa i Azure AD och Använd kapslade grupper för att ta med de användare som krävs.
* När en användare har synkroniserats tas den inte bort även om användaren inte är en del av grupperna.
* Om du behöver ändra LDAP-filtren direkt använder du användar gränssnittet först eftersom det innehåller några verifieringar.

## <a name="users-are-synced-separately"></a>Användare synkroniseras separat

Ambari och Ranger delar inte användar databasen eftersom de fungerar i två olika syfte. Om en användare behöver använda Ambari-ANVÄNDARGRÄNSSNITTET måste användaren synkroniseras med Ambari. Om användaren inte har synkroniserats till Ambari, avvisar Ambari UI/API men andra delar av systemet kommer att fungera (dessa skyddas av Ranger eller Resource Manager och inte Ambari). Om du vill inkludera användaren i en Ranger-princip synkroniserar du användaren med Ranger.

När ett säkert kluster distribueras synkroniseras grupp medlemmar transitivt (alla under grupper och medlemmar) till både Ambari och Ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Ambari och konfiguration av användar synkronisering

Från huvudnoderna, körs ett cron-jobb `/opt/startup_scripts/start_ambari_ldap_sync.py` varje timme för att schemalägga användar synkroniseringen. Cron-jobbet anropar Ambari REST-API: er för att utföra synkroniseringen. Skriptet skickar en lista med användare och grupper som ska synkroniseras (eftersom användarna inte tillhör de angivna grupperna, båda anges individuellt). Ambari synkroniserar sAMAccountName som användar namn och alla grupp medlemmar, transitivt.

Loggarna bör vara i `/var/log/ambari-server/ambari-server.log` . Mer information finns i [Konfigurera loggnings nivå för Ambari](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

I Data Lake kluster används hooken för att skapa användare för att skapa arbetsmapparna för de synkroniserade användarna och de har angetts som ägare till arbetsmapparna. Om användaren inte har synkroniserats till Ambari korrekt, kan användaren få ansikts fel vid åtkomst till mellanlagring och andra temporära mappar.

### <a name="update-groups-to-be-synced-to-ambari"></a>Uppdatera grupper som ska synkroniseras till Ambari

Om du inte kan hantera grupp medlemskap i Azure AD har du två alternativ:

* Synkronisera [LDAP-användare och-grupper](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html)genom att utföra en tidssynkronisering som beskrivs mer fullständigt. När grupp medlemskapet ändras måste du utföra den här synkroniseringen igen.

* Skriv ett cron-jobb och anropa [Ambari-API: et regelbundet](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) med de nya grupperna.

## <a name="ranger-user-sync-and-configuration"></a>Ranger användar synkronisering och konfiguration

Ranger har en ininbyggd Sync-motor som körs varje timme för att synkronisera användarna. Den delar inte användar databasen med Ambari. HDInsight konfigurerar Sök filtret för att synkronisera administratörs användaren, övervaknings enheten och medlemmarna i gruppen som angavs när klustret skapades. Grupp medlemmarna kommer att synkroniseras transitivt:

* Inaktivera stegvis synkronisering.
* Aktivera synkronisering av användar grupp.
* Ange Sök filtret för att inkludera de transitiva grupp medlemmarna.
* Sync sAMAccountName för användare och namnattribut för grupper.

### <a name="group-or-incremental-sync"></a>Grupp eller stegvis synkronisering

Ranger har stöd för ett alternativ för gruppsynkronisering, men fungerar som ett snitt med användar filter. Inte en union mellan grupp medlemskap och användar filter. Ett vanligt användnings fall för gruppsynkroniserings filter i Ranger är-Group filter: (DN = clusteradmingroup), användar filter: (City = Seattle).

Stegvis synkronisering fungerar bara för användare som redan har synkroniserats (första gången). Stegvis synkronisering kommer inte att synkronisera nya användare som har lagts till i grupperna efter den inledande synkroniseringen.

### <a name="update-ranger-sync-filter"></a>Uppdatera Ranger-synkroniseringsfilter

LDAP-filtret finns i Ambari-ANVÄNDARGRÄNSSNITTET under konfigurations avsnittet Ranger användare-Sync. Det befintliga filtret kommer att ha formatet `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` . Se till att du lägger till predikat i slutet och testa filtret med hjälp av `net ads` Sök kommandot eller ldp.exe eller något liknande.

## <a name="ranger-user-sync-logs"></a>Ranger loggar för användar synkronisering

Ranger-användar synkronisering kan inträffa från någon av huvudnoderna. Loggarna finns i `/var/log/ranger/usersync/usersync.log` . Gör så här för att öka utförligheten för loggarna:

1. Logga in på Ambari.
1. Gå till avsnittet Ranger-konfiguration.
1. Gå till avsnittet Advanced **usersync-log4j** .
1. Ändra `log4j.rootLogger` till `DEBUG` -nivå (efter ändring bör den se ut `log4j.rootLogger = DEBUG,logFile,FilterLog` ).
1. Spara konfigurationen och starta om Ranger.

## <a name="next-steps"></a>Nästa steg

* [Autentiseringsfel i Azure HDInsight](./domain-joined-authentication-issues.md)
* [Synkronisera Azure AD-användare till ett HDInsight-kluster](../hdinsight-sync-aad-users-to-cluster.md)
