---
title: "Konfigurera en utvecklingsmiljö i Mac OS X | Microsoft Docs"
description: "Installera runtime, SDK och verktyg och skapa ett lokalt utvecklingskluster. När du har slutfört den här installationen är du redo att börja bygga program i Mac OS X."
services: service-fabric
documentationcenter: java
author: saysa
manager: raunakp
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: e5d14eb0a656d67030f4c0d3d510aec0e9cafae7
ms.lasthandoff: 03/28/2017


---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Konfigurera din utvecklingsmiljö i Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Du kan skapa Service Fabric-program som körs i Linux-kluster i Mac OS X. Den här artikeln visar hur du konfigurerar din utvecklingsmiljö i Mac.

## <a name="prerequisites"></a>Krav
Service Fabric kan inte köras internt i OS X. För att du ska kunna köra ett lokalt Service Fabric-kluster tillhandahåller vi en förkonfigurerad virtuell Ubuntu-dator med Vagrant och VirtualBox. Innan du börjar behöver du:

* [Vagrant (v1.8.4 eller senare)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

>[!NOTE]
> Du behöver använda versioner som både stöds av Vagrant och VirtualBox. Vagrant kan bete sig oförutsägbart med en VirtualBox-version som inte stöds.
>

## <a name="create-the-local-vm"></a>Skapa den lokala virtuella datorn
För att skapa den lokala virtuella datorn med ett 5-nods Service Fabric-kluster utför du följande steg:

1. Klona `Vagrantfile`-databasen

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
    Det här steget hämtar filen `Vagrantfile` som innehåller VM-konfigurationen tillsammans med den plats som den virtuella datorn laddas ned från.

   
2. Navigera till den lokala repoklonen

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (Valfritt) Ändra standardinställningarna för virtuell dator

    Som standard konfigureras den lokala virtuella datorn på följande sätt:

   * 3 GB minne allokeras
   * Det privata värdnätverket konfigureras på IP 192.168.50.50 för att möjliggöra genomströmning av trafik från Mac-värden

     Du kan ändra dessa inställningar eller lägga till en annan konfiguration för den virtuella datorn i `Vagrantfile`. En fullständig lista över konfigurationsalternativen finns i [Vagrant-dokumentationen](http://www.vagrantup.com/docs).
4. Skapa den virtuella datorn

    ```bash
    vagrant up
    ```

   I det här steget laddas den förkonfigurerade VM-avbildningen ned. Avbildningen startas lokalt och ett lokalt Service Fabric-kluster konfigureras sedan på datorn. Det här kan ta några minuter. Om installationen lyckas, får du upp ett meddelande som indikerar att klustret startas.

    ![Klusterinstallationen startar efter att den virtuella datorn har etablerats][cluster-setup-script]

>[!TIP]
> Om nedladdningen av den virtuella datorn tar lång tid kan du hämta den med hjälp av wget eller curl eller via en webbläsare genom att gå till länken som anges i **config.vm.box_url** i filen `Vagrantfile`. När du hämtat den lokalt redigerar du `Vagrantfile` så att den pekar på den lokala sökväg som du laddade ned avbildningen till. Om du till exempel laddade ned avbildningen till /home/users/test/azureservicefabric.tp8.box anger du **config.vm.box_url** till den sökvägen.
>

5. Testa att klustret är korrekt installerat genom att gå till Service Fabric Explorer på http://192.168.50.50:19080/Explorer (förutsatt att du har behållit standard-IP för det privata nätverket).

    ![Service Fabric Explorer på Mac-värddatorn][sfx-mac]

## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Installera Service Fabric-plugin-programmet för Eclipse Neon

Service Fabric innehåller ett plugin-program för **Eclipse Neon för Java IDE** som förenklar processen med att skapa, bygga och distribuera Java-tjänster. Du kan följa installationsstegen i den här allmänna [dokumentationen](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) som beskriver hur du installerar eller uppdaterar Service Fabric Eclipse-plugin-programmet.

## <a name="using-service-fabric-eclipse-plugin-on-mac"></a>Använda Service Fabric Eclipse-plugin-programmet på Mac

Se till att du har gått igenom stegen som beskrivs i [dokumentationen för Service Fabric Eclipse-plugin-programmet](service-fabric-get-started-eclipse.md). Stegen för att skapa, utveckla och distribuera ett Service Fabric Java-program med vagrant-guest-behållaren på en Mac-värd är i princip samma som stegen i den allmänna dokumentationen, förutom för följande:

* Eftersom Service Fabric-biblioteken krävs av Service Fabric Java-programmet måste Eclipse-projektet skapas på en delad sökväg. Som standard delas innehållet i sökvägen på din värd där ``Vagrantfile`` finns med sökvägen ``/vagrant`` för gästen.
* Om ``Vagrantfile`` finns i en sökväg, t.ex. ``~/home/john/allprojects/``, måste du skapa Service Fabric-projektet ``MyActor`` på platsen ``~/home/john/allprojects/MyActor``, och sökvägen till din Eclipse-arbetsyta är i så fall ``~/home/john/allprojects``.

## <a name="next-steps"></a>Nästa steg
<!-- Links -->
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med Service Fabric-plugin-programmet för Eclipse](service-fabric-get-started-eclipse.md)
* [Skapa ett Service Fabric-kluster i Azure-portalen](service-fabric-cluster-creation-via-portal.md)
* [Skapa ett Service Fabric-kluster med Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Förstå Service Fabric-programmodellen](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

