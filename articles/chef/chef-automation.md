---
title: Snabb start – konfigurera en virtuell Windows-dator i Azure med chef
description: I den här snabb starten lär du dig att använda chef för att distribuera och konfigurera en virtuell Windows-dator i Azure
keywords: chef, Azure, DevOps, virtuell dator
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590071"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Snabb start – konfigurera en virtuell Windows-dator i Azure med chef

Chef ger dig möjlighet att leverera automatisering och önskade tillstånds konfigurationer.

Med den senaste Cloud API-versionen ger chef sömlös integrering med Azure, vilket ger dig möjlighet att etablera och distribuera konfigurations tillstånd via ett enda kommando.

I den här artikeln ställer du in din chefs miljö för att etablera virtuella Azure-datorer och gå igenom hur du skapar en princip-eller Cookbook och distribuerar sedan Cookbook till en virtuell Azure-dator.

## <a name="chef-basics"></a>Grunderna om chef

Innan du börjar med den här artikeln bör du [gå igenom de grundläggande begreppen chef](https://www.chef.io/chef).

Följande diagram visar chefs arkitekturen på hög nivå.

![Chefs arkitektur](media/chef-automation/chef-architecure.png)

Chef har tre huvudsakliga arkitektur komponenter: 
- Chefs Server – hanterings platsen och det finns två alternativ för chefs servern: en värdbaserad lösning eller en lokal lösning.
- Chefs klient (nod) – agenten som finns på de servrar som du hanterar.
- Chefs arbets Station – namnet på både admin-arbetsstationen (där du skapar principer och kör hanterings kommandon) och program varu paketet för chefs verktyg.

I allmänhet ser du **din arbets Station** som den plats där du kör kommandon och **chefs arbets Station** för program varu paketet.

Du kan till exempel hämta kniv-kommandot som en del av **chefs arbets stationen**, men du kör kniv-kommandon från **din arbets Station** för att hantera infrastrukturen.

Chef använder också begreppen *Kok böcker* och *recept*. Dessa villkor är de principer som definieras och tillämpas på servrarna.

## <a name="preparing-your-workstation"></a>Förbereda din arbets Station

Förbered först din arbets station genom att skapa en katalog för att lagra chefens konfigurationsfiler och Kok böcker.

Skapa en katalog som heter `C:\Chef`.

Hämta och installera den senaste versionen av [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) på din arbets Station.

## <a name="configure-azure-service-principal"></a>Konfigurera tjänstens huvudnamn för Azure

Vi använder ett huvud namn för tjänsten för att hjälpa oss att skapa Azure-resurser från vår chefs arbets Station.  Om du vill skapa relevant huvud namn för tjänsten med de behörigheter som krävs kör du följande kommandon i PowerShell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Anteckna ditt SubscriptionID, TenantID, ClientID och klient hemligheten (lösen ordet du angav tidigare i den här självstudien) eftersom du behöver dessa värden. 

## <a name="setup-chef-server"></a>Konfigurera chefs Server

Den här guiden förutsätter att du registrerar dig för en värdbaserad chef.

Om du inte redan använder en chefs Server kan du:

* Registrera dig för [värdbaserad chef](https://manage.chef.io/signup), vilket är det snabbaste sättet att komma igång med chef.
* Installera en fristående chefs server på en Linux-baserad dator genom att följa [installations anvisningarna](https://docs.chef.io/install_server.html) från [chefs dokumenten](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Skapa ett värd chefs konto

Registrera dig för ett värd chefs konto [här](https://manage.chef.io/signup).

Under registrerings processen blir du ombedd att skapa en ny organisation.

![Skapa organisations fönster](media/chef-automation/create-organization.png)

När din organisation har skapats laddar du ned start paketet.

![Konfigurera chef](media/chef-automation/configure-chef.png)

> [!NOTE]
> Om du får ett varnings meddelande om att dina nycklar kommer att återställas, är det dags att fortsätta eftersom vi inte har en befintlig infrastruktur som har kon figurer ATS ännu.
>

Den här zip-filen för start paket innehåller din organisations konfigurationsfiler och användar nyckel i `.chef`-katalogen.

`organization-validator.pem` måste laddas ned separat eftersom det är en privat nyckel och privata nycklar bör inte lagras på chefs servern. Från [chef hantera](https://manage.chef.io/), går du till avsnittet Administration och väljer "Återställ validerings nyckel", som innehåller en fil som du kan hämta separat. Spara filen till c:\chef.

### <a name="configuring-your-chef-workstation"></a>Konfigurera din chefs arbets Station

Extrahera innehållet i `chef-starter.zip` till `c:\chef`.

Kopiera alla filer under `chef-starter\chef-repo\.chef` till `c:\chef`-katalogen.

Kopiera `organization-validator.pem`-filen till `c:\chef`, om den sparas i `c:\Downloads`.

Din katalog bör nu se ut ungefär som i följande exempel.

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

Nu bör du ha fem filer och fyra kataloger (inklusive den tomma lagrings platsen-katalogen) i roten av c:\chef.

### <a name="edit-kniferb"></a>Redigera kniv. RB

PEM-filerna innehåller din organisation och dina administrativa privata nycklar för kommunikation och filen kniv. RB innehåller din kniva konfiguration. Vi kommer att behöva redigera filen kniv. RB.

Öppna filen kniv. RB i valfritt redigerings program. Den oförändrade filen bör se ut ungefär så här:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Lägg till följande information i din kniv. RB och ersätt plats hållarna med din information:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Dessa rader ser till att kniven refererar till Kok böcker-katalogen under `c:\chef\cookbooks`.

Din `knife.rb`-fil bör nu se ut ungefär som i följande exempel:

![Exempel på kniv fil](./media/chef-automation/knife-file-example.png)

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

## <a name="install-chef-workstation"></a>Installera chefs arbets Station

Hämta sedan [och installera chefs arbets stationen](https://downloads.chef.io/chef-workstation/).

Installera chefs arbets Station på standard platsen.

På Skriv bordet ser du en FV-PowerShell. Det här verktyget används för att interagera med chefs produkter. Med den här PowerShell-funktionen blir nya kommandon tillgängliga, till exempel `chef-run`-och chef CLI-kommandon (till exempel `chef`). Se den installerade versionen av chef-arbetsstationen och chefs verktygen med `chef -v`. Du kan också kontrol lera din arbets Stations version genom att välja **om chef arbets Station** från appen chef Workstation.

`chef --version` ska returnera något som:

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
> Sök vägens ordning är viktig! Om dina opscode sökvägar inte befinner sig i rätt ordning kommer problemen att uppstå.
>

Starta om arbets stationen innan du fortsätter.

### <a name="install-knife-azure"></a>Installera kniv Azure

I den här självstudien förutsätter vi att du använder Azure Resource Manager för att interagera med den virtuella datorn.

Installera kniv Azure-tillägget, som innehåller Azure-plugin-programmet.

Kör följande kommando.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argumentet `–-pre` garanterar att du får den senaste RC-versionen av kniv Azure-plugin-programmet som ger åtkomst till den senaste uppsättningen API: er.
>
>

Det är troligt att ett antal beroenden även kommer att installeras samtidigt.

![Utdata från installation av kniv – Azure](./media/chef-automation/install-knife-azure.png)

Kör följande kommando för att se till att allt är korrekt konfigurerat.

    knife azurerm server list

Om allt är korrekt konfigurerat visas en lista över tillgängliga Azure-avbildningar.

Grattis! Din arbets Station har kon figurer ATS!

## <a name="creating-a-cookbook"></a>Skapa en Cookbook

En Cookbook används av chef för att definiera en uppsättning kommandon som du vill köra på den hanterade klienten. Det är enkelt att skapa en Cookbook genom att bara använda kommandot `chef generate cookbook` för att generera Cookbook-mallen. Den här Cookbook är för en webb server som automatiskt distribuerar IIS.

Kör följande kommando under `C:\Chef directory`.

    chef generate cookbook webserver

Det här kommandot genererar en uppsättning filer under katalogen C:\Chef\cookbooks\webserver. Definiera sedan den uppsättning kommandon som chef klienten ska köra på den hanterade virtuella datorn.

Kommandona lagras i filen default. RB. I den här filen definierar du en uppsättning kommandon som installerar IIS, startar IIS och kopierar en mallfil till mappen `wwwroot`.

Ändra C:\chef\cookbooks\webserver\recipes\default.RB-filen och Lägg till följande rader.

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

Spara filen när du är färdig.

## <a name="creating-a-template"></a>Skapa en mall

I det här steget ska du skapa en mallfil som ska användas som `default.html` sida.

Kör följande kommando för att generera mallen:

    chef generate template webserver Default.htm

Navigera till `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`-filen. Redigera filen genom att lägga till några enkla *Hello World* HTML-kod och spara sedan filen.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Ladda upp Cookbook till chefs servern

I det här steget ska du göra en kopia av den Cookbook som du har skapat på den lokala datorn och ladda upp den till den server som är värd för chef. När den har laddats upp visas Cookbook under fliken **princip** .

    knife cookbook upload webserver

![Resultat av att installera Cookbook till chefs servern](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Distribuera en virtuell dator med kniv Azure

Distribuera en virtuell Azure-dator och tillämpa `Webserver`-Cookbook med kommandot `knife`.

Kommandot `knife` kommer också att installera IIS-webbtjänsten och standard webb sidan.

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

Kommandot `knife` skapar en *Standard_DS2_v2* virtuell dator med Windows Server 2016 installerat i regionen USA, västra. Ändra värdena till per app-behov.

När du har kört kommandot bläddrar du till Azure Portal för att se att datorn börjar etablera.

![Virtuell dator har allokerats](./media/chef-automation/virtual-machine-being-provisioned.png)

Kommando tolken visas härnäst.

![Kniva utdata vid skapande av virtuell dator](./media/chef-automation/knife-output-when-creating-vm.png)

När distributionen är klar visas den offentliga IP-adressen för den nya virtuella datorn. Klistra in det här värdet i en webbläsare för att visa den nya webbplatsen. När vi har distribuerat den virtuella datorn öppnade vi port 80 så den bör vara tillgänglig externt.   

![Testar den virtuella datorn](./media/chef-automation/testing-the-virtual-machine.png)

I det här exemplet används Creative HTML-kod.

Du kan också visa nodens status [chefs hantering](https://manage.chef.io/). 

![Visa nodens status](./media/chef-automation/viewing-node-status.png)

Glöm inte att du kan ansluta via en RDP-session från Azure Portal via port 3389.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Chef på Azure](/azure/chef/)