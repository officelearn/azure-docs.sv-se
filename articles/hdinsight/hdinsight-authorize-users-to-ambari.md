---
title: Auktorisera användare för Ambari Views - Azure HDInsight
description: Hantera Ambari-användar- och gruppbehörigheter för HDInsight-kluster med ESP aktiverat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 3bb58334e96bf5378fb78b70125f9c7994a7c2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435646"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Auktorisera användare för Apache Ambari Views

[Esp-aktiverade HDInsight-kluster (Enterprise Security Package)](./domain-joined/hdinsight-security-overview.md) tillhandahåller funktioner i företagsklass, inklusive Azure Active Directory-baserad autentisering. Du kan [synkronisera nya användare](hdinsight-sync-aad-users-to-cluster.md) som lagts till i Azure AD-grupper som har fått åtkomst till klustret, så att dessa specifika användare kan utföra vissa åtgärder. Att arbeta med användare, grupper och behörigheter i [Apache Ambari](https://ambari.apache.org/) stöds för både ESP HDInsight-kluster och vanliga HDInsight-kluster.

Active Directory-användare kan logga in på klusternoderna med sina domänautentiseringsuppgifter. De kan också använda sina domänautentiseringsuppgifter för att autentisera klusterinteraktioner med andra godkända slutpunkter som [Hue,](https://gethue.com/)Ambari Views, ODBC, JDBC, PowerShell och REST API:er.

> [!WARNING]  
> Ändra inte lösenordet för Ambari vakthund (hdinsightwatchdog) på din Linux-baserade HDInsight kluster. Om du ändrar lösenordet bryts möjligheten att använda skriptåtgärder eller utföra skalningsåtgärder med klustret.

Om du inte redan har gjort det följer du [dessa instruktioner](./domain-joined/apache-domain-joined-configure.md) för att etablera ett nytt ESP-kluster.

## <a name="access-the-ambari-management-page"></a>Gå till sidan Ambari-hantering

För att komma till **Ambari-hanteringssidan** på [Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)bläddrar du till `https://CLUSTERNAME.azurehdinsight.net`. Ange användarnamn och lösenord för klusteradministratören som du definierade när du skapade klustret. Välj sedan **Hantera Ambari** under **admin-menyn** på Ambari-instrumentpanelen:

![Apache Ambari instrumentpanel hantera](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="add-users"></a>Lägga till användare

### <a name="add-users-through-the-portal"></a>Lägga till användare via portalen

1. Välj **Användare**på hanteringssidan .

    ![Användare av apache ambari-hanteringssidan](./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png)

1. Välj **+ Skapa lokal användare**.

1. Ange **användarnamn** och **lösenord**. Välj **spara**.

### <a name="add-users-through-powershell"></a>Lägga till användare via PowerShell

Redigera variablerna nedan `CLUSTERNAME`genom `NEWUSER`att `PASSWORD` ersätta , och med lämpliga värden.

```powershell
# Set-ExecutionPolicy Unrestricted

# Begin user input; update values
$clusterName="CLUSTERNAME"
$user="NEWUSER"
$userpass='PASSWORD'
# End user input

$adminCredentials = Get-Credential -UserName "admin" -Message "Enter admin password"

$clusterName = $clusterName.ToLower()
$createUserUrl="https://$($clusterName).azurehdinsight.net/api/v1/users"

$createUserBody=@{
    "Users/user_name" = "$user"
    "Users/password" = "$userpass"
    "Users/active" = "$true"
    "Users/admin" = "$false"
} | ConvertTo-Json

# Create user
$statusCode =
Invoke-WebRequest `
    -Uri $createUserUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $createUserBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output "User is created: $user"
}
else
{
    Write-Output 'User is not created'
    Exit
}

$grantPrivilegeUrl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/privileges"

$grantPrivilegeBody=@{
    "PrivilegeInfo" = @{
        "permission_name" = "CLUSTER.USER"
        "principal_name" = "$user"
        "principal_type" = "USER"
    }
} | ConvertTo-Json

# Grant privileges
$statusCode =
Invoke-WebRequest `
    -Uri $grantPrivilegeUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $grantPrivilegeBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output 'Privilege is granted'
}
else
{
    Write-Output 'Privilege is not granted'
    Exit
}

Write-Host "Pausing for 100 seconds"
Start-Sleep -s 100

$userCredentials = "$($user):$($userpass)"
$encodedUserCredentials = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($userCredentials))
$zookeeperUrlHeaders = @{ Authorization = "Basic $encodedUserCredentials" }
$getZookeeperurl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"

# Perform query with new user
$zookeeperHosts =
Invoke-WebRequest `
    -Uri $getZookeeperurl `
    -Method Get `
    -Headers $zookeeperUrlHeaders

Write-Output $zookeeperHosts
```

### <a name="add-users-through-curl"></a>Lägga till användare via Curl

Redigera variablerna nedan `CLUSTERNAME`genom `ADMINPASSWORD` `NEWUSER`att `USERPASSWORD` ersätta , och med lämpliga värden. Skriptet är utformat för att köras med bash. Små ändringar skulle behövas för en Windows-kommandotolk.

```bash
export clusterName="CLUSTERNAME"
export adminPassword='ADMINPASSWORD'
export user="NEWUSER"
export userPassword='USERPASSWORD'

# create user
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d "{\"Users/user_name\":\"$user\",\"Users/password\":\"$userPassword\",\"Users/active\":\"true\",\"Users/admin\":\"false\"}" \
https://$clusterName.azurehdinsight.net/api/v1/users

echo "user created: $user"

# grant permissions
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d '[{"PrivilegeInfo":{"permission_name":"CLUSTER.USER","principal_name":"'$user'","principal_type":"USER"}}]' \
https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/privileges

echo "Privilege is granted"

echo "Pausing for 100 seconds"
sleep 10s

# perform query using new user account
curl -k -u $user:$userPassword -H "X-Requested-By: ambari" \
-X GET "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"
```

## <a name="grant-permissions-to-apache-hive-views"></a>Bevilja behörigheter till Apache Hive-vyer

Ambari kommer med tanke instanser för [Apache Hive](https://hive.apache.org/) och [Apache TEZ](https://tez.apache.org/), bland annat. Om du vill bevilja åtkomst till en eller flera Hive-vyinstanser går du till **sidan Ambari-hantering**.

1. På hanteringssidan väljer du länken **Vyer** under **menyrubriken Vyer** till vänster.

    ![Apache Ambari vyer visa länkar](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. Expandera **hive-raden** på sidan Vyer. Det finns en standardvy för Hive som skapas när Hive-tjänsten läggs till i klustret. Du kan också skapa fler Hive-vyinstanser efter behov. Välj en Hive-vy:

    ![HDInsight-vyer - Apache Hive-vyn](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. Bläddra mot nederkant på visa-sidan. Under avsnittet *Behörigheter* har du två alternativ för att ge domänanvändare deras behörigheter till vyn:

**Bevilja behörighet till dessa användare** ![Bevilja behörighet till dessa användare](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**Bevilja behörighet till dessa grupper** ![Bevilja behörighet till dessa grupper](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. Om du vill lägga till en användare väljer du knappen **Lägg till användare.**

   * Börja skriva användarnamnet så visas en listruta med tidigare definierade namn.

     ![Apache Ambari användare automatiskt slutförs](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * Markera eller skriv klart användarnamnet. Om du vill lägga till det här användarnamnet som en ny användare väljer du knappen **Nytt.**

   * Om du vill spara ändringarna markerar du den **blå kryssrutan**.

     ![Apache Ambari beviljar användarbehörigheter](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. Om du vill lägga till en grupp markerar du knappen **Lägg till grupp.**

   * Börja skriva gruppnamnet. Processen att välja ett befintligt gruppnamn eller lägga till en ny grupp är densamma som för att lägga till användare.
   * Om du vill spara ändringarna markerar du den **blå kryssrutan**.

     ![Apache Ambari bevilja behörigheter](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

Det är användbart att lägga till användare direkt i en vy när du vill tilldela en användare behörigheter att använda vyn, men inte vill att de ska vara medlem i en grupp som har ytterligare behörigheter. Om du vill minska antalet administrativa omkostnader kan det vara enklare att tilldela behörigheter till grupper.

## <a name="grant-permissions-to-apache-tez-views"></a>Bevilja behörigheter till Apache TEZ-vyer

[Apache](https://tez.apache.org/) TEZ-vyinstanserna gör det möjligt för användare att övervaka och felsöka alla Tez-jobb, som skickas in av [Apache Hive-frågor](https://hive.apache.org/) och [Apache Pig-skript.](https://pig.apache.org/) Det finns en standard-Tez-vyinstans som skapas när klustret etableras.

Om du vill tilldela användare och grupper till en Tez-vyinstans expanderar du raden **I den exklusiva ekonomiska zonerna** på sidan Vyer, som beskrivits tidigare.

![HDInsight-vyer - Apache Tez-vy](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

Om du vill lägga till användare eller grupper upprepar du steg 3 – 5 i föregående avsnitt.

## <a name="assign-users-to-roles"></a>Tilldela användare till roller

Det finns fem säkerhetsroller för användare och grupper, listade i ordning för att minska åtkomstbehörigheter:

* Klusteradministratör
* Klusteroperator
* Tjänstadministratör
* Serviceoperatör
* Klusteranvändare

Om du vill hantera roller går du till **sidan Ambari-hantering**och väljer sedan länken **Roller** i *menygruppen Kluster* till vänster.

![Menylänkar för Apache Ambari-roller](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

Om du vill visa listan över behörigheter som ges till varje roll klickar du på det blå frågetecknet bredvid tabellrubriken **Roller** på sidan Roller.

![Apache Ambari-roller menylänkbehörigheter](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Apache Ambari-roller menylänkbehörigheter")

På den här sidan finns det två olika vyer som du kan använda för att hantera roller för användare och grupper: Blockera och lista.

### <a name="block-view"></a>Blockera vy

Blockvyn visar varje roll på sin egen rad och ger **dessa användare tilldela roller** och Tilldela roller till dessa **grupper-alternativ** enligt beskrivningen tidigare.

![Apache Ambari-roller blockvy](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>Listvy

Listvyn innehåller funktioner för snabbredigering i två kategorier: Användare och grupper.

* Kategorin Användare i listvyn visar en lista över alla användare, så att du kan välja en roll för varje användare i listrutan.

    ![Apache Ambari roller listvy - användare](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* Kategorien Grupper i listvyn visar alla grupper och den roll som tilldelats varje grupp. I vårt exempel synkroniseras listan över grupper från Azure AD-grupper som anges i egenskapen **Access-användargrupp** för klustrets domäninställningar. Se [Skapa ett HDInsight-kluster med ESP aktiverat](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).

    ![Apache Ambari roller listvy - grupper](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    I bilden ovan tilldelas gruppen "hiveusers" rollen *klusteranvändare.* Det här är en skrivskyddad roll som gör att användarna i den gruppen kan visa men inte ändra tjänstkonfigurationer och klustermått.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Logga in på Ambari som visningsanvändare

Vi har tilldelat våra Azure AD-domänanvändare "hiveuser1"-behörigheter till Hive- och Tez-vyer. När vi startar Ambari Web UI och ange den här användarens domän autentiseringsuppgifter (Azure AD användarnamn i e-postformat och lösenord), omdirigeras användaren till Ambari Visningar sidan. Härifrån kan användaren välja en tillgänglig vy. Användaren kan inte besöka någon annan del av webbplatsen, inklusive instrumentpanelen, tjänster, värdar, aviseringar eller administratörssidor.

![Apache Ambari-användare med endast vyer](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Logga in på Ambari som klusteranvändare

Vi har tilldelat vår Azure AD-domänanvändare "hiveuser2" till *rollen Klusteranvändare.* Den här rollen kan komma åt instrumentpanelen och alla menyalternativ. En klusteranvändare har färre tillåtna alternativ än en administratör. Hiveuser2 kan till exempel visa konfigurationer för var och en av tjänsterna, men kan inte redigera dem.

![Apache Ambari instrumentpanelsvisning](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Apache Hive-principer i HDInsight med ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Hantera ESP HDInsight-kluster](./domain-joined/apache-domain-joined-manage.md)
* [Använd Apache Hive View med Apache Hadoop i HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synkronisera Azure AD-användare till klustret](hdinsight-sync-aad-users-to-cluster.md)
* [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API](./hdinsight-hadoop-manage-ambari-rest-api.md)
