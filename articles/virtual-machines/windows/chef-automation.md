---
title: Distribution av Azure virtuella datorer med Chef | Microsoft Docs
description: "Lär dig hur du använder Chef för automatiserade virtuella distribution och konfiguration i Microsoft Azure"
services: virtual-machines-windows
documentationcenter: 
author: diegoviso
manager: timlt
tags: azure-service-management,azure-resource-manager
editor: 
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: diviso
ms.openlocfilehash: b6db0fbb4e0de896994954974ddcc39daad9c125
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatisera distribution av virtuella Azure-datorer med Chef
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef är ett bra verktyg för att leverera automation och önskade tillstånd konfigurationer.

Med vår senaste moln-api-versionen tillhandahåller Chef sömlös integrering med Azure, ger dig möjlighet att tillhandahålla och distribuera konfigureringstillstånd via ett enda kommando.

I den här artikeln kommer jag visar hur du konfigurerar miljön Chef att etablera virtuella Azure-datorer och beskriver hur du skapar en princip eller ”CookBook” och sedan distribuera den här cookbook till en virtuell Azure-dator.

Vi börjar!

## <a name="chef-basics"></a>Chef grunderna
Innan du börjar rekommenderar jag du granska de grundläggande principerna för Chef. Det är bra material <a href="http://www.chef.io/chef" target="_blank">här</a> och jag rekommenderar att du har en snabb läsning innan du utför den här genomgången. Jag kommer dock Sammanfattningsvis grunderna innan vi börjar.

Följande diagram visar den övergripande Chef-arkitekturen.

![][2]

Chef har tre huvudkomponenter arkitektur: Chef servern, Chef klienten (nod) och Chef-arbetsstation.

Chef-servern är vår hanteringsplatsen och det finns två alternativ för Chef-Server: en värdbaserad lösning eller en lokal lösning. Vi kommer att använda en värdbaserad lösning.

Chef-klienten (nod) är den agent som placeras på de servrar som du hanterar.

Chef arbetsstationen är vår arbetsstation där vi skapar våra principer och köra vår management-kommandon. Vi kör den **kniv** från Chef arbetsstationen för att hantera vår infrastruktur.

Det finns också begreppet ”Cookbooks” och ”recept”. Det här är ett effektivt sätt principer vi definiera och gäller för våra servrar.

## <a name="preparing-the-workstation"></a>Förbereder arbetsstationen
Först gör Förbered dig arbetsstationen. Jag använder en standard Windows-arbetsstation. Vi behöver skapa en katalog för lagring av våra konfigurationsfiler och cookbooks.

Först skapa en katalog med namnet C:\chef.

Skapa sedan den andra katalogen c:\chef\cookbooks.

Vi behöver nu hämta vårt Azure inställningsfilen så Chef kan kommunicera med våra Azure-prenumeration.

