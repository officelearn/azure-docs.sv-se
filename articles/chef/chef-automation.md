---
title: Snabbstart – Konfigurera en virtuell Windows-dator i Azure med Chef
description: I den här snabbstarten får du lära dig hur du använder Chef för att distribuera och konfigurera en virtuell Windows-dator i Azure
keywords: ', azurblå, devops, virtuell maskin'
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77590071"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Snabbstart – Konfigurera en virtuell Windows-dator i Azure med Chef

Med Chef kan du leverera automatisering och önskade tillståndskonfigurationer.

Med den senaste moln-API-versionen ger Chef sömlös integrering med Azure, vilket ger dig möjlighet att etablera och distribuera konfigurationstillstånd via ett enda kommando.

I den här artikeln konfigurerar du din chef-miljö för att etablera virtuella Azure-datorer och gå igenom skapandet av en princip eller kokbok och sedan distribuera den här kokboken till en virtuell Azure-dator.

## <a name="chef-basics"></a>Grunderna i kocken

Innan du börjar med den här artikeln, [granska de grundläggande begreppen i Chef](https://www.chef.io/chef).

Följande diagram visar den högnivåarkitekturen Chef.

![Chef arkitektur](media/chef-automation/chef-architecure.png)

Chef har tre huvudsakliga arkitektoniska komponenter: 
- Chef Server - Hanteringsplatsen och det finns två alternativ för Chef Server: en värdbaserad lösning eller en lokal lösning.
- Chef Client (nod) - Agenten som sitter på servrarna du hanterar.
- Chef Workstation - Namnet på både admin arbetsstation (där du skapar principer och kör ledningskommandon) och mjukvarupaketet med Chef-verktyg.

I allmänhet ser du **din arbetsstation** som den plats där du kör kommandon och **Chef Workstation** för programvarupaketet.

Du kan till exempel hämta knivkommandot som en del av **Chef Workstation,** men du kör knivkommandon från **din arbetsstation** för att hantera infrastruktur.

Chef använder också begreppen *kokböcker* och *recept*. Dessa termer är de principer som definieras och tillämpas på servrarna.

## <a name="preparing-your-workstation"></a>Förbereda din arbetsstation

Förbered först din arbetsstation genom att skapa en katalog för att lagra Chef-konfigurationsfiler och kokböcker.

Skapa en katalog som heter `C:\Chef`.

Hämta och installera den senaste [Azure CLI-versionen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) på din arbetsstation.

## <a name="configure-azure-service-principal"></a>Konfigurera tjänstens huvudnamn för Azure

Vi använder en serviceansvarig för att hjälpa oss att skapa Azure-resurser från vår Chef Workstation.  Om du vill skapa relevant tjänsthuvudnamn med de behörigheter som krävs kör du följande kommandon i PowerShell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Notera ditt SubscriptionID, TenantID, ClientID och Client Secret (lösenordet du angav tidigare i den här självstudien) eftersom du behöver dessa värden. 

## <a name="setup-chef-server"></a>Chef-server för konfigurera

Den här guiden förutsätter att du registrerar dig för Hosted Chef.

Om du inte redan använder en Chef Server kan du:

* Registrera dig för [Hosted Chef](https://manage.chef.io/signup), vilket är det snabbaste sättet att komma igång med Chef.
* Installera en fristående Chef Server på Linux-baserad dator, enligt [installationsanvisningarna](https://docs.chef.io/install_server.html) från [Chef Docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Skapa ett värdchefskonto

Registrera dig för ett Hosted Chef-konto [här](https://manage.chef.io/signup).

Under registreringsprocessen blir du ombedd att skapa en ny organisation.

![Skapa organisationsfönster](media/chef-automation/create-organization.png)

När din organisation har skapats laddar du ned startpaketet.

![Konfigurera chef](media/chef-automation/configure-chef.png)

> [!NOTE]
> Om du får en uppmaning om att dina nycklar kommer att återställas, är det okej att fortsätta eftersom vi inte har någon befintlig infrastruktur konfigurerad ännu.
>

Den här zip-filen för startpaket innehåller organisationens konfigurationsfiler och användarnyckel i katalogen. `.chef`

Måste `organization-validator.pem` hämtas separat eftersom det är en privat nyckel och privata nycklar bör inte lagras på Chef Server. Från [Chef Manage](https://manage.chef.io/)går du till avsnittet Administration och väljer "Återställ valideringsnyckel", som innehåller en fil där du kan hämta separat. Spara filen på c:\chef.

### <a name="configuring-your-chef-workstation"></a>Konfigurera din Chef-arbetsstation

Extrahera innehållet i `chef-starter.zip` `c:\chef`till .

Kopiera alla `chef-starter\chef-repo\.chef` filer `c:\chef` under till din katalog.

Kopiera `organization-validator.pem` filen `c:\chef`till , om den `c:\Downloads`sparas i .

Katalogen ska nu se ut ungefär som följande exempel.

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

Du bör nu ha fem filer och fyra kataloger (inklusive den tomma chef-repo katalogen) i roten av c:\chef.

### <a name="edit-kniferb"></a>Redigera kniv.rb

PEM-filerna innehåller din organisation och administrativa privata nycklar för kommunikation och filen knife.rb innehåller din knivkonfiguration. Vi måste redigera filen knife.rb.

Öppna filen knife.rb i den redaktion du väljer. Den oförändrade filen ska se ut ungefär som:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Lägg till följande information till din knife.rb och ersätt platshållarna med din information:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Dessa rader kommer att se till att `c:\chef\cookbooks`Kniv refererar kokböcker katalogen under .

Filen `knife.rb` ska nu se ut ungefär som följande exempel:

![Exempel på knivfil](./media/chef-automation/knife-file-example.png)

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Installera chefarbetsstation

Hämta [sedan och installera Chef Workstation](https://downloads.chef.io/chef-workstation/).

Installera Chef Workstation till standardplatsen.

På skrivbordet visas en CW PowerShell. Detta verktyg används för att interagera med Chef produkter. CW PowerShell gör nya kommandon tillgängliga, till exempel `chef-run` och `chef`Chef CLI-kommandon (till exempel ). Se din installerade version av Chef Workstation och Chef-verktygen med `chef -v`. Du kan också kontrollera din arbetsstationsversion genom att välja **Om Chef Workstation** från Chef Workstation-appen.

`chef --version`bör återvända något i stil med:

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> Ordningen på stigen är viktig! Om dina opscode-sökvägar inte är i rätt ordning uppstår problem.
>

Starta om arbetsstationen innan du fortsätter.

### <a name="install-knife-azure"></a>Installera Kniv Azure

Den här självstudien förutsätter att du använder Azure Resource Manager för att interagera med din virtuella dator.

Installera tillägget Knife Azure, som innehåller Azure-insticksprogrammet.

Kör följande kommando.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argumentet `–-pre` säkerställer att du får den senaste RC-versionen av Knife Azure Plugin som ger åtkomst till den senaste uppsättningen API:er.
>
>

Det är troligt att ett antal beroenden också kommer att installeras samtidigt.

![Utdata från installation av kniv-azure](./media/chef-automation/install-knife-azure.png)

Kör följande kommando för att säkerställa att allt är korrekt konfigurerat.

    knife azurerm server list

Om allt är korrekt konfigurerat visas en lista över tillgängliga Azure-avbildningar bläddrar igenom.

Grattis! Din arbetsstation är inställd!

## <a name="creating-a-cookbook"></a>Skapa en kokbok

En kokbok används av Chef för att definiera en uppsättning kommandon som du vill köra på din hanterade klient. Skapa en kokbok är enkelt, bara använda `chef generate cookbook` kommandot för att generera kokbok mall. Den här kokboken är till för en webbserver som automatiskt distribuerar IIS.

Kör `C:\Chef directory`följande kommando under ditt.

    chef generate cookbook webserver

Det här kommandot genererar en uppsättning filer under katalogen C:\Chef\kokböcker\webbserver. Definiera sedan uppsättningen kommandon för chefklienten som ska köras på den hanterade virtuella datorn.

Kommandona lagras i filen default.rb. I den här filen definierar du en uppsättning kommandon som installerar IIS, `wwwroot` startar IIS och kopierar en mallfil till mappen.

Ändra filen C:\chef\kokböcker\webserver\recipes\default.rb och lägg till följande rader.

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Spara filen när du är klar.

## <a name="creating-a-template"></a>Skapa en mall

I det här steget ska du generera en `default.html` mallfil som ska användas som sida.

Kör följande kommando för att generera mallen:

    chef generate template webserver Default.htm

Navigera till `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` filen. Redigera filen genom att lägga till några enkla *Hello World* HTML-kod och sedan spara filen.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Ladda upp kokboken till Chef Server

I det här steget gör du en kopia av kokboken som du har skapat på den lokala datorn och laddar upp den till chef värdservern. När kokboken har laddats upp visas den under fliken **Policy.**

    knife cookbook upload webserver

![Resultat av att installera kokbok till Chef Server](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Distribuera en virtuell dator med Knife Azure

Distribuera en virtuell Azure-dator och tillämpa `Webserver` kokboken med `knife` kommandot.

Kommandot `knife` kommer också att installera IIS webbtjänst och standardwebbsida.

```bash
    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"
```

I `knife` kommandoexemplet skapas en *Standard_DS2_v2* virtuell dator med Windows Server 2016 installerat i regionen Västra USA. Ändra dessa värden enligt appens behov.

När du har kört kommandot bläddrar du till Azure-portalen för att se din dator börja etablera.

![Virtuell dator etableras](./media/chef-automation/virtual-machine-being-provisioned.png)

Kommandotolken visas härnäst.

![Knivutgång när du skapar virtuell maskin](./media/chef-automation/knife-output-when-creating-vm.png)

När distributionen är klar visas den offentliga IP-adressen för den nya virtuella datorn. Klistra in det här värdet i en webbläsare för att visa den nya webbplatsen. När vi distribuerade den virtuella datorn öppnade vi port 80 så den ska vara tillgänglig externt.   

![Testa den virtuella datorn](./media/chef-automation/testing-the-virtual-machine.png)

I det här exemplet används kreativ HTML-kod.

Du kan också visa nodens status [Chef Manage](https://manage.chef.io/). 

![Visa nodstatus](./media/chef-automation/viewing-node-status.png)

Glöm inte att du också kan ansluta via en RDP-session från Azure-portalen via port 3389.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Chef på Azure](/azure/chef/)