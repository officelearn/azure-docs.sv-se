---
title: Utveckla ett säkert webb program | Microsoft Docs
description: Den här enkla exempel appen implementerar säkerhets metod tips som förbättrar ditt program och din organisations säkerhets position när du utvecklar på Azure.
keywords: na
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 640900458eccc36afe58cb148ffd7b94b43be879
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934923"
---
# <a name="develop-a-secure-web-app"></a>Utveckla en säker webbapp

Det här exemplet är en enkel python-app som visar en webb sida som innehåller länkar till säkerhets resurser för att utveckla appar i Azure. Appen implementerar säkerhets metoder som hjälper dig att förbättra ditt program och din organisations säkerhets position när du utvecklar appar i Azure.

Följ stegen som beskrivs i den här artikeln för att se till att program komponenterna är korrekt konfigurerade. Databasen, Azure App Service, Azure Key Vault instansen och Azure Application Gateway-instansen är beroende av varandra.

Distributions skripten konfigurerar infrastrukturen. När du har kört distributions skripten måste du göra en manuell konfiguration i Azure Portal för att länka komponenterna och tjänsterna tillsammans.

Exempel appen är riktad mot nybörjare som utvecklar program i Azure som vill implementera säkerhets åtgärder i sina program.

När du utvecklar och distribuerar den här appen lär du dig att:

- Skapa en Azure Key Vault instans, lagra och hämta hemligheter från den.
- Distribuera Azure Database for PostgreSQL, konfigurera säkra lösen ord och ge åtkomst till den.
- Kör en Alpine Linux-behållare på Azure Web Apps för Linux och aktivera hanterade identiteter för Azure-resurser.
- Skapa och konfigurera en Azure Application Gateway-instans med en brand vägg som använder [OWASP 10 högsta ruleset](https://coreruleset.org/).
- Aktivera kryptering av data under överföring och i vila med hjälp av Azure-tjänster.

När du har utvecklat och distribuerat den här appen har du ställt in följande exempel-webbapp tillsammans med de konfigurations-och säkerhets åtgärder som beskrivs.

![Exempel på webbapp](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Arkitektur
Appen är ett typiskt n-Nivåprogram med tre nivåer. Klient delen, Server delen och databas lagret med integrerade komponenter för övervakning och hemlig hantering visas här:

![App-arkitektur](./media/secure-web-app/architecture.png)

Arkitekturen består av följande komponenter:

- [Azure Application Gateway](../../application-gateway/index.yml). Tillhandahåller gateway och brand vägg för vår program arkitektur.
- [Azure Web Apps på Linux](../../app-service/containers/app-service-linux-intro.md). Tillhandahåller behållar körning för att köra python-appen i en Linux-miljö.
- [Azure Key Vault](../../key-vault/index.yml). Lagrar och krypterar vår Apps hemligheter och hanterar skapandet av åtkomst principer runt dem.
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/). Lagra appens data på ett säkert sätt.
- [Azure Security Center](../../security-center/index.yml) och [Azure Application](../../azure-monitor/app/app-insights-overview.md)insikter. Tillhandahåller övervakning och aviseringar om hur appen fungerar.

## <a name="threat-model"></a>Hot modell
Hot modellering är en process där du kan identifiera potentiella säkerhetshot för ditt företag och program och sedan se till att en lämplig minsknings plan är på plats.

Det här exemplet använde [Microsoft Threat Modeling Tool](threat-modeling-tool.md) för att implementera hot modellering för appen säker exempel. Genom att skriva diagram över komponenterna och data flödena kan du identifiera problem och hot tidigt i utvecklings processen. Detta sparar tid och pengar senare.

Det här är Hot modellen för exempel appen:

![Hot modell](./media/secure-web-app/threat-model.png)

Några exempel på hot och eventuella sårbarheter som verktyget för Threat Modeling genererar visas på följande skärm bild. Hot modellen ger en översikt över angrepps ytan som exponeras och gör det möjligt för utvecklarna att tänka på hur de kan åtgärda problemen.

![Hot modellens utdata](./media/secure-web-app/threat-model-output.png)

SQL-inmatning i föregående hot modells utdata minimeras till exempel genom att de sanerade indata matas in och genom att använda lagrade funktioner i Azure Database for PostgreSQL. Den här lösningen förhindrar körning av frågor vid data läsningar och skrivningar.

Utvecklare förbättrar den övergripande säkerheten för systemet genom att minimera varje hot i Hot modellens utdata.

## <a name="deployment"></a>Distribution
Med följande alternativ kan du köra Linux på Azure App Service:

- Välj en behållare i listan över färdiga Microsoft-behållare på Azure som har skapats med stöd för tekniker (python, ruby, PHP, Java, Node. js, .NET Core).
- Använd en anpassad behållare som skapats. Välj egna behållar register som källa för avbildningen och bygg på de många tillgängliga tekniker som stöder HTTP.

I det här exemplet ska du köra distributions skriptet som distribuerar webapp till App Service och skapar resurserna.

Appen kan använda de olika distributions modeller som visas nedan:

![Diagram över distributions data flöde](./media/secure-web-app/deployment.png)

Det finns många sätt att distribuera appar på Azure, inklusive:

- Azure Resource Manager-mallar
- PowerShell
- Azure CLI
- Azure Portal
- Azure DevOps

Det här programmet används:

- [Docker](https://docs.docker.com/) för att skapa och bygga behållar avbildningar.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för distribution.
- [Docker-hubb](https://hub.docker.com/) som behållar registret.

## <a name="security-considerations"></a>Säkerhetsöverväganden

### <a name="network"></a>Nätverk
Exempel appen använder SSL-kryptering från slut punkt till slut punkt för överförings data som flödar in i och ut ur nätverket. Gatewayen konfigureras med ett självsignerat certifikat.
> [!IMPORTANT]
> Ett självsignerat certifikat används i den här demonstrationen. I en produktions miljö bör du skaffa certifikat från en verifierad certifikat utfärdare (CA).

Program brand väggen kontrollerar också inkommande trafik och aviserings administratörer när skadlig trafik upptäcks i nätverks trafiken.
Application Gateway minimerar risken för DDoS-och SQL-injektering-hot som upptäckts i Hot modellen.

### <a name="identity"></a>Identitet
För att logga in på portalen använder exempel-appen Multi-Factor Authentication för Azure Active Directory (Azure AD)-administratörer som har tilldelats åtkomst till resurserna.
Exempel appen använder hanterade identiteter för att få behörighet att läsa och hämta hemligheter från Azure Key Vault, vilket säkerställer att appen inte behöver hårdkoda autentiseringsuppgifter och tokens för att läsa hemligheterna. Azure AD skapar automatiskt de tjänst huvud namn som appen behöver för att läsa och ändra hemligheter när hanterade identiteter används.

Hanterade identiteter för Azure-resurser och MFA gör det svårare för angripare att få behörighet och eskalera sina privilegier i systemet. Det här hotet påpekades i Hot modellen.
Appen använder OAuth, vilket innebär att användare som registrerats i OAuth-programmet kan logga in i appen.

### <a name="storage"></a>Storage
Data i PostgreSQL-databasen krypteras i vila automatiskt av Azure Database for PostgreSQL. Databasen auktoriserar App Service IP-adresser så att bara den distribuerade App Service-webbappen kan komma åt databas resurserna med rätt autentiseringsuppgifter för autentisering.

### <a name="logging-and-auditing"></a>Loggning och granskning
Appen implementerar loggning med hjälp av Application Insights för att spåra mått, loggar och undantag som inträffar. Den här loggningen ger tillräckligt med app-metadata för att informera utvecklare och drift team medlemmar om appens status. Det ger också tillräckligt med data för att BackTrack om säkerhets incidenter.

## <a name="cost-considerations"></a>Kostnadsöverväganden
Om du inte redan har ett Azure-konto kan du skapa ett kostnads fritt. Gå till [sidan kostnads fritt konto](https://azure.microsoft.com/free/) för att komma igång, se vad du kan göra med ett kostnads fritt Azure-konto och lär dig vilka produkter som är kostnads fria i 12 månader.

Om du vill distribuera resurserna i exempel appen med säkerhetsfunktionerna måste du betala för vissa Premium-funktioner. När appen skalas och de kostnads fria nivåerna och utvärderingarna som erbjuds av Azure måste uppgraderas för att uppfylla program kraven kan kostnaderna öka. Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för Azure för att beräkna dina kostnader.

## <a name="deploy-the-solution"></a>Distribuera lösningen
### <a name="prerequisites"></a>Förutsättningar
För att komma igång med programmet måste du installera följande verktyg:

- En kod redigerare för att ändra och Visa program koden. [Visual Studio Code](https://code.visualstudio.com/) är ett alternativ med öppen källkod.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) på din utvecklings dator.
- [Git](https://git-scm.com/) i systemet. Git används för att klona käll koden lokalt.
- [JQ](https://stedolan.github.io/jq/), ett UNIX-verktyg för att fråga JSON på ett användarvänligt sätt.

Du behöver en Azure-prenumeration för att distribuera exempel appens resurser. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) för att testa exempel appen.

När du har installerat dessa verktyg är du redo att distribuera appen på Azure.

### <a name="environment-setup"></a>Konfigurera miljön
Kör distributions skripten för att konfigurera miljön och prenumerationen:

1. Om du vill klona käll kods databasen använder du följande git-kommando:

   ``` git
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```
2. Använd följande kommando för att flytta till katalogen:

   ```
   cd tutorial-project/scripts
   ```

3. Det finns filer i mappen skript som är speciella för den plattform som du använder (Windows eller Linux). När Azure CLI redan har installerats loggar du in på Azure-kontot i kommando tolken genom att köra CLI-kommandot:

   ``` azurecli
   az login
   ```

Webbläsaren öppnas, logga in med dina autentiseringsuppgifter. När du har loggat in kan du börja distribuera resurserna från kommando tolken.

Distributions `deploy-powershell.ps1` skripten `deploy-bash.sh` och innehåller kod som distribuerar hela programmet.
Så här distribuerar du lösningen:

1. Om du använder PowerShell kör `deploy-powershell.ps1` du filen genom att skriva `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` ersätta regionen och resurs gruppens namn med lämpliga Azure-regioner och ett namn för resurs gruppen
2. Om du använder Linux för att köra `deploy-bash.sh` filen genom att `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`skriva, kan du behöva göra filen körbar genom att skriva`chmod +x deploy-bash.sh`

I följande exempel demonstreras kodfragmenten i viktiga komponenter. Du kan distribuera exemplen individuellt eller med resten av komponenterna genom att köra Deploy-filerna.

### <a name="implementation-guidance"></a>Implementeringsanvisningar
Distributions skriptet är ett skript som kan delas upp i fyra faser. Varje fas distribuerar och konfigurerar en Azure-resurs som finns i [arkitektur diagrammet](#architecture).

De fyra faserna är:

- Distribuera Azure Key Vault.
- Distribuera Azure Database for PostgreSQL.
- Distribuera Azure Web Apps på Linux.
- Distribuera Application Gateway med brand vägg för webbaserade program.

Varje fas bygger på föregående, med hjälp av konfiguration från de tidigare distribuerade resurserna.

Kontrol lera att du har installerat verktygen som visas under [krav](#prerequisites)för att slutföra implementerings stegen.

#### <a name="deploy-azure-key-vault"></a>Distribuera Azure Key Vault
I det här avsnittet skapar och distribuerar du en Azure Key Vault-instans som används för att lagra hemligheter och certifikat.

När du har slutfört distributionen har du en Azure Key Vault-instans som distribuerats på Azure.

Distribuera Azure Key Vault med Azure CLI:

1. Deklarera variablerna för Azure Key Vault.
2. Registrera Azure Key Vault-providern.
3. Skapa resurs gruppen för instansen.
4. Skapa Azure Key Vault-instansen i resurs gruppen som skapades i steg 3.

   ``` azurecli

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```
Vi rekommenderar att du använder hanterade identiteter för Azure-resurser i appar som använder Key Vault för att få åtkomst till resurser. Din säkerhets position ökar när åtkomst nycklar till Key Vault inte lagras i koden eller i konfigurationen.

#### <a name="deploy-azure-database-for-postgresql"></a>Distribuera Azure Database for PostgreSQL
Azure Database for PostgreSQL fungerar på följande sätt, skapa först databas servern och skapa sedan databasen där schemat och data ska lagras.

När du har slutfört distributionen har du en PostgreSQL-Server och-databas som körs på Azure.

Distribuera Azure Database for PostgreSQL med Azure CLI:

1. Öppna en Terminal med Azure CLI och konfigurationen av Azure-prenumerationen.
2. Generera en kombination av säker användar namn och lösen ord som används för att komma åt databasen. (Dessa bör lagras i Azure Key Vault för appar som använder dem.)
3. Skapa PostgreSQL-serverinstansen.
4. Skapa en databas på Server instansen som du skapade i steg 3.
5. Kör PostgreSQL-skript på PostgreSQL-instansen.

Koden nedan förlitar sig på PGUSERNAME-och PGPASSWORD-hemligheter som lagras i Azure-valv från steget distribuera nyckel valv ovan.

   ``` azurecli
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

När du har distribuerat databasen måste du lagra dess autentiseringsuppgifter och anslutnings sträng i Azure Key Vault.
I mappen skript finns det en `functions.sql` fil som innehåller den pl/pgsql-kod som skapar lagrade funktioner när du kör den. Genom att köra den här filen parameterizes du indata för att begränsa SQL-inmatning.

PostgreSQL paketeras med ett verktyg som kallas `psql` som används för att ansluta till databasen. För att `functions.sql`köra måste du ansluta till Azure Database for PostgreSQL-instansen från den lokala datorn och köra den därifrån. Installationen av verktyget psql ingår i standard installationen för PostgreSQL på varje operativ system.
Mer information finns i psql- [dokumentationen](https://www.postgresql.org/docs/9.3/app-psql.html).

Azure Cloud Shell inkluderar `psql` även verktyget. Du kan använda Cloud Shell direkt från Azure Portal genom att välja Cloud Shell-ikonen.

Om du vill aktivera fjärråtkomst till PostgreSQL-instansen måste du auktorisera IP-adressen i PostgreSQL.
Du aktiverar den här åtkomsten genom att gå till fliken **anslutnings säkerhet** , välja **Lägg till klient-IP**och spara de nya inställningarna.

![Auktorisera klient-IP](./media/secure-web-app/add-client-ip-postgres.png)

Om du använder Cloud Shell i stället för det lokala psql-verktyget väljer du **Tillåt åtkomst till Azure-tjänster** och ändra värdet till **på** för att tillåta åtkomst till Cloud Shell.

Anslut sedan till instansen genom att köra nedanstående psql-kommando med anslutnings sträng parametrar från fliken **anslutnings strängar** i postgresql-instansen på Azure Portal.
Ersätt de tomma klamrarna med parametrar från bladet anslutnings sträng i databasen och lösen ordet med lösen ordet från Azure Key Vault.

```sql
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Kör följande PL/pgSQL-skript efter att du har kontrollerat att du är ansluten till databasen. Skriptet skapar de lagrade funktioner som används för att infoga data i databasen.

```sql
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;


CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```


Mer information om hur du ställer in SSL och verifiering av certifikat utfärdare för PostgreSQL finns i [Konfigurera SSL-anslutning i Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security).

Ett rot certifikat ingår i behållaren. Stegen som vidtas för att hämta certifikatet är:

1. Ladda ned certifikat filen från [certifikat utfärdaren](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Hämta och installera openssl på datorn](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security).
3. Avkoda certifikat filen:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Läs mer om hur du konfigurerar SSL-säkerhet för PostgreSQL här [Konfigurera SSL-anslutningens säkerhet](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Distribuera Azure Web Apps på Linux
Du kan enkelt skapa Linux-tjänster ovanpå Azure App Service som Azure tillhandahåller en uppsättning fördefinierade behållare och avbildningar för vanliga språk som python, ruby, C#och Java. Azure stöder också anpassade behållare, vilket kan göra det möjligt för praktiskt taget alla programmeringsspråk att köras på Azure App Services plattform.

Den app som distribueras är en enkel python-app som körs på den senaste Ubuntu Linux distributionen. Den ansluter till Azure Key Vault-och PostgreSQL-instanser som skapades i föregående avsnitt för hantering av autentiseringsuppgifter och data lagring.

Följande Docker-fil finns i rotmappen i appen:

``` docker
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

Dockerfile ovan används för att bygga den behållare som finns på Azure Container Registry på `mcr.microsoft.com/samples/basic-linux-app`.

Koden nedan:

1. Deklarerar variabler och namn för App Service-instansen.
2. Skapar resurs gruppen för App Service plan.
3. Etablerar en instans av Azure Web Apps på Linux-behållare.
4. Aktiverar loggning för Web Apps-behållaren.
5. Ställer in några AppData i behållarens appinställningar.

   ```
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }

   ```

Det här skriptet skapar en tilldelad identitet för App Service-instansen som kan användas med MSI för att interagera med Azure Key Vault utan hårda kodnings hemligheter i kod eller konfiguration.

Gå till Azure Key Vault-instansen i portalen för att auktorisera den tilldelade identiteten på fliken åtkomst princip. Välj **Lägg till ny åtkomst princip**. Under **Välj huvud**namn söker du efter det program namn som liknar namnet på App Service-instansen som skapats.
Ett tjänst huvud namn som är kopplat till programmet ska vara synligt. Välj den och spara åtkomst princip sidan, som du ser i följande skärm bild.

Eftersom programmet bara behöver hämta nycklar väljer du behörigheten **Hämta** i alternativen för hemligheter, vilket ger åtkomst samtidigt som de behörigheter som beviljats minskas.

![Key Vault åtkomst princip](./media/secure-web-app/kv-access-policy.png)

*Skapa en princip för Key Vault åtkomst*

Spara åtkomst principen och spara sedan den nya ändringen på fliken **åtkomst principer** för att uppdatera principerna.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Distribuera Application Gateway med brand vägg för webbaserade program aktive rad
I Web Apps rekommenderar vi inte att du exponerar tjänster direkt för världen utanför Internet.
Belastnings utjämning och brand Väggs regler ger bättre säkerhet och kontroll över inkommande trafik och hjälper dig att hantera den.

Så här distribuerar du en Application Gateway-instans:

1. Skapa resurs gruppen för att House Application Gateway.
2. Etablera ett virtuellt nätverk att ansluta till gatewayen.
3. Skapa ett undernät för gatewayen i det virtuella nätverket.
4. Etablera en offentlig IP-adress.
5. Etablera programgatewayen.
6. Aktivera brand väggen för webbaserade program på gatewayen.

   ``` azurecli
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

Föregående skript:

1. Skapar ett nytt självsignerat certifikat i Azure.
2. Laddar ned det självsignerade certifikatet som en Base64-kodad fil.
3. Skapar ett lösen ord för det självsignerade certifikatet.
4. Exporterar certifikatet som en PFX-fil signerad med lösen ordet.
5. Lagrar certifikatets lösen ord i Azure Key Vault.

I det här avsnittet distribueras programgatewayen:

```powershell
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

När du har slutfört distributionen har du en Application Gateway med brand vägg för webbaserade program aktive rad.

Gateway-instansen exponerar port 443 för HTTPS. Den här konfigurationen säkerställer att vår app endast är tillgänglig på port 443 via HTTPS.

Att blockera oanvända portar och begränsa exponerings ytans exponering är en bra säkerhets rutin.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Lägg till nätverks säkerhets grupper i App Service-instansen

App Service instanser kan integreras med virtuella nätverk. Den här integrationen gör att de kan konfigureras med principer för nätverks säkerhets grupper som hanterar appens inkommande och utgående trafik.

1. Om du vill aktivera den här funktionen går du till bladet Azure App tjänst instans och väljer **nätverk**under **Inställningar**. I den högra rutan under **VNet-integrering**väljer **du klicka här för att konfigurera**.

   ![Ny integrering av virtuella nätverk](./media/secure-web-app/app-vnet-menu.png)

    *Ny virtuell nätverks integrering för App Service*
1. På nästa sida väljer du **Lägg till VNet (för hands version)** .

1. På nästa meny väljer du det virtuella nätverk som skapades i distributionen som börjar med `hello-vnet`. Du kan antingen skapa ett nytt undernät eller välja ett befintligt.
   I det här fallet skapar du ett nytt undernät. Ange **adress intervallet** till **10.0.3.0/24** och ge under **nätet**ett namn.

   ![App Service konfiguration av virtuellt nätverk](./media/secure-web-app/app-vnet-config.png)

    *Konfiguration av virtuellt nätverk för App Service*

Nu när du har aktiverat integrering av virtuella nätverk kan du lägga till nätverks säkerhets grupper i appen.

1. Använd Sök-rutan och Sök efter **nätverks säkerhets grupper**. Välj **nätverks säkerhets grupper** i resultatet.

    ![Sök efter nätverks säkerhets grupper](./media/secure-web-app/nsg-search-menu.png)

    *Sök efter nätverks säkerhets grupper*

2. I nästa meny väljer du **Lägg till**. Ange **namnet** på NSG och **resurs gruppen** där den ska finnas. Den här NSG kommer att användas för application gateways undernät.

    ![Skapa en NSG](./media/secure-web-app/nsg-create-new.png)

    *Skapa en NSG*

3. När NSG har skapats väljer du den. I sitt blad väljer du **inkommande säkerhets regler**under **Inställningar**. Konfigurera de här inställningarna så att anslutningar kommer till Application Gateway via port 443.

   ![Konfigurera NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Konfigurera NSG*

4. I utgående regler för gateway-NSG lägger du till en regel som tillåter utgående anslutningar till App Service-instansen genom att skapa en regel som är `AppService`riktad mot tjänst tag gen:

   ![Lägg till utgående regler för NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Lägg till utgående regler för NSG*

    Lägg till en annan utgående regel som tillåter att gatewayen skickar utgående regler till ett virtuellt nätverk.

   ![Lägg till en annan utgående regel](./media/secure-web-app/nsg-outbound-vnet.png)

    *Lägg till en annan utgående regel*

5. På bladet undernät i NSG väljer du **associera**, väljer det virtuella nätverk som skapades i distributionen och väljer Gateway-undernätet med namnet **GW-undernät**. NSG tillämpas på under nätet.

6. Skapa en annan NSG som i föregående steg, den här gången för App Service-instansen. Ge den ett namn. Lägg till regeln för inkommande trafik för port 443 som du gjorde för Application Gateway-NSG.

   Om du har en App Service-instans som distribuerats på en App Service-miljön-instans, vilket inte är fallet för den här appen, kan du lägga till regler för inkommande trafik som tillåter Azure Service Health avsökningar genom att öppna portarna 454-455 i App Service NSG. Här är konfigurationen:

   ![Lägg till regler för Azure Service Health avsökningar](./media/secure-web-app/nsg-create-healthprobes.png)

    *Lägg till regler för Azure Service Health avsökningar (endast App Service-miljön)*

7. I utgående säkerhets regler skapar du en ny utgående säkerhets regel som gör att App Service-instansen kan kommunicera med PostgreSQL-databasen. Konfigurera den så här:

   ![Regel för att tillåta utgående PostgreSQL-anslutningar](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Lägg till en regel för att tillåta utgående PostgreSQL-anslutningar*

Om du vill begränsa angrepps ytan ändrar du App Service nätverks inställningar så att endast programgatewayen får åtkomst till programmet.
Du gör detta genom att gå till fliken App Service nätverk, välja fliken **IP-begränsningar** och skapa en Tillåt-regel som endast tillåter programgatewayens IP att komma åt tjänsten direkt.

Du kan hämta IP-adressen för gatewayen från sidan Översikt. På fliken **CIDR för IP-adress** anger du IP-adressen i följande format `<GATEWAY_IP_ADDRESS>/32`:.

![Tillåt endast gatewayen](./media/secure-web-app/app-allow-gw-only.png)

*Tillåt endast Gateway-IP att komma åt App Service*


#### <a name="implement-azure-active-directory-oauth"></a>Implementera Azure Active Directory OAuth

Azure-dokumenten som distribueras på sidan exempel webb program är resurser i vår app som kan behöva skydd. Du kan använda Azure Active Directory (Azure AD) för att implementera autentisering för webb-, skriv bords-och mobilappar genom att använda olika autentiserings flöden.
Appen använder **inloggning med Microsoft**, som tillåter appen att läsa profiler för användare som har lagts till i vår Azure AD-lista med en enda klient.

Konfigurera appen så att de använder de autentiseringsuppgifter som krävs i Azure Portal:

1. Välj **Azure Active Directory**eller Sök efter den med hjälp av sökrutan.

2. Välj **ny registrering**:

   ![Skapa en registrering](./media/secure-web-app/ad-auth-create.png)

   *Skapa en Azure AD-App-registrering*

3. Ange namnet på appen på nästa sida. Under **konto typer som stöds**väljer du **konton endast i den här organisations katalogen**.
    Under omdirigerings- **URI**anger du den bas domän som appen ska köras på plus en med token-slutpunkten. Exempel: *GATEWAY_HASH*. cloudapp.net/token.

   ![Konfigurera registrering av Azure AD-App](./media/secure-web-app/ad-auth-type.png)

   *Konfigurera registrering av Azure AD-App*

4. En skärm visas som visar den registrerade appen och dess information. Du måste lägga till den här informationen i Azure Key Vault-instansen.
   1. Kopiera program-ID: t (Client) och spara det i `CLIENTID`Key Vault som.
   2. Kopiera omdirigerings-URI: n som du angav i föregående steg och `REDIRECTURI`Spara den som.
   3. Kopiera standard katalog namnet för Azure AD, som har formatet *Name*. microsoftonline.com, och spara det i Key Vault som `TENANT`.
   4. Gå till fliken **certifikat & hemligheter** i den Azure AD-app som du skapade tidigare och välj **ny klient hemlighet**, som visas i följande skärm bild. Ange ett förfallo datum och kopiera sedan det genererade värdet och spara det i Key Vault som `CLIENTSECRET`.

      ![Azure AD-Authorization Secret](./media/secure-web-app/ad-auth-secrets.png)

      *Azure AD-Authorization Secret*

   5. Generera en säker slumpmässig hemlig nyckel med hjälp av kommando rads verktyg och online verktyg. Spara den i Key Vault som `FLASKSECRETKEY`. Program ramverket använder den här nyckeln för att skapa sessioner.
        Information om hur du skapar en hemlig nyckel finns i [kolv-sessioner](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. När du har konfigurerat inloggningen måste du lägga till användare i Azure AD-länken så att de kan logga in på resursen. Om du vill lägga till dem går du till fliken **användare** i Azure AD, väljer **alla användare**och väljer sedan **ny användare** eller **ny gäst användare**. För testning kan du lägga till en gäst användare och bjuda in användaren till katalogen. Du kan också lägga till en ny användare om den domän där appen körs är verifierad. I det här exemplet kan endast användare som är registrerade i Azure AD-klienten registreras för åtkomst. Information om åtkomst till flera innehavare finns i dokumentationen.

   ![Lägg till användare i standard domän](./media/secure-web-app/ad-auth-add-user.png)

   *Lägg till användare till standard Azure Active Directorys domänen*

När du har lagt till Azure AD-konfigurationen och hemligheterna i Key Vault kan användarna autentiseras i appen med hjälp av Azure OAuth-autentisering.
I app-koden hanteras detta av Azure Active Directory Authentication Library (ADAL).

När hemligheterna är i Key Vault och programmet har åtkomst till hemligheterna och databasen, kan program tjänsten nås via gatewayens program-URL (https://GATEWAY_HASH.cloudapp.net), som du kan hämta från bladet.

Om du när du loggar in på Azure AD visas ett fel meddelande om att användaren inte är registrerad i den katalog som du försöker logga in på, måste du lägga till användaren. Om du vill lägga till användaren går du till fliken **användare** i Azure AD och lägger till användaren manuellt genom att ange informationen eller Bjud in användaren genom att ange deras e-postadress som gäst användare i Azure AD på bladet **Bjud in gäst** .

#### <a name="deploy-application-insights"></a>Distribuera Application Insights
Nu när appen distribueras och fungerar måste du hantera fel som uppstår i appen tillsammans med loggnings-och spårnings data insamling.
Logga och spåra data insamling innehåller en vy över gransknings händelser som sker i appen.

Application Insights är en tjänst som samlar in loggar som kan genereras av användare eller av systemet.

Så här skapar du en Application Insights-instans:

1. Sök efter **Application Insights** med hjälp av sökrutan i Azure Portal.
2. Välj **Application Insights**. Ange den information som visas här för att skapa en instans.

   ![Skapa en Application Insights-instans](./media/secure-web-app/app-insights-data.png)

När distributionen är klar har du en Application Insights-instans.

När du har skapat program insikts-instansen måste du göra appen medveten om Instrumentation-nyckeln som gör det möjligt att skicka loggar till molnet. Du gör detta genom att hämta Application Insights nyckeln och använda den i de program bibliotek som Azure tillhandahåller för Application Insights. Det bästa sättet är att lagra nycklar och hemligheter i Azure Key Vault för att skydda dem.

När du har skapat program insikts-instansen för den grundläggande exempel appen måste du göra appen medveten om Instrumentation-nyckeln som gör att den kan skicka loggar till molnet.
I Key Vault anger du en `APPINSIGHTSKEY` hemlighet och anger dess värde som Instrumentation-nyckel. Om du gör det kan appen skicka loggar och mått till Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementera Multi-Factor Authentication för Azure Active Directory
Administratörer måste se till att prenumerations kontona i portalen är skyddade. Prenumerationen är sårbar för attacker eftersom den hanterar de resurser som du har skapat. Om du vill skydda prenumerationen aktiverar du Multi-Factor Authentication på fliken **Azure Active Directory** i prenumerationen.

Azure AD fungerar baserat på principer som tillämpas på en användare eller grupper av användare som uppfyller ett visst kriterium.
Azure skapar en standard princip som anger att administratörer behöver tvåfaktorautentisering för att logga in på portalen.
När du har aktiverat den här principen kan du uppmanas att logga ut och logga in igen på Azure Portal.

Så här aktiverar du MFA för admin-inloggningar:

1. Gå till fliken **Azure Active Directory** i Azure Portal
2. Under kategorin säkerhet väljer du villkorlig åtkomst. Du ser den här skärmen:

   ![Villkorlig åtkomst – principer](./media/secure-web-app/ad-mfa-conditional-add.png)

Om du inte kan skapa en ny princip:

1. Gå till fliken **MFA** .
2. Välj länken Azure AD Premium **kostnads fri utvärderings version** för att prenumerera på den kostnads fria utvärderings versionen.

   ![Azure AD Premium kostnads fri utvärdering](./media/secure-web-app/ad-trial-premium.png)

Gå tillbaka till skärmen för villkorlig åtkomst.

1. Välj fliken ny princip.
2. Ange principens namn.
3. Välj de användare eller grupper som du vill aktivera MFA för.
4. Under **åtkomst kontroller**väljer du fliken **beviljande** och väljer sedan **Kräv Multi-Factor Authentication** (och andra inställningar om du vill).

   ![Kräv MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

Du kan aktivera principen genom att markera kryss rutan längst upp på skärmen eller göra det på fliken **villkorlig åtkomst** . När principen är aktive rad behöver användare MFA för att logga in på portalen.

Det finns en bas linje princip som kräver MFA för alla Azure-administratörer. Du kan aktivera det direkt i portalen. Om du aktiverar den här principen kan den aktuella sessionen bli ogiltig och du kan logga in igen.

Om bas linje principen inte är aktive rad:
1.  Välj **KRÄV MFA för administratörer**.
2.  Välj **Använd princip omedelbart**.

   ![Välj Använd princip omedelbart](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Övervaka appar och resurser med hjälp av Azure Sentinel

När ett program växer blir det svårt att sammanställa alla säkerhets signaler och mått som tas emot från resurser och gör dem användbara på ett åtgärds sätt.

Azure Sentinel har utformats för att samla in data, identifiera de typer av hot som är möjliga och ge insyn i säkerhets incidenter.
Även om det väntar på en manuell åtgärd kan Azure Sentinel förlita sig på fördefinierade spel böcker för att starta aviseringar och incident hanterings processer.

Exempel appen består av flera resurser som Azure Sentinel kan övervaka.
Om du vill konfigurera Azure Sentinel måste du först skapa en Log Analytics arbets yta som lagrar alla data som samlas in från de olika resurserna.

Så här skapar du den här arbets ytan:

1. Sök efter **Log Analytics**i rutan sök i Azure Portal. Välj **Log Analytics arbets ytor**.

   ![Sök efter Log Analytics arbets ytor](./media/secure-web-app/sentinel-log-analytics.png)

    *Sök efter Log Analytics arbets ytor*

2. På nästa sida väljer du **Lägg till** och anger sedan ett namn, en resurs grupp och en plats för arbets ytan.
   ![Skapa en Log Analytics-arbetsyta](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Skapa en Log Analytics-arbetsyta*

3. Använd sökrutan för att söka efter **Azure Sentinel**.

   ![Sök efter Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *Sök efter Azure Sentinel*

4. Välj **Lägg till** och välj sedan Log Analytics arbets ytan som du skapade tidigare.

   ![Lägg till en Log Analytics-arbetsyta](./media/secure-web-app/sentinel-workspace-add.png)

    *Lägg till en Log Analytics-arbetsyta*

5. På sidan **Azure Sentinel-data anslutningar** väljer du **data kopplingar**under **konfiguration**. Du ser en matris med Azure-tjänster som du kan länka till Log Analytics lagrings instans för analys i Azure Sentinel.

   ![Log Analytics data anslutningar](./media/secure-web-app/sentinel-connectors.png)

    *Lägg till en data anslutning i Azure Sentinel*

   Gör så här för att ansluta Application Gateway:

   1. Öppna bladet Azure Application Gateway-instans.
   2. Under **övervakning**väljer **diagnostikinställningar**.
   3. Välj **Lägg till diagnostisk inställning**.

      ![Lägg till Application Gateway diagnostik](./media/secure-web-app/sentinel-gateway-connector.png)

      *Lägg till Application Gateway diagnostik*

   4. På sidan **diagnostikinställningar** väljer du Log Analytics arbets ytan som du skapade och väljer sedan alla mått som du vill samla in och skicka till Azure Sentinel. Välj **Spara**.

        ![Inställningar för Azure Sentinel Connector](./media/secure-web-app/sentinel-connector-settings.png)

        *Inställningar för Azure Sentinel Connector*

  Måtten från resursen finns i Azure Sentinel, där du kan fråga och undersöka dem.

   Lägg till samma mått i diagnostikinställningar för Azure Key Vault, den offentliga IP-adressen, Azure Database for PostgreSQL och alla tjänster som stöder diagnostikloggar i ditt konto.

När du har konfigurerat måtten har Azure Sentinel data att analysera.

## <a name="evaluate-and-verify"></a>Utvärdera och verifiera
När du har utvecklat och distribuerat arkitekturen måste du kontrol lera att koden och de distribuerade tjänsterna uppfyller säkerhets kraven. Dessa är några steg som du kan vidta för att verifiera program varan:

- Statisk kod analys
- Sårbarhets sökning
- Hitta och åtgärda sårbarheter i program beroenden

Det här är de grundläggande Bygg stenarna för bästa praxis i säker utveckling.

### <a name="static-code-analysis"></a>Statisk kod analys
För exempel appen omfattar verifiering med statiska analys verktyg att hitta sårbarheter i app-koden genom att använda metoder som till exempel vattenkontrollerande och data flödes analys. Med de statiska analys verktygen för python blir du mer säker på att din app är säker.

**Luddfri**

PyFlakes, ett Python-bibliotek, hjälper dig att ta bort död kod och oanvända funktioner från appar, som du ser här:

![PyFlakes](./media/secure-web-app/pyflakes.png)

Luddfri ger tips och möjliga ändringar som kan göra kod rensningen och mindre fel känslig under körningen.

**PyLint**

PyLint angav det högsta värdet för det här projektet. Den utför kod-standard kontroller, fel kontroll och omstrukturering av tips för att säkerställa att koden som körs på servern är säker. Genom att använda PyLint för att uppdatera din kod kan du eliminera buggar och förbättra PyLint-klassificeringen, som följande bilder visar.

![Innan PyLint](./media/secure-web-app/before-pylint.png)

*Innan PyLint*

När du har åtgärdat några av kod felen som påträffas av de här verktygen är du mer säker på att koden inte är känslig för fel. Att åtgärda felen minskar avsevärt de säkerhets risker som kan uppstå när koden distribueras till produktions miljöer.

![Efter pylint](./media/secure-web-app/after-pylint.png)

*Efter PyLint*

### <a name="vulnerability-scanning"></a>Sårbarhets sökning
[OWASP](https://www.zaproxy.org/) är en webb programs sårbarhets-skanner med öppen källkod som du kan använda för att kontrol lera exempel appen för sårbarheter. Att köra verktyget i exempel appen visar några möjliga fel och angrepps vektorer.

![Verktyget ZAP](./media/secure-web-app/zap-tool.png)

*Verktyget ZAP*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Hitta och åtgärda sårbarheter i program beroenden
Om du vill hitta och åtgärda program beroenden kan du använda [OWASP beroende kontroll](https://www.owasp.org/index.php/OWASP_Dependency_Check).

Säkerhet är ett liknande program som kontrollerar beroenden. Du kan hitta den på [GitHub](https://github.com/pyupio/safety). Säkerhets genomsökningar för säkerhets risker som finns i välkända sårbarhets databaser.

![Nivå](./media/secure-web-app/pysafety.png)

*Nivå*

## <a name="next-steps"></a>Nästa steg
Följande artiklar kan hjälpa dig att utforma, utveckla och distribuera säkra program.

- [Design](secure-design.md)
- [Utveckla](secure-develop.md)
- [Distribuera](secure-deploy.md)
