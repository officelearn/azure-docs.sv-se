---
title: Distribution av Azure-dator med Chef | Microsoft Docs
description: Lär dig att använda Chef för att göra automatisk VM-distribution och konfiguration i Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: jeconnoc
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: diviso
ms.openlocfilehash: 31a0177ecfd87fa7ea78989b36141070c2ac193b
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865734"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatisera distribution av virtuella Azure-datorer med Chef
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef är ett bra verktyg för att leverera automation och önskad tillstånd konfigurationer.

Molnet api med senast versionen, Chef ger sömlös integration med Azure, vilket ger dig möjlighet att etablera och distribuera konfigureringstillstånd via ett enda kommando.

I den här artikeln kan du ställa in din Chef-miljö för att etablera Azure-datorer och beskriver hur du skapar en princip eller ”CookBook” och sedan distribuera den här cookbook till en Azure virtuell dator.

Vi börjar!

## <a name="chef-basics"></a>Chef-grunderna
Innan du börjar [gå igenom de grundläggande principerna för Chef](http://www.chef.io/chef). 

Följande diagram visar den övergripande Chef-arkitekturen.

![][2]

Chef har tre huvudsakliga Arkitekturkomponenter: Chef-Server, Chef-klienten (nod) och Chef-arbetsstation.

Chef-Server är hanteringsplatsen och det finns två alternativ för Chef-Server: en värdbaserad lösning eller en lokal lösning. Vi kommer att använda en värdbaserad lösning.

Chef-klienten (node) är den agent som är placerad på de servrar som du hanterar.

Chef-arbetsstation är administratörsarbetsstation där vi skapa principer och köra kommandon för hantering. Vi kör den **kniv** från Chef-arbetsstationen för att hantera infrastrukturen.

Det finns också begreppet ”Kokböcker” och ”recept”. Det här är ett effektivt sätt principerna som vi definiera och tillämpa på servrarna.

## <a name="preparing-the-workstation"></a>Förbereda arbetsstationen
Först kan Förbered arbetsstationen. Jag använder en standard Windows-arbetsstation. Vi måste du skapa en katalog för att lagra konfigurationsfiler och kokböcker.

Först skapa en katalog med namnet C:\chef.

Skapa sedan en andra katalog med namnet c:\chef\cookbooks.

Vi behöver nu att ladda ned filen med Azure så Chef kan kommunicera med Azure-prenumeration.

Hämta dina publiceringsinställningar med PowerShell Azure [Get-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/azure/get-azurepublishsettingsfile?view=azuresmps-4.0.0) kommando. 

Spara filen med publicera i C:\chef.

## <a name="creating-a-managed-chef-account"></a>Skapa ett hanterat konto Chef
Registrera dig för en värdbaserad Chef konto [här](https://manage.chef.io/signup).

Vid registreringen, blir du ombedd att skapa en ny organisation.

![][3]

När din organisation har skapats kan du hämta starter kit.

![][4]

> [!NOTE]
> Om du får ett meddelande som varnar dig om att dina nycklar återställs, är det ok att slutföra eftersom vi har ingen befintlig infrastruktur som konfigurerats ännu.
> 
> 

Den här starter kit zip-filen innehåller din organisation konfigurationsfiler och nycklar.

## <a name="configuring-the-chef-workstation"></a>Konfigurera Chef-arbetsstation
Extrahera innehållet i chef-starter.zip till C:\chef.

Kopiera alla filer under chef-starter\chef-lagringsplatsen\.chef till c:\chef-katalogen.

Din katalog bör nu se ut ungefär som i följande exempel.

![][5]

Du bör nu ha fyra filer, inklusive Azure publishing filen i roten av c:\chef.

PEM-filer innehåller din organisation och administratören privata nycklar för kommunikation medan knife.rb-filen innehåller kniv konfigurationen. Vi behöver du redigera filen knife.rb.

Öppna filen i redigeringsprogrammet väljer och ändra ”cookbook_path” genom att ta bort den /... och från sökvägen så visas det på Nästa.

    cookbook_path  ["#{current_dir}/cookbooks"]

Lägg även till följande rad återger namnet på din Azure inställningsfilen för publicering.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Filen knife.rb bör nu se ut ungefär som i följande exempel.

![][6]

Dessa rader säkerställer att kniv refererar till kokböcker-katalogen under c:\chef\cookbooks och använder även våra Azure-publiceringsinställningsfilen under Azure-åtgärder.

## <a name="installing-the-chef-development-kit"></a>Installera Chef Development Kit
Nästa [ladda ned och installera](http://downloads.getchef.com/chef-dk/windows) ChefDK (Chef Development Kit) att ställa in din Chef-arbetsstation.

![][7]

Installera på standardplatsen för c:\opscode. Den här installationen tar cirka 10 minuter.

Bekräfta PATH-variabeln innehåller poster för C:\opscode\chefdk\bin; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Om de inte är det, kontrollera att du lägger till sökvägarna!

*OBS ORDNINGEN PÅ SÖKVÄGEN ÄR VIKTIGT!* Om din opscode sökvägar inte kan i rätt ordning får du problem.

Starta om arbetsstationen innan du fortsätter.

Sedan installerar vi kniv Azure-tillägget. Detta ger kniv med ”Azure plugin-programmet”.

Kör följande kommando.

    chef gem install knife-azure ––pre

> [!NOTE]
> Argumentet – pre säkerställer du får den senaste RC-versionen av kniv Azure plugin-programmet som ger åtkomst till den senaste uppsättningen API: er.
> 
> 

Det är troligt att ett antal beroenden också kommer att installeras på samma gång.

![][8]

Om du vill se till att allt är korrekt konfigurerad, kör du följande kommando.

    knife azure image list

Om allt är korrekt konfigurerad, visas en lista över tillgängliga Azure-avbildningar Bläddra.

Grattis! Arbetsstationen har ställts in!

## <a name="creating-a-cookbook"></a>Skapa en Cookbook
En Kokbok används av Chef för att definiera en uppsättning kommandon som du vill köra på dina hanterad klient. Det är enkelt att skapa en Kokbok och vi använder den **chef generera cookbook** kommando för att generera Cookbook mallen. Jag kommer att kontakta dig webbservern Cookbook som jag vill ha en princip som automatiskt distribuerar IIS.

Kör följande kommando under katalogen C:\Chef.

    chef generate cookbook webserver

Detta genererar en uppsättning filer i katalogen C:\Chef\cookbooks\webserver. Vi behöver nu att definiera uppsättningen kommandon som vi vill Chef-klienten att köra på den hantera virtuella datorn.

Kommandona lagras i filen default.rb. I den här filen kommer jag definierar en uppsättning kommandon som installerar IIS, som startar IIS och kopierar en mallfil till Wwwroot-mappen.

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
Som vi nämnde tidigare måste vi skapa en mallfil som ska användas som default.html-sida.

Kör följande kommando för att skapa mallen.

    chef generate template webserver Default.htm

Navigera till filen C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Redigera filen genom att lägga till vissa enkel ”Hello World” HTML-kod och spara filen.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Ladda upp Cookbook till Chef-Server
I det här steget ska vi tar en kopia av Cookbook som vi har skapat på den lokala datorn och överföra den till den servern för värdbaserad Chef. När du har överfört Cookbook visas under den **princip** fliken.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Distribuera en virtuell dator med kniv Azure
Vi kommer nu distribuera en Azure-dator och tillämpar ”webbserver”-Cookbook som installerar IIS-tjänsten och standard web webbsidan.

Om du vill göra detta måste använda den **kniv azure-servern skapa** kommando.

Är exempel på kommandot visas.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Parametrarna är självförklarande. Ersätt dina specifika variabler och köra.

> [!NOTE]
> Via kommandoraden jag också automatisera min filterregler för slutpunkt-nätverk med hjälp av parametern – tcp-slutpunkter. Jag har öppnat portarna 80 och 3389 att ge åtkomst till min webbsida och RDP-session.
> 
> 

När du har kört kommandot kan gå till Azure-portalen och ser du din dator börja etablera.

![][13]

Kommandoprompten visas nästa.

![][10]

När distributionen är klar kan ska vi kunna ansluta till webbtjänsten via port 80 som vi hade öppnat porten när vi etableras den virtuella datorn med kommandot kniv Azure. Eftersom den här virtuella datorn är den enda virtuella datorn i min molntjänst, kommer den ansluts med cloud service-url.

![][11]

Som du ser jag kreativ med min HTML-kod.

Glöm inte vi kan också ansluta via en RDP-session från Azure-portalen via port 3389.

Jag hoppas att detta har varit bra! Gå och starta din infrastruktur som kod Azure-resa idag!

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
