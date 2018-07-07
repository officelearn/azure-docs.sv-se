---
title: Vara värd för en Ruby on Rails-webbplats på en Linux VM | Microsoft Docs
description: Ställ in och vara värd för en Ruby on Rails-baserad webbplats på Azure med en Linux-dator.
services: virtual-machines-linux
documentationcenter: ruby
author: rmcmurray
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: aad32685-3550-4bff-9c73-beb8d70b3291
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: ruby
ms.topic: article
ms.date: 06/27/2017
ms.author: robmcm
ms.openlocfilehash: 6ea1d249b7f9aec3a45923b162a97ce7f83d0d31
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901161"
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Ruby on Rails-webbprogram på en virtuell Azure-dator
Den här självstudien visar hur du vara värd för en Ruby on Rails-webbplats på Azure med hjälp av en Linux-dator.  

Den här självstudien har verifierats med hjälp av Ubuntu Server 14.04 LTS. Om du använder en annan Linux-distribution kan behöva du ändra stegen för att installera Rails.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md).  Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]
>

## <a name="create-an-azure-vm"></a>Skapa en Azure virtuell dator
Börja med att skapa en Azure-dator med en Linux-avbildning.

Du kan använda Azure portal eller Azure-kommandoradsgränssnittet (CLI) för att skapa den virtuella datorn.

### <a name="azure-portal"></a>Azure Portal
1. Logga in på den [Azure-portalen](https://portal.azure.com)
2. Klicka på **skapa en resurs**, Skriv ”Ubuntu Server 14.04” i sökrutan. Klicka på posten som returneras av sökningen. Distributionsmodellen, Välj **klassiska**, klicka på ”Skapa”.
3. På bladet grundläggande inställningar anger du värden för fälten som krävs: namn (för den virtuella datorn), användarnamn, autentiseringstyp och autentiseringsuppgifterna som motsvarande Azure-prenumeration, resursgrupp och plats.

   ![Skapa en ny Ubuntu-avbildning](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. När den virtuella datorn har etablerats visas klickar du på virtuella datorns namn och klickar på **slutpunkter** i den **inställningar** kategori. Hitta SSH-slutpunkt som visas under **fristående**.

   ![Standardslutpunkten](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>Azure CLI
Följ stegen i [skapa en virtuell dator som kör Linux][vm-instructions].

När den virtuella datorn har etablerats kan du hämta SSH-slutpunkten genom att köra följande kommando:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Installera Ruby on Rails
1. Använda SSH för att ansluta till den virtuella datorn.
2. Använd följande kommandon för att installera Ruby på den virtuella datorn från SSH-sessionen:

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    Installationen kan ta några minuter. När den är klar använder du följande kommando för att verifiera att Ruby är installerad:

        ruby -v

3. Använd följande kommando för att installera Rails:

        sudo gem install rails --no-rdoc --no-ri -V

    Använd--no-rdoc och--no-ri flaggor att hoppa över installationen i dokumentationen som är snabbare.
    Det här kommandot kommer förmodligen ta lång tid att köra, så att lägga till V - Visar information om under installationen.

## <a name="create-and-run-an-app"></a>Skapa och köra en app
När du är inloggad fortfarande via SSH, kör du följande kommandon:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

Den [nya](http://guides.rubyonrails.org/command_line.html#rails-new) kommandot skapar en ny Rails-app. Den [server](http://guides.rubyonrails.org/command_line.html#rails-server) kommando startar WEBrick webbservern som medföljer Rails. (För användning i produktion, skulle förmodligen du använder en annan server, till exempel Unicorn eller passagerartrafik.)

Du bör se utdata som liknar följande.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Lägga till en slutpunkt
1. Gå till [Azure-portalen] [https://portal.azure.com] och välj den virtuella datorn.

2. Välj **SLUTPUNKTER** i den **inställningar** på vänster edge-sidan.

3. Klicka på **lägga till** överst på sidan.

4. I den **Lägg till slutpunkt** dialogrutan anger du följande information:

   * **Namn på**: HTTP
   * **Protokollet**: TCP
   * **Offentlig port**: 80
   * **Privat port**: 3000
   * **Flytande PI adress**: inaktiverad
   * **Åtkomstkontrollistan - ordning**: 1001 eller ett annat värde som anger prioriteten för Åtkomstregeln.
   * **Åtkomstkontrollistan - namnet**: allowHTTP
   * **Åtkomstkontrollistan - åtgärd**: Tillåt
   * **Åtkomstkontrollistan - fjärrundernätet**: 1.0.0.0/16

     Den här slutpunkten har en offentlig port 80 som dirigerar trafik till den privata porten 3000, där Rails-servern lyssnar. Regeln för åtkomstkontrollista tillåter offentlig trafik på port 80.

     ![new-endpoint](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. Klicka på OK om du vill spara slutpunkten.

6. Ett meddelande visas som anger **sparar den virtuella datorslutpunkten**. När det här meddelandet försvinner är slutpunkten aktivt. Du kan nu testa ditt program genom att gå till DNS-namnet på den virtuella datorn. Webbplatsen ska se ut ungefär så här:

    ![rails-standardsida][default-rails-cloud]

## <a name="next-steps"></a>Nästa steg
I de här självstudierna har de flesta stegen manuellt. I en produktionsmiljö skulle du skriva din app på en utvecklingsdator och distribuera Azure-datorn. Dessutom de flesta produktionsmiljöer vara värd för Rails-program tillsammans med en annan serverprocess, till exempel Apache eller NginX, som hanterar begäran routning till flera instanser av Rails-program och betjänar statiska resurser. Mer information finns i http://guides.rubyonrails.org/routing.html.

Mer information om Ruby on Rails, på den [Ruby on Rails-guider][rails-guides].

Om du vill använda Azure-tjänster från din Ruby-App, se:

* [Store Ostrukturerade data i blobbar][blobs]
* [Store nyckel/värde-par med tabeller][tables]
* [Leverera innehåll med hög bandbredd med Content Delivery Network][cdn-howto]

<!-- WA.com links -->
[blobs]:../../../storage/blobs/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]:https://azure.microsoft.com/develop/ruby/app-services/
[tables]:../../../cosmos-db/table-storage-how-to-use-ruby.md
[vm-instructions]:createportal-classic.md

<!-- External Links -->
[rails-guides]:http://guides.rubyonrails.org/
[sqlite3]:http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]:./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]:./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]:./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
