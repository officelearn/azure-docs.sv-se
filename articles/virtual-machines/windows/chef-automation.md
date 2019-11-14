---
title: Distribution av virtuella Azure-datorer med chef
description: Lär dig hur du använder chef för automatisk distribution och konfiguration av virtuella datorer på Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: gwallace
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 58642cdbf164523390d5e4925290b43f6c05549b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039551"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatisera distribution av virtuella Azure-datorer med Chef

Chef är ett utmärkt verktyg för att leverera automatisering och önskade tillstånds konfigurationer.

Med den senaste Cloud API-versionen ger chef sömlös integrering med Azure, vilket ger dig möjlighet att etablera och distribuera konfigurations tillstånd via ett enda kommando.

I den här artikeln ställer du in din chefs miljö för att etablera virtuella Azure-datorer och gå igenom hur du skapar en princip eller "Cookbook" och sedan distribuerar den här Cookbook till en virtuell Azure-dator.

## <a name="chef-basics"></a>Grunderna om chef
Innan du börjar bör du [gå igenom de grundläggande begreppen chef](https://www.chef.io/chef).

Följande diagram illustrerar chefs arkitekturen på hög nivå.

![][2]

Chef har tre huvudsakliga arkitektur komponenter: chefs Server, chefs klient (nod) och chefs arbets Station.

Chefs servern är hanterings platsen och det finns två alternativ för chefs servern: en värdbaserad lösning eller en lokal lösning.

Chefs klienten (noden) är den agent som finns på de servrar som du hanterar.

Chefs arbets stationen, som är namnet på både administratörs arbets stationen där du skapar principer och kör hanterings kommandon och program varu paketet för chefs verktyg.

I allmänhet ser du _din arbets Station_ som den plats där du utför åtgärder och _chefs arbets Station_ för program varu paketet.
Du kan till exempel hämta kniv-kommandot som en del av _chef-arbets stationen_, men du kör kniv-kommandon från _din arbets Station_ för att hantera infrastrukturen.

Chef använder också begreppen "Kok böcker" och "recept", som i praktiken är de principer som vi definierar och tillämpar på servrarna.

## <a name="preparing-your-workstation"></a>Förbereda din arbets Station

Förbered först din arbets station genom att skapa en katalog för att lagra chefens konfigurationsfiler och Kok böcker.

Skapa en katalog med namnet C:\Chef.

Hämta och installera den senaste versionen av [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) på din arbets Station.

## <a name="configure-azure-service-principal"></a>Konfigurera tjänstens huvudnamn för Azure

I enkla och flesta villkor och Azure Service-huvudobjektet är ett tjänst konto.   Vi kommer att använda ett huvud namn för tjänsten för att hjälpa oss att skapa Azure-resurser från vår chefs arbets Station.  För att skapa relevant huvud namn för tjänsten med de behörigheter som krävs måste vi köra följande kommandon i PowerShell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Anteckna ditt SubscriptionID, TenantID, ClientID och klient hemligheten (lösen ordet du angav ovan). du behöver detta senare. 

## <a name="setup-chef-server"></a>Konfigurera chefs Server

Den här guiden förutsätter att du registrerar dig för en värdbaserad chef.

Om du inte redan använder en chefs Server kan du:

* Registrera dig för [värdbaserad chef](https://manage.chef.io/signup), vilket är det snabbaste sättet att komma igång med chef.
* Installera en fristående chefs server på en Linux-baserad dator genom att följa [installations anvisningarna](https://docs.chef.io/install_server.html) från [chefs dokumenten](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Skapa ett värd chefs konto

Registrera dig för ett värd chefs konto [här](https://manage.chef.io/signup).

Under registrerings processen blir du ombedd att skapa en ny organisation.

![][3]

När din organisation har skapats laddar du ned start paketet.

![][4]

> [!NOTE]
> Om du får ett varnings meddelande om att dina nycklar kommer att återställas, är det dags att fortsätta eftersom vi inte har en befintlig infrastruktur som har kon figurer ATS ännu.
>

Den här zip-filen för start paket innehåller din organisations konfigurationsfiler och användar nyckel i `.chef`-katalogen.

`organization-validator.pem` måste laddas ned separat, eftersom det är en privat nyckel och privata nycklar ska inte lagras på chefs servern. Från [chef hantera](https://manage.chef.io/), går du till avsnittet Administration och väljer "Återställ validerings nyckel", som innehåller en fil som du kan hämta separat. Spara filen till c:\chef.

### <a name="configuring-your-chef-workstation"></a>Konfigurera din chefs arbets Station

Extrahera innehållet i chef-starter. zip till c:\chef.

Kopiera alla filer under chef-starter\chef-repo\.chef till din c:\chef-katalog.

Kopiera `organization-validator.pem`-filen till c:\chef, om den sparas i c:\Downloads

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

Lägg till följande information i din kniv. RB:

validation_client_name "myorg-validator"

validation_key "# {current_dir}/myorg.pem"

knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"

knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

knife[:azure_client_secret] =     "#1234p$wdchef19"


Dessa rader ser till att kniven refererar till Kok böcker-katalogen under c:\chef\cookbooks och använder även det tjänst huvud namn för Azure som du skapade under Azure-åtgärder.

Din kniv. RB-fil bör nu se ut ungefär som i följande exempel:

![][14]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

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

Sedan [laddar du ned och installerar](https://downloads.chef.io/chef-workstation/) chef Workstation.
Installera chefs arbets station som standard plats. Den här installationen kan ta några minuter.

På Skriv bordet ser du en "FV PowerShell", som är en miljö som har lästs in med verktyget som du behöver för att interagera med chefs produkterna. Med den tillfälliga PowerShell: en blir nya Ad hoc-kommandon tillgängliga, till exempel `chef-run` och traditionella chef CLI-kommandon, till exempel `chef`. Se den installerade versionen av chef-arbetsstationen och chefs verktygen med `chef -v`. Du kan också kontrol lera din arbets Stations version genom att välja "om chef Workstation" från appen chef Workstation.

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
> Sök vägens ordning är viktig! Om dina opscode sökvägar inte finns i rätt ordning får du problem.
>

Starta om arbets stationen innan du fortsätter.

### <a name="install-knife-azure"></a>Installera kniv Azure

I den här självstudien förutsätter vi att du använder Azure Resource Manager för att interagera med den virtuella datorn.

Installera kniv Azure-tillägget. Detta ger kniv med "Azure-plugin".

Kör följande kommando.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argumentet – pre ser till att du får den senaste RC-versionen av kniv Azure-plugin-programmet som ger till gång till den senaste uppsättningen API: er.
>
>

Det är troligt att ett antal beroenden även kommer att installeras samtidigt.

![][8]

Kör följande kommando för att se till att allt är korrekt konfigurerat.

    knife azurerm server list

Om allt är korrekt konfigurerat visas en lista över tillgängliga Azure-avbildningar.

Grattis! Din arbets Station har kon figurer ATS!

## <a name="creating-a-cookbook"></a>Skapa en Cookbook

En Cookbook används av chef för att definiera en uppsättning kommandon som du vill köra på den hanterade klienten. Det är enkelt att skapa en Cookbook med hjälp av kommandot **chef generate Cookbook** för att skapa Cookbook-mallen. Den här Cookbook är för en webb server som automatiskt distribuerar IIS.

Kör följande kommando under C:\Chef-katalogen.

    chef generate cookbook webserver

Det här kommandot genererar en uppsättning filer under katalogen C:\Chef\cookbooks\webserver. Definiera sedan den uppsättning kommandon som chef klienten ska köra på den hanterade virtuella datorn.

Kommandona lagras i filen default. RB. I den här filen definierar du en uppsättning kommandon som installerar IIS, startar IIS och kopierar en mallfil till mappen wwwroot.

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
I det här steget ska du skapa en mallfil som används som standard-HTML-sida.

Kör följande kommando för att generera mallen:

    chef generate template webserver Default.htm

Navigera till `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`-filen. Redigera filen genom att lägga till en enkel "Hello World"-HTML-kod och spara sedan filen.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Ladda upp Cookbook till chefs servern
I det här steget ska du göra en kopia av den Cookbook som du har skapat på den lokala datorn och ladda upp den till den server som är värd för chef. När den har laddats upp visas Cookbook under fliken **princip** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Distribuera en virtuell dator med kniv Azure
Distribuera en virtuell Azure-dator och Använd Cookbook "webserver" som installerar IIS-webbtjänsten och standard webb sidan.

För att göra detta använder du kommandot **kniv azurerm Server Create** .

Ett exempel på kommandot visas härnäst.

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


Exemplet ovan skapar en Standard_DS2_v2 virtuell dator med Windows Server 2016 installerat i regionen USA, västra. Ersätt dina specifika variabler och kör dem.

> [!NOTE]
> På kommando raden automatiserar jag också mina nätverks filter regler för slut punkter med hjälp av parametern – TCP-endpoints. Jag har öppnat portarna 80 och 3389 för att ge åtkomst till webb sidan och RDP-sessionen.
>
>

När du har kört kommandot går du till Azure Portal för att se att datorn börjar etablera.

![][15]

Kommando tolken visas härnäst.

![][16]

När distributionen är klar visas den offentliga IP-adressen för den nya virtuella datorn när distributionen ska slutföras. du kan kopiera den här och klistra in den i en webbläsare och visa den webbplats som du har distribuerat. När vi har distribuerat den virtuella datorn öppnade vi port 80 så den bör vara tillgänglig externt.   

![][11]

I det här exemplet används Creative HTML-kod.

Du kan också visa nodens status [chefs hantering](https://manage.chef.io/). 

![][17]

Glöm inte att du kan ansluta via en RDP-session från Azure Portal via port 3389.

Tack! Sätt igång infrastrukturen som kod resa med Azure idag!

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png
[14]: media/chef-automation/14.png
[15]: media/chef-automation/15.png
[16]: media/chef-automation/16.png
[17]: media/chef-automation/17.png


<!--Link references-->
