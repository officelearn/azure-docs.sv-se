---
title: Utveckla en säker webbapplikation | Microsoft-dokument
description: Den här enkla exempelappen implementerar metodtips för säkerhet som förbättrar ditt program och organisationens säkerhetsposition när du utvecklar på Azure.
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
ms.openlocfilehash: 730e478622da8cd90af1c559e4d0c6fd04151cca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686796"
---
# <a name="develop-a-secure-web-app"></a>Utveckla en säker webbapp

Det här exemplet är en enkel Python-app som visar en webbsida som innehåller länkar till säkerhetsresurser för att utveckla appar på Azure. Appen implementerar metodtips för säkerhet som kan förbättra ditt program och organisationens säkerhetsposition när du utvecklar appar på Azure.

Du bör följa stegen som beskrivs i den här artikeln sekventiellt för att säkerställa att programkomponenterna är korrekt konfigurerade. Databasen, Azure App Service, Azure Key Vault-instans och Azure Application Gateway-instans beror på varandra.

Distributionsskripten konfigurerar infrastrukturen. När du har kört distributionsskripten måste du göra en viss manuell konfiguration i Azure-portalen för att länka komponenterna och tjänsterna tillsammans.

Exempelappen är inriktad på nybörjare som utvecklar program på Azure som vill implementera säkerhetsåtgärder i sina program.

När du utvecklar och distribuerar den här appen får du lära dig hur du:

