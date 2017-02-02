---
title: "Konfigurera en utvecklingsmiljö i Mac OS X | Microsoft Docs"
description: "Installera runtime, SDK och verktyg och skapa ett lokalt utvecklingskluster. När du har slutfört den här installationen är du redo att börja bygga program i Mac OS X."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: d5a04e34a3f46097b3ad1b04ac6b2b845fc946af
ms.openlocfilehash: beb869fd5c1fcc19101b16149244187c37a6d6a3


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

## <a name="create-the-local-vm"></a>Skapa den lokala virtuella datorn
För att skapa den lokala virtuella datorn med ett 5-nods Service Fabric-kluster utför du följande steg:

1. Klona **Vagrantfile**-lagringsplatsen
   
    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
2. Navigera till den lokala repoklonen

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (Valfritt) Ändra standardinställningarna för virtuell dator

    Som standard konfigureras den lokala virtuella datorn på följande sätt:

   * 3 GB minne allokeras
   * Det privata värdnätverket konfigureras på IP 192.168.50.50 för att möjliggöra genomströmning av trafik från Mac-värden

     Du kan ändra dessa inställningar eller lägga till en annan konfiguration i den virtuella datorn i Vagrantfile. En fullständig lista över konfigurationsalternativen finns i [Vagrant-dokumentationen](http://www.vagrantup.com/docs).
4. Skapa den virtuella datorn

    ```bash
    vagrant up
    ```
  
   I det här steget laddas den förkonfigurerade VM-avbildningen ned. Avbildningen startas lokalt och ett lokalt Service Fabric-kluster konfigureras sedan på datorn. Det här kan ta några minuter. Om installationen lyckas, får du upp ett meddelande som indikerar att klustret startas.
   
    ![Klusterinstallationen startar efter att den virtuella datorn har etablerats][cluster-setup-script]
    
5. Testa att klustret är korrekt installerat genom att gå till Service Fabric Explorer på http://192.168.50.50:19080/Explorer (förutsatt att du har behållit standard-IP för det privata nätverket).

    ![Service Fabric Explorer på Mac-värddatorn][sfx-mac]

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-optional"></a>Installera Service Fabric-plugin-programmet för Eclipse Neon (valfritt)
Service Fabric innehåller ett plugin-program för Eclipse Neon IDE som förenklar processen att skapa och distribuera Java-tjänster.

1. Kontrollera att du har Buildship version 1.0.17 eller senare installerat i Eclipse. Du kan kontrollera vilka versioner de installerade komponenterna har genom att välja **Help > Installation Details** (Hjälp > Installationsinformation). Du kan uppdatera Buildship genom att följa instruktionerna [här][buildship-update].
2. Om du vill installera Service Fabric-plugin-programmet väljer du **Help > Install New Software...** (Hjälp > Installera ny programvara...).
3. Ange följande i textrutan Work with (Arbeta med): http://dl.windowsazure.com/eclipse/servicefabric.
4. Klicka på Add (Lägg till).

    ![Eclipse Neon-plugin-programmet för Service Fabric][sf-eclipse-plugin-install]
5. Välj Service Fabric-plugin-programmet och klicka på Next (Nästa).
6. Fortsätt med installationen och acceptera licensvillkoren.

## <a name="next-steps"></a>Nästa steg
<!-- Links -->

* [Skapa ditt första Service Fabric-program för Linux](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa ett Service Fabric-kluster i Azure-portalen](service-fabric-cluster-creation-via-portal.md)
* [Skapa ett Service Fabric-kluster med Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Förstå Service Fabric-programmodellen](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship



<!--HONumber=Jan17_HO1-->


