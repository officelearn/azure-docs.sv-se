---
title: "Värd för en Ruby på spår webbplats på en Linux-VM | Microsoft Docs"
description: "Ställ in och vara värd för en Ruby på spår-baserade webbplats på Azure med hjälp av en virtuell Linux-dator."
services: virtual-machines-linux
documentationcenter: ruby
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: aad32685-3550-4bff-9c73-beb8d70b3291
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: ruby
ms.topic: article
ms.date: 06/27/2017
ms.author: robmcm
ms.openlocfilehash: fb6ded1dcba2ac0f78fc6f1f4f7de9238cd752bd
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2017
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Ruby on Rails-webbprogram på en virtuell Azure-dator
Den här kursen visar hur som värd för en Ruby på spår webbplats på Azure med hjälp av en virtuell Linux-dator.  

Den här självstudiekursen har verifierats med hjälp av Ubuntu Server 14.04 LTS. Om du använder en annan Linux distributionsplats kan behöva du ändra steg för att installera spår.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md).  Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]
>

## <a name="create-an-azure-vm"></a>Skapa en virtuell dator i Azure
Börja med att skapa en virtuell dator i Azure med en Linux-avbildning.

Du kan använda Azure-portalen eller Azure-kommandoradsgränssnittet (CLI) för att skapa den virtuella datorn.

### <a name="azure-portal"></a>Azure Portal
1. Logga in på den [Azure-portalen](https://portal.azure.com)
2. Klicka på **ny**, Skriv ”Ubuntu Server 14.04” i sökrutan. Klicka på posten som returneras av sökningen. Distributionsmodell, Välj **klassiska**, klicka på ”Skapa”.
3. Ange värden för de obligatoriska fälten i bladet grundläggande: namn (VM), användarnamn, autentiseringstyp och autentiseringsuppgifter för motsvarande Azure-prenumeration, resursgrupp och plats.

   ![Skapa en ny Ubuntu-avbildning](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. När den virtuella datorn har etablerats klickar du på namnet på virtuella datorn och klickar på **slutpunkter** i den **inställningar** kategori. Hitta SSH-slutpunkt, som visas under **fristående**.

   ![Standardslutpunkten](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>Azure CLI
Följ stegen i [skapa en virtuell dator kör Linux][vm-instructions].

När den virtuella datorn har etablerats kan du hämta SSH-slutpunkten genom att köra följande kommando:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Installera Ruby spår
1. Använda SSH för att ansluta till den virtuella datorn.
2. Använd följande kommandon för att installera Ruby på den virtuella datorn från SSH-session:

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    Installationen kan ta några minuter. När den har slutförts använder du följande kommando för att verifiera att Ruby har installerats:

        ruby -v

3. Använd följande kommando för att installera spår:

        sudo gem install rails --no-rdoc --no-ri -V

    Använd--Nej rdoc och--Nej ri flaggorna att hoppa över installerar dokumentation, vilket går snabbare.
    Det här kommandot kommer förmodligen ta lång tid att köra, så att lägga till V - Visar information om installationsförloppet.

## <a name="create-and-run-an-app"></a>Skapa och köra en app
När du är inloggad fortfarande via SSH, kör du följande kommandon:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

Den [nya](http://guides.rubyonrails.org/command_line.html#rails-new) kommando skapar en ny app spår. Den [server](http://guides.rubyonrails.org/command_line.html#rails-server) kommando startar webbservern WEBrick som medföljer spår. (För produktion, du förmodligen vill använda en annan server, till exempel Unicorn eller passagerare.)

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

2. Välj **SLUTPUNKTER** i den **inställningar** längs vänster kant sidan.

3. Klicka på **lägga till** överst på sidan.

4. I den **lägga till slutpunkten** dialogrutan anger du följande information:

   * **Namnet**: HTTP
   * **Protokollet**: TCP
   * **Offentlig port**: 80
   * **Privat port**: 3000
   * **Flytande PI adress**: inaktiverad
   * **ACL - ordning**: 1001 eller ett annat värde som anger prioritet för den här regeln.
   * **ACL - namnet**: allowHTTP
   * **ACL - åtgärd**: Tillåt
   * **ACL - fjärrundernät**: 1.0.0.0/16

     Den här slutpunkten har en offentlig port 80 som dirigerar trafik till den privata porten 3000, där spår servern lyssnar. Regeln för åtkomstkontrollista tillåter offentliga trafik på port 80.

     ![ny slutpunkt](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. Klicka på OK om du vill spara slutpunkten.

6. Ett meddelande visas som anger **sparar den virtuella datorslutpunkten**. När det här meddelandet försvinner är slutpunkten aktiv. Du kan nu testa programmet genom att gå till DNS-namnet på den virtuella datorn. Webbplatsen ska se ut ungefär så här:

    ![spår standardsida][default-rails-cloud]

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen har de flesta av stegen manuellt. I en produktionsmiljö skulle du skriva din app på en utvecklingsdator och distribuera den till Azure-VM. De flesta produktionsmiljöer värd också spår program tillsammans med en annan server-processen, till exempel Apache eller NginX, som hanterar begäran routning till flera instanser av programmet spår och betjänar statiska resurser. Mer information finns i http://rubyonrails.org/deploy/.

Läs mer om Ruby spår den [Ruby på spår guider][rails-guides].

Om du vill använda Azure-tjänster från tillämpningsprogrammet Ruby, se:

* [Lagra Ostrukturerade data med blobbar][blobs]
* [Lagra nyckel/värde-par med tabeller][tables]
* [Hög bandbredd innehåll med innehållet Delivery Network][cdn-howto]

<!-- WA.com links -->
[blobs]:../../../storage/blobs/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]:https://azure.microsoft.com/develop/ruby/app-services/
[tables]:../../../cosmos-db/table-storage-how-to-use-ruby.md
[vm-instructions]:createportal.md

<!-- External Links -->
[rails-guides]:http://guides.rubyonrails.org/
[sqlite3]:http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]:./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]:./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]:./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