- Skapa en Azure Key Vault-instans, lagra och hämta hemligheter från den.
- Distribuera Azure Database för PostgreSQL, konfigurera säkra lösenord och auktorisera åtkomst till den.
- Kör en Alpine Linux-behållare på Azure Web Apps for Linux och aktivera hanterade identiteter för Azure-resurser.
- Skapa och konfigurera en Azure Application Gateway-instans med en brandvägg som använder [OWASP Top 10 Ruleset](https://coreruleset.org/).
- Aktivera kryptering av data under överföring och i vila med hjälp av Azure-tjänster.

När du har utvecklat och distribuerat den här appen har du konfigurerat följande exempelwebbapp tillsammans med de konfigurations- och säkerhetsåtgärder som beskrivs.

![Exempel på webbapp](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Arkitektur

Appen är ett typiskt n-nivåprogram med tre nivåer. Frontend, baksidan och databasskiktet med integrerade övervaknings- och hemliga hanteringskomponenter visas här:

![App-arkitektur](./media/secure-web-app/architecture.png)

Arkitekturen består av dessa komponenter:

- [Azure Application Gateway](../../application-gateway/index.yml). Tillhandahåller gateway och brandvägg för vår programarkitektur.
- [Azure Web Apps på Linux](../../app-service/containers/app-service-linux-intro.md). Tillhandahåller behållarkörningen för att köra Python-appen i en Linux-miljö.
- [Azure Key Vault](../../key-vault/index.yml). Lagrar och krypterar appens hemligheter och hanterar skapandet av åtkomstpolicyer runt dem.
- [Azure-databas för PostgreSQL](https://azure.microsoft.com/services/postgresql/). Lagrar våra appars data på ett säkert sätt.
- [Azure Security Center](../../security-center/index.yml) och [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Ger övervakning och varningar om hur vår app fungerar.

## <a name="threat-model"></a>Hotmodell

Hotmodellering är processen att identifiera potentiella säkerhetshot mot ditt företag och program och sedan se till att en korrekt begränsningsplan finns på plats.

I det här exemplet användes [Microsoft Threat Modeling Tool](threat-modeling-tool.md) för att implementera hotmodellering för den säkra exempelappen. Genom att kartlägga komponenterna och dataflödena kan du identifiera problem och hot tidigt i utvecklingsprocessen. Detta sparar tid och pengar senare.

Det här är hotmodellen för exempelappen:

![Hotmodell](./media/secure-web-app/threat-model.png)

Vissa exempelhot och potentiella sårbarheter som verktyget för hotmodellering genererar visas i följande skärmbild. Hotmodellen ger en översikt över den exponerade attackytan och uppmanar utvecklarna att tänka på hur du kan minska problemen.

![Utdata för hotmodell](./media/secure-web-app/threat-model-output.png)

Sql-injektion i föregående hotmodellutdata mildras till exempel genom att sanera användarindata och använda lagrade funktioner i Azure Database för PostgreSQL. Den här begränsningen förhindrar godtycklig körning av frågor under dataläsningar och skrivningar.

Utvecklare förbättrar systemets övergripande säkerhet genom att minska alla hot i hotmodellens utdata.

## <a name="deployment"></a>Distribution

Med följande alternativ kan du köra Linux på Azure App Service:

- Välj en behållare i listan över fördefinierade Microsoft-behållare på Azure som har skapats med stödtekniker (Python, Ruby, PHP, Java, Node.js, .NET Core).
- Använd en specialbyggd behållare. Välj dina egna behållarregister som källan till avbildningen och bygg vidare på de många tillgängliga tekniker som stöder HTTP.

I det här exemplet ska du köra distributionsskriptet som ska distribuera webbappen till App Service och skapa resurserna.

Appen kan använda de olika distributionsmodellerna som visas nedan:

![Flödesdiagram för distributionsdata](./media/secure-web-app/deployment.png)

Det finns många sätt att distribuera appar på Azure, bland annat:

- Azure Resource Manager-mallar
- PowerShell
- Azure CLI
- Azure Portal
- Azure DevOps

Denna ansökan används:

- [Docker](https://docs.docker.com/) för att skapa och skapa behållaravbildningar.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för distribution.
- [Docker Hub](https://hub.docker.com/) som behållarregister.

## <a name="security-considerations"></a>Säkerhetsöverväganden

### <a name="network"></a>Nätverk

Exempelappen använder TLS/SSL-kryptering från slutpunkt till slutpunkt för data som flödar in i och ut ur nätverket. Gatewayen är konfigurerad med ett självsignerat certifikat.
> [!IMPORTANT]
> Ett självsignerat certifikat används i den här demonstrationen. I en produktionsmiljö bör du hämta certifikat från en verifierad certifikatutfärdare.

Programbrandväggen inspekterar också inkommande trafik och varnar administratörer när skadlig trafik upptäcks i nätverkstrafiken.
Application Gateway minskar risken för DDoS- och SQL-insprutningshot som upptäckts i hotmodellen.

### <a name="identity"></a>Identitet

Om du vill logga in på portalen använder exempelappen Multifaktorautentisering för Azure Active Directory-administratörer (Azure AD) som har tilldelats åtkomst till resurserna.
Exempelappen använder hanterade identiteter för att få behörighet att läsa och hämta hemligheter från Azure Key Vault, vilket säkerställer att appen inte behöver autentiseringsuppgifter och token för att läsa hemligheterna. Azure AD skapar automatiskt tjänstens huvudnamn som appen behöver läsa och ändrar hemligheterna när hanterade identiteter används.

Hanterade identiteter för Azure-resurser och MFA gör det svårare för motståndare att få privilegier och eskalera sina privilegier i systemet. Hotet pekades ut i hotmodellen.
Appen använder OAuth, som gör att användare som är registrerade i OAuth-programmet kan logga in på appen.

### <a name="storage"></a>Storage

Data i PostgreSQL-databasen krypteras i vila automatiskt av Azure Database för PostgreSQL. Databasen auktoriserar IP-adresserna för App Service så att endast den distribuerade App Service-webbappen kan komma åt databasresurserna med rätt autentiseringsuppgifter.

### <a name="logging-and-auditing"></a>Loggning och granskning

Appen implementerar loggning med hjälp av Application Insights för att spåra mått, loggar och undantag som uppstår. Den här loggningen innehåller tillräckligt med appmetadata för att informera utvecklare och driftteammedlemmar om appens status. Det ger också tillräckligt med data för att backa i händelse av säkerhetsincidenter.

## <a name="cost-considerations"></a>Kostnadsöverväganden

Om du inte redan har ett Azure-konto kan du skapa ett kostnadsfritt konto. Gå till den [kostnadsfria kontosidan](https://azure.microsoft.com/free/) för att komma igång, se vad du kan göra med ett kostnadsfritt Azure-konto och lär dig vilka produkter som är lediga i 12 månader.

Om du vill distribuera resurserna i exempelappen med säkerhetsfunktionerna måste du betala för vissa premiumfunktioner. När appen skalas och de kostnadsfria nivåer och utvärderingsversioner som erbjuds av Azure måste uppgraderas för att uppfylla programmets krav kan dina kostnader öka. Använd [Azure-priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att uppskatta dina kostnader.

## <a name="deploy-the-solution"></a>Distribuera lösningen

### <a name="prerequisites"></a>Krav

För att få igång programmet måste du installera följande verktyg:

- En kodredigerare för att ändra och visa programkoden. [Visual Studio Code](https://code.visualstudio.com/) är ett alternativ med öppen källkod.
- [Azure CLI](/cli/azure/install-azure-cli) på din utvecklingsdator.
- [Git](https://git-scm.com/) på ditt system. Git används för att klona källkoden lokalt.
- [jq](https://stedolan.github.io/jq/), ett UNIX-verktyg för att fråga JSON på ett användarvänligt sätt.

Du behöver en Azure-prenumeration för att distribuera exempelappens resurser. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) för att testa exempelappen.

När du har installerat dessa verktyg är du redo att distribuera appen på Azure.

### <a name="environment-setup"></a>Miljökonfiguration

Kör distributionsskripten för att konfigurera miljö och prenumeration:

1. Om du vill klona källkodsdatabasen använder du det här Git-kommandot:

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. Använd det här kommandot om du vill flytta till katalogen:

   ```shell
   cd tutorial-project/scripts
   ```

3. Det finns filer i skriptmappen som är specifika för den plattform du använder (Windows eller Linux). Eftersom Azure CLI redan har installerats loggar du in på Azure-kontot i kommandotolken genom att köra det här Azure CLI-kommandot:

   ```azurecli-interactive
   az login
   ```

Webbläsaren öppnas, logga in med dina autentiseringsuppgifter. När du har loggat in kan du börja distribuera resurserna från kommandotolken.

Distributionsskripten `deploy-powershell.ps1` `deploy-bash.sh` och innehåller kod som distribuerar hela programmet.
Så här distribuerar du lösningen:

1. Om du använder PowerShell `deploy-powershell.ps1` kör du `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` filen genom att skriva som ersätter region- och resursgruppsnamnet med lämpliga Azure-regioner och ett namn för resursgruppen
2. Om du är på `deploy-bash.sh` Linux köra `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`filen genom att skriva, kan du behöva göra filen körbar genom att skriva`chmod +x deploy-bash.sh`

I följande exempel visas utdrag av de viktigaste komponenterna. Du kan distribuera exemplen individuellt eller med resten av komponenterna genom att köra distributionsfilerna.

### <a name="implementation-guidance"></a>Vägledning för genomförande

Distributionsskriptet är ett skript som kan delas upp i fyra faser. Varje fas distribuerar och konfigurerar en Azure-resurs som finns i [arkitekturdiagrammet](#architecture).

De fyra faserna är:

- Distribuera Azure Key Vault.
- Distribuera Azure-databas för PostgreSQL.
- Distribuera Azure Web Apps på Linux.
- Distribuera Application Gateway med brandvägg för webbprogram.

Varje fas bygger på den föregående med hjälp av konfiguration från tidigare distribuerade resurser.

Kontrollera att du har installerat verktygen under [Förutsättningar](#prerequisites)för implementeringen.

#### <a name="deploy-azure-key-vault"></a>Distribuera Azure Key Vault

I det här avsnittet skapar och distribuerar du en Azure Key Vault-instans som används för att lagra hemligheter och certifikat.

När du har slutfört distributionen har du en Azure Key Vault-instans distribuerad på Azure.

Så här distribuerar du Azure Key Vault med hjälp av Azure CLI:

1. Deklarera variablerna för Azure Key Vault.
2. Registrera Azure Key Vault-providern.
3. Skapa resursgruppen för instansen.
4. Skapa Azure Key Vault-instansen i resursgruppen som skapats i steg 3.

   ```powershell-interactive

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

Det är en bra idé att använda hanterade identiteter för Azure-resurser i appar som använder Key Vault för att komma åt resurser. Säkerhetspositionen ökar när åtkomstnycklarna till Key Vault inte lagras i kod eller i konfiguration.

#### <a name="deploy-azure-database-for-postgresql"></a>Distribuera Azure-databas för PostgreSQL

Azure Database for PostgreSQL fungerar på följande sätt och skapa först databasservern och skapa sedan databasen där schemat och data ska lagras.

När du har slutfört distributionen har du en PostgreSQL-server och databas som körs på Azure.

Så här distribuerar du Azure Database för PostgreSQL med hjälp av Azure CLI:

1. Öppna en terminal med Azure CLI och din Azure-prenumerationskonfiguration.
2. Generera en säker kombination av användarnamn och lösenord som används för att komma åt databasen. (Dessa bör lagras i Azure Key Vault för appar som använder dem.)
3. Skapa PostgreSQL-serverinstansen.
4. Skapa en databas på den serverinstan som du skapade i steg 3.
5. Kör PostgreSQL-skript på PostgreSQL-instansen.

Koden nedan är beroende av PGUSERNAME- och PGPASSWORD-hemligheterna som lagras i Azure KeyVault från distributionen KeyVault ovan.

   ```powershell-interactive
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

När du har distribuerat databasen måste du lagra dess autentiseringsuppgifter och anslutningssträng i Azure Key Vault.
I skriptmappen finns det `functions.sql` en fil som innehåller PL/pgSQL-koden som skapar lagrade funktioner när du kör den. Om du kör den här filen parameteriserar du indata för att begränsa SQL-injektionen.

PostgreSQL levereras med ett `psql` verktyg som kallas som används för att ansluta till databasen. För `functions.sql`att köra måste du ansluta till Azure Database för PostgreSQL-instans från din lokala dator och köra den därifrån. Installation av psql-verktyget ingår i standardinstallationen för PostgreSQL på varje operativsystem.
Mer information finns i [psql-dokumentationen](https://www.postgresql.org/docs/9.3/app-psql.html).

Azure Cloud Shell `psql` innehåller också verktyget. Du kan använda Cloud Shell direkt från Azure-portalen genom att välja Cloud Shell Icon.

Om du vill aktivera fjärråtkomst till PostgreSQL-instansen måste du auktorisera IP-adressen i PostgreSQL.
Du aktiverar den här åtkomsten genom att gå till fliken **Anslutningssäkerhet,** välja **Lägg till klient-IP**och spara de nya inställningarna.

![Auktorisera klient-IP](./media/secure-web-app/add-client-ip-postgres.png)

Om du använder Cloud Shell i stället för det lokala psql-verktyget väljer du **Tillåt åtkomst till Azure-tjänster** och ändrar dess värde till **ON** så att ditt Cloud Shell-åtkomst.

Anslut sedan till instansen genom att köra kommandot nedan psql med anslutningssträngparametrar från fliken **Anslutningssträngar** i PostgreSQL-instansen på Azure-portalen.
Ersätt de tomma klammerparenteserna med parametrar från bladet Anslutningssträng i databasen och lösenordet med lösenordet från Azure Key Vault.

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Kör följande PL/pgSQL-skript när du har kontrollerat att du är ansluten till databasen. Skriptet skapar de lagrade funktioner som används för att infoga data i databasen.

```shell
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

Mer information om hur du konfigurerar TLS- och Certifikatutfärdaresverifiering för PostgreSQL finns [i Konfigurera TLS-anslutning i Azure Database för PostgreSQL](/azure/postgresql/concepts-ssl-connection-security).

Ett rotcertifikat ingår i behållaren. De åtgärder som vidtagits för att erhålla intyget är följande:

1. Hämta certifikatfilen från [certifikatutfärdaren](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Ladda ner och installera OpenSSL på din dator](/azure/postgresql/concepts-ssl-connection-security).
3. Avkoda certifikatfilen:

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Läs mer om hur du konfigurerar TLS-säkerhet för PostgreSQL här [Konfigurera TLS-anslutningssäkerhet](/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Distribuera Azure Web Apps på Linux

Du kan enkelt skapa Linux-tjänster ovanpå Azure App Service eftersom Azure tillhandahåller en uppsättning färdiga behållare och avbildningar för allmänt använda språk som Python, Ruby, C#och Java. Azure stöder också anpassade behållare, vilket kan tillåta praktiskt taget alla programmeringsspråk att köras på Azure App Service-plattformen.

Appen som distribueras är en enkel Python-app som körs på den senaste Ubuntu Linux-distributionen. Den ansluter till Azure Key Vault- och PostgreSQL-instanserna som har skapats i föregående avsnitt för autentiseringsuppgifter och datalagring.

Följande Docker-fil finns i appens rotmapp:

```dockerfile
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

Dockerfile ovan används för att skapa behållaren som finns på `mcr.microsoft.com/samples/basic-linux-app`Azure Container Registry at .

Koden nedan:

1. Deklarerar variabler och namn för App Service-instansen.
2. Skapar resursgruppen för App Service-planen.
3. Etablerar en Azure Web Apps på Linux-behållarinstans.
4. Aktiverar loggning för web app-behållaren.
5. Ställer in vissa appkonfigurationer i behållarens appinställningar.

   ```powershell-interactive
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

Det här skriptet skapar en tilldelad identitet för App Service-instansen som kan användas med MSI för att interagera med Azure Key Vault utan hårda kodningshemligheter i kod eller konfiguration.

Gå till Azure Key Vault-instansen i portalen för att auktorisera den tilldelade identiteten på fliken åtkomstprincip. Välj **Lägg till ny åtkomstprincip**. Under **Välj huvudnamn**söker du efter programnamnet som liknar namnet på apptjänstinstansen som skapats.
Ett huvudnamn för tjänsten som är kopplad till programmet ska vara synligt. Välj den och spara åtkomstprincipsidan, som visas i följande skärmbild.

Eftersom programmet bara behöver hämta nycklar väljer du behörigheten **Hämta** i hemligheterna, vilket ger åtkomst samtidigt som de privilegier som beviljas minskas.

![Åtkomstprincip för nyckelvalv](./media/secure-web-app/kv-access-policy.png)

*Skapa en princip för nyckelvalv*

Spara åtkomstprincipen och spara sedan den nya ändringen på fliken **Åtkomstprinciper** för att uppdatera principerna.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Distribuera Application Gateway med brandvägg för webbprogram aktiverat

I webbappar rekommenderas inte att du exponerar tjänster direkt för omvärlden på internet.
Belastningsutjämning och brandväggsregler ger mer säkerhet och kontroll över inkommande trafik och hjälper dig att hantera den.

Så här distribuerar du en Application Gateway-instans:

1. Skapa resursgruppen för att hysa programgatewayen.
2. Etablera ett virtuellt nätverk som ska kopplas till gatewayen.
3. Skapa ett undernät för gatewayen i det virtuella nätverket.
4. Etablera en offentlig IP-adress.
5. Etablera programgatewayen.
6. Aktivera brandvägg för webbprogram i gatewayen.

   ```powershell-interactive
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

Det föregående skriptet:

1. Skapar ett nytt självsignerat certifikat på Azure.
2. Hämtar det självsignerade certifikatet som en base64-kodad fil.
3. Genererar ett lösenord för det självsignerade certifikatet.
4. Exporterar certifikatet som en PFX-fil signerad med lösenordet.
5. Lagrar certifikatets lösenord i Azure Key Vault.

Det här avsnittet distribuerar programgatewayen:

```powershell-interactive
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

När du har slutfört distributionen har du en programgateway med brandvägg för webbprogram aktiverat.

Gateway-instansen exponerar port 443 för HTTPS. Den här konfigurationen säkerställer att vår app endast är tillgänglig på port 443 till HTTPS.

Att blockera oanvända portar och begränsa exponeringen på angreppsytan är en säkerhetspraxis.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Lägga till nätverkssäkerhetsgrupper i App Service-instansen

App Service-instanser kan integreras med virtuella nätverk. Med den här integreringen kan de konfigureras med principer för nätverkssäkerhetsgrupper som hanterar appens inkommande och utgående trafik.

1. Om du vill aktivera den här funktionen väljer du **Nätverk**under Inställningar under **Inställningar**. Välj Klicka här **i**den högra rutan för **att konfigurera**.

   ![Ny integrering av virtuella nätverk](./media/secure-web-app/app-vnet-menu.png)

    *Ny integrering av virtuella nätverk för App Service*

1. På nästa sida väljer du **Lägg till VNET (förhandsgranskning)**.

1. På nästa meny väljer du det virtuella nätverk `hello-vnet`som skapas i distributionen som börjar med . Du kan antingen skapa ett nytt undernät eller välja ett befintligt.
   Skapa i så fall ett nytt undernät. Ange **adressintervallet** till **10.0.3.0/24** och ge undernätet **ett appundernät**ett namn.

   ![Konfiguration av virtuellt nätverk för App Service](./media/secure-web-app/app-vnet-config.png)

    *Konfiguration av virtuellt nätverk för App Service*

Nu när du har aktiverat integreringen av virtuella nätverk kan du lägga till nätverkssäkerhetsgrupper i vår app.

1. Använd sökrutan, sök efter **nätverkssäkerhetsgrupper**. Välj **Nätverkssäkerhetsgrupper** i resultatet.

    ![Sök efter nätverkssäkerhetsgrupper](./media/secure-web-app/nsg-search-menu.png)

    *Sök efter nätverkssäkerhetsgrupper*

2. Välj **Lägg till**på nästa meny . Ange **namnet** på NSG och **resursgruppen** där den ska placeras. Denna NSG kommer att tillämpas på programgatewayens undernät.

    ![Skapa en NSG](./media/secure-web-app/nsg-create-new.png)

    *Skapa en NSG*

3. När NSG har skapats markerar du den. Välj Regler för inkommande **säkerhet**under **Inställningar**i bladet . Konfigurera dessa inställningar så att anslutningar som kommer in i programgatewayen över port 443.

   ![Konfigurera NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Konfigurera NSG*

4. I de utgående reglerna för gateway NSG lägger du till en regel som tillåter utgående `AppService`anslutningar till App Service-instansen genom att skapa en regel som är inriktad på servicetag:

   ![Lägga till utgående regler för NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Lägga till utgående regler för NSG*

    Lägg till ytterligare en utgående regel så att gatewayen kan skicka utgående regler till ett virtuellt nätverk.

   ![Lägga till ytterligare en utgående regel](./media/secure-web-app/nsg-outbound-vnet.png)

    *Lägga till ytterligare en utgående regel*

5. På undernätsbladet i NSG väljer du **Associera**, väljer det virtuella nätverk som skapats i distributionen och väljer gateway-undernätet med namnet **gw-undernät**. NSG tillämpas på undernätet.

6. Skapa en annan NSG som i det tidigare steget, den här gången för App Service-instansen. Ge det ett namn. Lägg till inkommande regeln för port 443 som du gjorde för programgateway NSG.

   Om du har en App Service-instans som distribueras på en App Service Environment-instans, vilket inte är fallet för den här appen, kan du lägga till inkommande regler för att tillåta Azure Service Health-avsökningar genom att öppna portar 454-455 på de inkommande säkerhetsgrupperna för din App Service NSG. Här är konfigurationen:

   ![Lägga till regler för Azure Service Health-avsökningar](./media/secure-web-app/nsg-create-healthprobes.png)

    *Lägga till regler för Azure Service Health-avsökningar (endast App Service Environment)*

7. Skapa en ny utgående säkerhetsregel som gör att App Service-instansen kan kommunicera med PostgreSQL-databasen i de utgående säkerhetsreglerna. Konfigurera den så här:

   ![Regel för att tillåta utgående PostgreSQL-anslutningar](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Lägga till en regel för att tillåta utgående PostgreSQL-anslutningar*

Om du vill begränsa angreppsytan ändrar du apptjänstens nätverksinställningar så att endast programgatewayen kan komma åt programmet.
Du gör detta genom att gå in på fliken App Service-nätverk, välja fliken **IP-begränsningar** och skapa en tillåt-regel som endast tillåter programgatewayens IP för att direkt komma åt tjänsten.

Du kan hämta IP-adressen för gatewayen från dess översiktssida. På fliken **IP-adress CIDR** anger du IP-adressen i det här formatet: `<GATEWAY_IP_ADDRESS>/32`.

![Tillåt endast gatewayen](./media/secure-web-app/app-allow-gw-only.png)

*Tillåt endast gateway-IP för åtkomst till App-tjänsten*

#### <a name="implement-azure-active-directory-oauth"></a>Implementera Azure Active Directory OAuth

Azure-dokumenten som distribueras på exempelwebbappsidan är resurser i vår app som kan behöva skydd. Du kan använda Azure Active Directory (Azure AD) för att implementera autentisering för webb-, skrivbords- och mobilappar med hjälp av olika autentiseringsflöden.
Appen använder **Logga in med Microsoft**, som gör att appen kan läsa profiler för användare som har lagts till i vår single-tenant Azure AD-användare lista.

Konfigurera appen så att den använder de autentiseringsuppgifter som krävs i Azure-portalen:

1. Välj **Azure Active Directory**eller sök efter den med hjälp av sökrutan.

2. Välj **ny registrering:**

   ![Skapa en registrering](./media/secure-web-app/ad-auth-create.png)

   *Skapa en Azure AD-appregistrering*

3. På nästa sida anger du appens namn. Under **Kontotyper som stöds**väljer du Konton i den här **organisationskatalogen**.
    Under **Omdirigera URI**anger du basdomänen som appen ska köras på plus en med tokenslutpunkten. Till exempel: *GATEWAY_HASH*.cloudapp.net/token.

   ![Konfigurera registrering av Azure AD-appar](./media/secure-web-app/ad-auth-type.png)

   *Konfigurera registrering av Azure AD-appar*

4. Du presenteras med en skärm som visar den registrerade appen och dess information. Du måste lägga till den här informationen i Azure Key Vault-instansen.
   1. Kopiera programmets (klient)-ID:t och `CLIENTID`spara det i Key Vault som .
   2. Kopiera den omdirigerings-URI som du `REDIRECTURI`angav i föregående steg och spara den som .
   3. Kopiera Azure AD-standardkatalognamnet, som har *formatnamnet*.microsoftonline.com, och spara `TENANT`det i Key Vault som .
   4. Gå till fliken **Certifikat & hemligheter** i Azure AD-appen som du skapade tidigare och välj Ny **klienthemlighet**, som visas i följande skärmbild. Ange ett utgångsdatum och kopiera sedan det genererade `CLIENTSECRET`värdet och spara det i Key Vault som .

      ![Azure AD-auktoriseringshemlighet](./media/secure-web-app/ad-auth-secrets.png)

      *Azure AD-auktoriseringshemlighet*

   5. Generera en säker slumpmässig hemlig nyckel med hjälp av något kommandorad/online-verktyg. Spara den i `FLASKSECRETKEY`Key Vault som . Programramverket använder den här nyckeln för att skapa sessioner.
        Mer information om hur du skapar en hemlig nyckel finns i [Flask Sessions](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. När du har konfigurerat inloggningen måste du lägga till användare på Azure AD-länken så att de kan logga in på resursen. Om du vill lägga till dem går du till fliken **Användare** i Azure AD, väljer **Alla användare**och väljer sedan **Ny användare** eller **Ny gästanvändare**. För testning kan du lägga till en gästanvändare och bjuda in användaren till katalogen. Du kan också lägga till en ny användare om domänen som appen körs på har validerats. I det här exemplet kan endast användare som är registrerade i Azure AD-klienten registreras för åtkomst. Information om inloggningsåtkomst för flera signaturer finns i dokumentationen.

   ![Lägga till användare i standarddomänen](./media/secure-web-app/ad-auth-add-user.png)

   *Lägga till användare i standarddolydsprincipen för Active Directory*

När du har lagt till Azure AD-konfigurationen och hemligheterna i Key Vault kan användare autentiseras i appen med hjälp av Azure OAuth-autentisering.
I appkoden hanteras detta av Azure Active Directory Authentication Library (ADAL).

När hemligheterna finns i Key Vault och programmet har tillgång till hemligheter och databasen kan programtjänsten nås\/via gatewayens program-URL (https: /GATEWAY_HASH.cloudapp.net), som du kan få från bladet.

Om du när du loggar in på Azure AD får du ett felmeddelande med "Användaren är inte registrerad i katalogen du försöker logga in på", måste du lägga till användaren. Om du vill lägga till användaren går du till fliken **Användare** i Azure AD och lägger till användaren manuellt genom att ange deras information eller bjuda in användaren genom att ange sin e-postadress som gästanvändare till Azure AD i bladet **Bjud in gäst.**

#### <a name="deploy-application-insights"></a>Distribuera Application Insights
Nu när appen har distribuerats och fungerar måste du hantera fel som uppstår i appen tillsammans med loggning och spårning av datainsamling.
Loggning och spårning av datainsamling ger en vy över granskningshändelser som inträffar i appen.

Application Insights är en tjänst som samlar in loggar som kan genereras av användare eller av systemet.

Så här skapar du en Application Insights-instans:

1. Sök efter **programinsikter** med hjälp av sökrutan i Azure-portalen.
2. Välj **Application Insights**. Ange den information som visas här för att skapa en instans.

   ![Skapa en Application Insights-förekomst](./media/secure-web-app/app-insights-data.png)

När distributionen är klar har du en Application Insights-instans.

När du har skapat application insights-instansen måste du göra appen medveten om instrumenteringsnyckeln som gör att den kan skicka loggar till molnet. Du gör detta genom att hämta application insights-nyckeln och använda den i programbiblioteken som Azure tillhandahåller för Application Insights. Det bästa är att lagra nycklar och hemligheter i Azure Key Vault för att skydda dem.

När du har skapat instansen Applications Insights för den grundläggande exempelappen måste du göra appen medveten om instrumenteringsnyckeln som gör att den kan skicka loggar till molnet.
I Key Vault `APPINSIGHTSKEY` anger du en hemlighet och anger dess värde som instrumenteringsnyckel. På så sätt kan appen skicka loggar och mått till Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementera multifaktorautentisering för Azure Active Directory

Administratörer måste se till att prenumerationskontona i portalen är skyddade. Prenumerationen är sårbar för attacker eftersom den hanterar de resurser som du har skapat. För att skydda prenumerationen aktiverar du multifaktorautentisering på fliken **Azure Active Directory** i prenumerationen.

Azure AD fungerar baserat på principer som tillämpas på en användare eller grupper av användare som uppfyller vissa kriterier.
Azure skapar en standardprincip som anger att administratörer behöver tvåfaktorsautentisering för att logga in på portalen.
När du har aktiverat den här principen kan du uppmanas att logga ut och logga in på Azure-portalen igen.

Så här aktiverar du MFA för administratörs inloggningar:

1. Gå till fliken **Azure Active Directory** i Azure-portalen
2. Välj villkorlig åtkomst under säkerhetskategorin. Följande skärm visas:

   ![Villkorlig åtkomst - principer](./media/secure-web-app/ad-mfa-conditional-add.png)

Om du inte kan skapa en ny princip:

1. Gå till fliken **MFA.**
2. Välj azure AD Premium **Free-utvärderingsversionen** för att prenumerera på den kostnadsfria utvärderingsversionen.

   ![Kostnadsfri utvärderingsversion av Azure AD Premium](./media/secure-web-app/ad-trial-premium.png)

Gå tillbaka till skärmen för villkorlig åtkomst.

1. Välj den nya principfliken.
2. Ange principens namn.
3. Markera de användare eller grupper som du vill aktivera MFA för.
4. Under **Access-kontroller**väljer du fliken **Bevilja** och väljer sedan **Kräv multifaktorautentisering** (och andra inställningar om du vill).

   ![Kräv MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

Du kan aktivera principen genom att markera kryssrutan högst upp på skärmen eller göra det på fliken **Villkorlig åtkomst.** När principen är aktiverad behöver användarna MFA för att logga in på portalen.

Det finns en baslinjeprincip som kräver MFA för alla Azure-administratörer. Du kan aktivera den direkt i portalen. Om du aktiverar den här principen kan den aktuella sessionen bli ogiltig och du måste logga in igen.

Om originalprincipen inte är aktiverad:

1. Välj **Kräv MFA för administratörer**.
2. Välj **Använd princip omedelbart**.

   ![Välj Använd princip omedelbart](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Använda Azure Sentinel för att övervaka appar och resurser

När ett program växer blir det svårt att aggregera alla säkerhetssignaler och mått som tas emot från resurser och göra dem användbara på ett åtgärdsorienterat sätt.

Azure Sentinel är utformat för att samla in data, identifiera möjliga typer av hot och ge insyn i säkerhetsincidenter.
Medan azure Sentinel väntar på manuella åtgärder kan den förlita sig på förskrivna spelböcker för att starta aviseringar och incidenthanteringsprocesser.

Exempelappen består av flera resurser som Azure Sentinel kan övervaka.
Om du vill konfigurera Azure Sentinel måste du först skapa en Log Analytics-arbetsyta som lagrar alla data som samlas in från de olika resurserna.

Så här skapar du den här arbetsytan:

1. Sök efter **Log Analytics**i sökrutan i Azure-portalen . Välj **Log Analytics-arbetsytor**.

   ![Sök efter log analytics-arbetsytor](./media/secure-web-app/sentinel-log-analytics.png)

    *Sök efter log analytics-arbetsytor*

2. På nästa sida väljer du **Lägg till** och anger sedan ett namn, en resursgrupp och en plats för arbetsytan.
   ![Skapa en Log Analytics-arbetsyta](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Skapa en Log Analytics-arbetsyta*

3. Använd sökrutan för att söka efter **Azure Sentinel**.

   ![Sök efter Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *Sök efter Azure Sentinel*

4. Välj **Lägg till** och välj sedan arbetsytan Log Analytics som du skapade tidigare.

   ![Lägga till en log analytics-arbetsyta](./media/secure-web-app/sentinel-workspace-add.png)

    *Lägga till en log analytics-arbetsyta*

5. Välj Datakopplingar under **Konfiguration**på sidan **Azure Sentinel - Data-anslutningsappar** . **Data connectors** Du ser en matris med Azure-tjänster som du kan länka till log Analytics-lagringsinstansen för analys i Azure Sentinel.

   ![Logga Analytics-dataanslutningar](./media/secure-web-app/sentinel-connectors.png)

    *Lägga till en dataanslutning i Azure Sentinel*

   Om du till exempel vill ansluta programgatewayen gör du så här:

   1. Öppna instansbladet azure application gateway.
   2. Under **Övervakning** väljer du **Diagnostikinställningar**.
   3. Välj **Lägg till diagnostikinställning**.

      ![Lägga till diagnostik för programgateway](./media/secure-web-app/sentinel-gateway-connector.png)

      *Lägga till diagnostik för programgateway*

   4. På sidan **Diagnostikinställningar** väljer du arbetsytan Log Analytics som du har skapat och väljer sedan alla mått som du vill samla in och skicka till Azure Sentinel. Välj **Spara**.

        ![Azure Sentinel-anslutningsinställningar](./media/secure-web-app/sentinel-connector-settings.png)

        *Azure Sentinel-anslutningsinställningar*

  Måtten från resursen finns i Azure Sentinel, där du kan fråga och undersöka dem.

   Lägg till samma mått i diagnostikinställningar för Azure Key Vault, den offentliga IP-adressen, Azure-databasen för PostgreSQL och alla tjänster som stöder diagnostikloggar i ditt konto.

När du har konfigurerat måtten har Azure Sentinel data att analysera.

## <a name="evaluate-and-verify"></a>Utvärdera och verifiera

När du har utvecklat och distribuerat arkitekturen måste du se till att koden och de distribuerade tjänsterna uppfyller säkerhetsstandarderna. Det här är några åtgärder som du kan vidta för att verifiera programvaran:

- Statisk kodanalys
- Sårbarhetsskanning
- Söka efter och åtgärda säkerhetsproblem i programberoenden

Dessa är de grundläggande byggstenarna för bästa praxis för säker utveckling.

### <a name="static-code-analysis"></a>Statisk kodanalys

För exempelappen innebär verifiering med statiska analysverktyg att hitta sårbarheter i appkoden med hjälp av tekniker som färgkontroll och dataflödesanalys. Statiska pythonanalysverktyg ger dig mer förtroende för att appen är säker.

**Linting (linting)**

PyFlakes, en Python linting bibliotek, hjälper dig att ta bort död kod och oanvända funktioner från appar, som visas här:

![Pyflakes](./media/secure-web-app/pyflakes.png)

Linting ger tips och möjliga ändringar som kan göra din kod renare och mindre fel benägna under körning.

**Pylint**

PyLint gav mest värde för det här projektet. Den utför kodstandardkontroller, felkontroll och refactoring tips för att säkerställa att koden som körs på servern är säker. Genom att använda PyLint för att uppdatera din kod kan du eliminera buggar och förbättra PyLint-klassificeringen, som följande bilder visar.

![Innan PyLint](./media/secure-web-app/before-pylint.png)

*Innan PyLint*

När du har åtgärdat några av de kodfel som hittades av linningsverktygen har du större förtroende för att koden inte är benägen att fel. Om du åtgärdar felen minskar säkerhetsriskerna avsevärt när koden distribueras till produktionsmiljöer.

![Efter Pylint](./media/secure-web-app/after-pylint.png)

*Efter PyLint*

### <a name="vulnerability-scanning"></a>Sårbarhetsskanning

[OWASP:s ZAP-verktyg](https://www.zaproxy.org/) är en sårbarhetsskanner för webbprogram med öppen källkod som du kan använda för att kontrollera exempelappen för säkerhetsproblem. Om du kör verktyget i exempelappen visas några möjliga fel och angreppsvektorer.

![ZAP-verktyg](./media/secure-web-app/zap-tool.png)

*ZAP-verktyg*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Hitta och åtgärda säkerhetsproblem i appberoenden

Om du vill söka efter och åtgärda programberoenden kan du använda [OWASP:s dependency check](https://owasp.org/www-project-dependency-check/).

Säkerhet är ett liknande program som kontrollerar beroenden. Du hittar den på [GitHub](https://github.com/pyupio/safety). Säkerhetsskanningar efter sårbarheter som finns i välkända sårbarhetsdatabaser.

![Säkerhet](./media/secure-web-app/pysafety.png)

*Säkerhet*

## <a name="next-steps"></a>Nästa steg

Följande artiklar kan hjälpa dig att utforma, utveckla och distribuera säkra program.

- [Design](secure-design.md)
- [Utveckla](secure-develop.md)
- [Distribuera](secure-deploy.md)