<!--Download your publish settings from [here](https://manage.windowsazure.com/publishsettings/).-->
Hämtar dina publiceringsinställningar med hjälp av PowerShell Azure [Get-AzurePublishSettingsFile](https://docs.microsoft.com/en-us/powershell/module/azure/get-azurepublishsettingsfile?view=azuresmps-4.0.0) kommando. 

Spara filen med publicera i C:\chef.

## <a name="creating-a-managed-chef-account"></a>Skapa ett hanterat konto Chef
Registrera dig för en värdbaserad Chef konto [här](https://manage.chef.io/signup).

Under registreringsprocessen, blir du ombedd att skapa en ny organisation.

![][3]

När din organisation har skapats kan du hämta starter kit.

![][4]

> [!NOTE]
> Om du får ett meddelande som varnar dig om att dina nycklar återställs, är det ok om du vill fortsätta eftersom vi har inga befintliga infrastruktur konfigurerad ännu.
> 
> 

Den här starter kit zip-filen innehåller din organisation konfigurationsfiler och nycklar.

## <a name="configuring-the-chef-workstation"></a>Konfigurera Chef arbetsstationen
Extrahera innehållet i chef-starter.zip till C:\chef.

Kopiera alla filer under chef-starter\chef-lagringsplatsen\.chef till c:\chef-katalogen.

Katalogen bör nu se ut ungefär som följande exempel.

![][5]

Du bör nu ha fyra filer, inklusive Azure publishing filen i roten av c:\chef.

PEM-filer innehåller din organisation och admin privata nycklar för kommunikation medan knife.rb-filen innehåller kniv konfigurationen. Vi behöver du redigera filen knife.rb.

Öppna filen i redigeringsprogrammet val och ändra ”cookbook_path” genom att ta bort den /... / från sökvägen så visas det på Nästa.

    cookbook_path  ["#{current_dir}/cookbooks"]

Lägg även till följande rad reflektion namnet på ditt Azure inställningsfilen för publicering.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Filen knife.rb bör nu se ut ungefär som följande exempel.

![][6]

Dessa rader säkerställer att kniv refererar till katalogen cookbooks under c:\chef\cookbooks och använder också vår Azure-publiceringsinställningsfil under åtgärder i Azure.

## <a name="installing-the-chef-development-kit"></a>Installera Chef Development Kit
Nästa [ladda ned och installera](http://downloads.getchef.com/chef-dk/windows) ChefDK (Chef Development Kit) att ställa in din Chef arbetsstation.

![][7]

Installera på standardplatsen för c:\opscode. Den här installationen tar cirka 10 minuter.

Bekräfta din sökvägsvariabeln innehåller poster för C:\opscode\chefdk\bin; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Om de inte är det, se till att du lägger till dessa sökvägar!

*OBSERVERA ATT SÖKVÄGEN ÄR VIKTIGT!* Om din opscode sökvägar inte kan i rätt ordning har du problem.

Starta om arbetsstationen innan du fortsätter.

Därefter installerar vi kniv Azure-tillägget. Detta ger kniv med pluginprogrammet ”Azure”.

Kör följande kommando.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argumentet – pre säkerställer du får den senaste RC-versionen av kniv Azure plugin-programmet som ger åtkomst till den senaste uppsättningen API: er.
> 
> 

Det är troligt att installeras även ett antal beroenden på samma gång.

![][8]

Kör följande kommando för att se till att allt är korrekt konfigurerad.

    knife azure image list

Om allt är korrekt konfigurerad, visas en lista över tillgängliga Azure avbildningar rulla.

Grattis. Arbetsstationen ställs in!

## <a name="creating-a-cookbook"></a>Skapa en Cookbook
En Cookbook används av Chef för att definiera en uppsättning kommandon som du vill köra på en hanterad klient. Det är enkelt att skapa en Cookbook och vi använder den **chef generera cookbook** kommando för att generera våra Cookbook mallen. Jag kommer att ringa upp webbservern Cookbook som jag vill att en princip som automatiskt distribuerar IIS.

Kör följande kommando under katalogen C:\Chef.

    chef generate cookbook webserver

Detta genererar en uppsättning filer i katalogen C:\Chef\cookbooks\webserver. Vi nu måste du definiera en uppsättning kommandon som vi vill gärna Chef klient ska köras på vår hanterade virtuella datorn.

Kommandon som lagras i filen default.rb. I den här filen ska jag definierar en uppsättning kommandon som installerar IIS, startar IIS och kopierar en mallfil till Wwwroot-mappen.

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
Som vi nämnt tidigare behöver vi skapa en mallfil som ska användas som sidan med våra default.html.

Kör följande kommando för att skapa mallen.

    chef generate template webserver Default.htm

Navigera till filen C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Redigera filen genom att lägga till vissa enkel ”Hello World” HTML-kod och spara sedan filen.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Överför Cookbook till servern Chef
I det här steget är vi tar en kopia av Cookbook som vi har skapat på våra lokala dator och överföra den till Chef Hosted Server. När du har överfört Cookbook visas under den **princip** fliken.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Distribuera en virtuell dator med kniv Azure
Vi kommer nu distribuera en virtuell Azure-dator och tillämpa ”webbserver”-Cookbook som installerar våra IIS web service och standard webbsida.

För att kunna göra detta måste använda den **kniv azure-servern skapa** kommando.

Är exempel på kommandot visas.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Parametrarna är självförklarande. Ersätta viss variabler och kör.

> [!NOTE]
> Via den kommandoraden jag också automatisera filterregler min endpoint nätverk med hjälp av parametern – tcp-slutpunkter. Jag har öppnat portarna 80 och 3389 att ge åtkomst till min sida och RDP-session.
> 
> 

När du kör kommandot Gå till Azure-portalen och börja etablera datorn visas.

![][13]

Kommandotolken visas.

![][10]

När installationen är klar, ska vi kunna ansluta till webbtjänsten via port 80 som vi har öppnat porten när vi etableras den virtuella datorn med kommandot kniv Azure. Eftersom den här virtuella datorn är den enda virtuella i min Molntjänsten, kommer den ansluts med molnet tjänst-url.

![][11]

Som du ser fick jag kreativa med HTML-kod.

Vi kan också ansluta via en RDP-session från Azure-portalen via port 3389 Glöm inte.

Hoppas det här har bra! Gå och starta din infrastruktur som koden resa med Azure idag!

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


<!--Link references-->
