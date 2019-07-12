---
title: Distribution av Azure-dator med Chef | Microsoft Docs
description: Lär dig att använda Chef för att göra automatisk VM-distribution och konfiguration i Microsoft Azure
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
ms.devlang: na
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 74b92c277b1d6eaa0984e55a70459bad59c2bf84
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719272"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatisera distribution av virtuella Azure-datorer med Chef

Chef är ett bra verktyg för att leverera automation och önskad tillstånd konfigurationer.

Med den senaste Molnets API-versionen tillhandahåller Chef sömlös integrering med Azure, vilket ger dig möjlighet att etablera och distribuera konfigureringstillstånd via ett enda kommando.

I den här artikeln kan du ställa in din Chef-miljö för att etablera Azure-datorer och beskriver hur du skapar en princip eller ”Cookbook” och sedan distribuera den här cookbook till en Azure virtuell dator.

## <a name="chef-basics"></a>Chef-grunderna
Innan du börjar [gå igenom de grundläggande principerna för Chef](https://www.chef.io/chef).

Följande diagram visar den övergripande Chef-arkitekturen.

![][2]

Chef har tre huvudsakliga strukturella komponenter: Chef-Server, Chef klienten (nod) och Chef-arbetsstation.

Chef-Server är hanteringsplatsen och det finns två alternativ för Chef-Server: en värdbaserad lösning eller en lokal lösning.

Chef-klienten (node) är den agent som är placerad på de servrar som du hanterar.

Chef-arbetsstation, som namnet för båda administratören arbetsstation där du skapar principer och köra kommandon för hantering och programpaket Chef verktyg.

I allmänhet bör du ser _arbetsstationen_ som den plats där du utföra åtgärder och _Chef arbetsstation_ för programpaketet.
Till exempel laddar du ned kommandot kniv som en del av _Chef arbetsstation_, men du kan köra kniv kommandon från _arbetsstationen_ att hantera infrastrukturen.

Chef använder också begreppet ”Kokböcker” och ”recept”, som är i själva verket principerna som vi definiera och tillämpa på servrarna.

## <a name="preparing-your-workstation"></a>Förbereda din arbetsstation

Förbered först arbetsstationen genom att skapa en katalog för att lagra konfigurationsfiler för Chef och kokböcker.

Skapa en katalog med namnet C:\Chef.

Ladda ned och installera senast [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) version in på din arbetsstation.

## <a name="configure-azure-service-principal"></a>Konfigurera tjänstens huvudnamn för Azure

I är enklaste med termer och Azure Service Principal ett tjänstkonto.   Vi kommer att använda ett huvudnamn för tjänsten som hjälper oss att skapa Azure-resurser från vår Chef-arbetsstation.  Vi behöver köra följande kommandon i PowerShell för att skapa relevanta tjänstens huvudnamn med behörigheterna som krävs:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Ta ner ditt prenumerations-ID, TenantID, ClientID och Klienthemlighet (lösenord som du angav ovan), du behöver det senare. 

## <a name="setup-chef-server"></a>Konfigurera Chef-Server

Den här handboken förutsätts att du ska registrera sig för värdbaserade Chef.

Om du inte redan använder en Chef-Server, kan du:

* Registrera dig för [finns Chef](https://manage.chef.io/signup), vilket är det snabbaste sättet att komma igång med Chef.
* Installera en fristående Chef-Server på linux-baserade datorn, och följa den [Installationsinstruktioner](https://docs.chef.io/install_server.html) från [Chef Docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Skapa en värdbaserad Chef-konto

Registrera dig för en värd Chef konto [här](https://manage.chef.io/signup).

När du registrerade dig blir du ombedd att skapa en ny organisation.

![][3]

När din organisation har skapats kan du hämta starter kit.

![][4]

> [!NOTE]
> Om du får ett meddelande som varnar dig om att dina nycklar kommer att återställas är det bra att slutföra eftersom vi har ingen befintlig infrastruktur som konfigurerats ännu.
>

Den här starter kit zip-filen innehåller din organisation konfigurationsfiler och nyckeln i den `.chef` directory.

Den `organization-validator.pem` måste laddas ned separat, eftersom det är en privat nyckel och privata nycklar bör inte lagras på Chef-Server. Från [Chef hantera](https://manage.chef.io/), gå till avsnittet Administration och välj ”återställa valideringsnyckel”, vilket ger en fil som du kan ladda ned separat. Spara filen på c:\chef.

### <a name="configuring-your-chef-workstation"></a>Konfigurera din Chef-arbetsstation

Extrahera innehållet i chef-starter.zip till c:\chef.

Kopiera alla filer under chef-starter\chef-lagringsplatsen\.chef till c:\chef-katalogen.

Kopiera den `organization-validator.pem` filen till c:\chef, om den sparas i c:\Downloads

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

Du bör nu ha fem filer och fyra kataloger (inklusive katalogen tom chef-lagringsplats) i roten av c:\chef.

### <a name="edit-kniferb"></a>Redigera knife.rb

PEM-filer som innehåller din organisation och administrativa privata nycklar för kommunikation och knife.rb-filen innehåller kniv konfigurationen. Vi behöver du redigera filen knife.rb.

Öppna filen knife.rb i valfri redigerare. Oförändrat filen bör se ut ungefär som:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Lägg till följande information i din knife.rb:

validation_client_name   "myorg-validator"

validation_key           "#{current_dir}/myorg.pem"

knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"

knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

knife[:azure_client_secret] =     "#1234p$wdchef19"


Dessa rader säkerställer att kniv refererar till kokböcker-katalogen under c:\chef\cookbooks och använder även Azure tjänstens huvudnamn som du skapade under Azure-åtgärder.

Filen knife.rb bör nu se ut ungefär så här:

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

## <a name="install-chef-workstation"></a>Installera Chef arbetsstation

Nästa [ladda ned och installera](https://downloads.chef.io/chef-workstation/) Chef arbetsstation.
Installera Chef arbetsstation standardplatsen. Den här installationen kan ta några minuter.

På skrivbordet ser du en ”CW PowerShell”, vilket är en miljö med verktyget du behöver för att interagera med Chef-produkter. PowerShell-CW tillgängliggör nya ad hoc-kommandon, till exempel `chef-run` samt som för traditionella Chef CLI-kommandon, till exempel `chef`. Se den installerade versionen av Chef arbetsstation och Chef-verktyg med `chef -v`. Du kan också kontrollera din arbetsstation version genom att välja ”om Chef arbetsstation” från appen Chef arbetsstation.

`chef --version` ska returnera något som liknar:

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
> Ordningen på sökvägen är viktigt! Om din opscode sökvägar inte kan i rätt ordning får du problem.
>

Starta om arbetsstationen innan du fortsätter.

### <a name="install-knife-azure"></a>Installera kniv Azure

Den här självstudien förutsätter att du använder Azure Resource Manager för att interagera med den virtuella datorn.

Installera tillägget kniv Azure. Detta ger kniv med ”Azure plugin-programmet”.

Kör följande kommando.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argumentet – pre säkerställer du får den senaste RC-versionen av kniv Azure plugin-programmet som ger åtkomst till den senaste uppsättningen API: er.
>
>

Det är troligt att ett antal beroenden också kommer att installeras på samma gång.

![][8]

Om du vill se till att allt är korrekt konfigurerad, kör du följande kommando.

    knife azurerm server list

Om allt är korrekt konfigurerad, visas en lista över tillgängliga Azure-avbildningar Bläddra.

Grattis! Arbetsstationen har ställts in!

## <a name="creating-a-cookbook"></a>Skapa en Cookbook

En Kokbok används av Chef för att definiera en uppsättning kommandon som du vill köra på dina hanterad klient. Det är enkelt att skapa en Cookbook, Använd bara den **chef generera cookbook** kommando för att generera Cookbook mallen. Den här cookbook är för en webbserver som automatiskt distribuerar IIS.

Kör följande kommando under katalogen C:\Chef.

    chef generate cookbook webserver

Det här kommandot genererar en uppsättning filer i katalogen C:\Chef\cookbooks\webserver. Definiera uppsättningen kommandon för Chef-klienten att köra på den hantera virtuella datorn.

Kommandona lagras i filen default.rb. Definiera en uppsättning kommandon som installerar IIS, som startar IIS och kopierar en mallfil till Wwwroot-mappen i den här filen.

Ändra filen C:\chef\cookbooks\webserver\recipes\default.rb och Lägg till följande rader.

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
I det här steget ska du generera en mallfil som användas som default.html-sidan.

Kör följande kommando för att skapa mallen:

    chef generate template webserver Default.htm

Navigera till den `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` filen. Redigera filen genom att lägga till vissa enkel ”Hello World” HTML-kod och spara filen.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Ladda upp Cookbook till Chef-Server
I det här steget ska göra du en kopia av Cookbook som du har skapat på den lokala datorn och överför den till den servern för värdbaserad Chef. När du har överfört Cookbook visas under den **princip** fliken.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Distribuera en virtuell dator med kniv Azure
Distribuera en Azure-dator och tillämpa ”webbserver”-Cookbook som installerar IIS-tjänsten och standard web webbsidan.

Om du vill göra detta måste använda den **kniv azurerm servern skapa** kommando.

Ett exempel på kommandot visas.

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


I exemplet ovan skapar en Standard_DS2_v2 virtuell dator med Windows Server 2016 i regionen USA, västra. Ersätt dina specifika variabler och köra.

> [!NOTE]
> Via kommandoraden jag också automatisera min filterregler för slutpunkt-nätverk med hjälp av parametern – tcp-slutpunkter. Jag har öppnat portarna 80 och 3389 att ge åtkomst till den webbsidan och RDP-session.
>
>

När du har kört kommandot kan du gå till Azure portal för att se din dator börja etablera.

![][15]

Kommandoprompten visas nästa.

![][16]

När distributionen är klar visas den offentliga IP-adressen för den nya virtuella datorn när distributionen har slutförts, du kan kopiera och klistra in den i en webbläsare och visa den webbplats som du har distribuerat. När vi har distribuerat den virtuella datorn öppnas vi port 80 så bör den vara tillgänglig externt.   

![][11]

Det här exemplet används kreativa HTML-kod.

Du kan också visa nodens status [Chef hantera](https://manage.chef.io/). 

![][17]

Glöm inte du kan också ansluta via en RDP-session från Azure-portalen via port 3389.

Tack! Gå och starta din infrastruktur som kod Azure-resa idag!

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
