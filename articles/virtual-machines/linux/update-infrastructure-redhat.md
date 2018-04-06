---
title: Infrastrukturen för Red Hat | Microsoft Docs
description: Lär dig mer om infrastrukturen för Red Hat för på begäran Red Hat Enterprise Linux-instanser i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: timlt
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: borisb
ms.openlocfilehash: 336990373027ae13688f41e453b9a262f120c6e0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update infrastruktur för att på begäran Red Hat Enterprise Linux virtuella datorer i Azure
 [Infrastrukturen för Red Hat](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) gör att molntjänstleverantörer, till exempel Azure att spegla Red Hat-värdbaserad databasinnehåll, skapa anpassade databaser med Azure-specifika innehåll och gör den tillgänglig för slutanvändaren virtuella datorer.

Red Hat Enterprise Linux (RHEL) betala per användning (PAYG) bilder levereras förinställda åtkomst till Azure RHUI. Ingen ytterligare konfiguration krävs. För att få de senaste uppdateringarna, köra `sudo yum update` när RHEL-instans är klar. Den här tjänsten ingår som en del av RHEL PAYG programvara avgifter.

## <a name="important-information-about-azure-rhui"></a>Viktig information om Azure RHUI
* Azure RHUI stöder för närvarande endast den senaste minor-versionen i varje RHEL familj (RHEL6 eller RHEL7). Om du vill uppgradera en RHEL VM-instans som är anslutna till RHUI till den senaste versionen av mindre kör `sudo yum update`.

    Om du vill etablera en virtuell dator från en avbildning RHEL 7.2 PAYG och kör till exempel `sudo yum update`, du får en RHEL 7.4 virtuell dator (senaste lägre version i familjen RHEL7).

    Om du vill undvika det här problemet måste du skapa en egen avbildning som beskrivs i den [skapa och ladda upp en Red Hat-baserad virtuell dator för Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artikel. Sedan måste du ansluta till en annan uppdatering infrastruktur ([innehåll direkt leverans servrar till Red Hat](https://access.redhat.com/solutions/253273) eller en [Red Hat satellit server](https://access.redhat.com/products/red-hat-satellite)).

* Åtkomst till Azure-baserad RHUI ingår i RHEL PAYG avbildningen priset. Om du avregistrerar en virtuell dator RHEL PAYG från Azure-baserad RHUI konverterar som inte den virtuella datorn till en bring-your-äger-licens (BYOL) typ för den virtuella datorn. Om du registrerar av samma virtuella dator med en annan källa för uppdateringar, du kan innebära _indirekt_ dubbla avgifter. Du är debiteras första gången för Azure RHEL programvara avgift. Du är debiteras en gång för Red Hat-prenumerationer som köpts tidigare. Om du behöver konsekvent att använda en annan infrastruktur än Azure-baserad RHUI kan du skapa och distribuera dina egna avbildningar (BYOL-typ). Den här processen beskrivs i [skapa och ladda upp en Red Hat-baserad virtuell dator för Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Två klasser av RHEL PAYG avbildningar i Azure (RHEL för SAP HANA) och RHEL för SAP Business-program är anslutna till dedikerade RHUI kanaler som finns kvar på RHEL specifika lägre version som krävs för SAP-certifiering. 

* Åtkomst till Azure-baserad RHUI är begränsad till de virtuella datorerna inom de [IP-adressintervall för Azure-datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Om du är proxyanslutning alla VM-trafik via ett lokalt nätverksinfrastruktur, du kan behöva konfigurera användardefinierade vägar för de virtuella datorerna PAYG RHEL åtkomst till Azure-RHUI.

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>IP-adresser för servrar för RHUI leverans av innehåll

RHUI är tillgänglig i alla regioner där RHEL på begäran bilder är tillgängliga. För närvarande innehåller alla offentliga områden som anges på den [Azure status instrumentpanelen](https://azure.microsoft.com/status/) sida, Azure som tillhör amerikanska myndigheter och Tyskland för Microsoft Azure-regioner. 

Om du använder en nätverkskonfiguration för att begränsa åtkomsten ytterligare från RHEL PAYG virtuella datorer kontrollerar du att följande IP-adresser tillåts för `yum update` ska fungera beroende på miljön som du använder: 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="rhui-azure-infrastructure-update"></a>Uppdatering av RHUI Azure-infrastrukturen

I September 2016 distribuerade vi en uppdaterad RHUI i Azure. I April 2017 Stäng vi gamla Azure RHUI. Om du har använt RHEL PAYG-bilder (eller deras ögonblicksbilder) från September 2016 eller senare måste ansluter du automatiskt till den nya Azure-RHUI. Om men du har äldre ögonblicksbilder på dina virtuella datorer, måste du manuellt uppdatera konfigurationen för att komma åt Azure-RHUI som beskrivs i följande avsnitt.

De nya Azure RHUI-servrarna distribueras med [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). I Traffic Manager en enda slutpunkt (rhui 1.microsoft.com) kan användas av en virtuell dator oavsett region. 

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Felsökning av anslutningsproblem med till Azure RHUI
Följ dessa steg om du får problem med att ansluta till Azure RHUI från din Azure RHEL PAYG VM:

1. Kontrollera konfigurationen för virtuell dator för Azure RHUI slutpunkten:

    a. Kontrollera om den `/etc/yum.repos.d/rh-cloud.repo` filen innehåller en referens till `rhui-[1-3].microsoft.com` i den `baseurl` av den `[rhui-microsoft-azure-rhel*]` avsnitt i filen. Om du använder den nya Azure-RHUI.

    b. Om den pekar på en plats med följande mönster `mirrorlist.*cds[1-4].cloudapp.net`, en konfiguration måste uppdateras. Du använder den gamla VM-ögonblicksbilden och du behöver uppdatera den att peka mot den nya Azure-RHUI.

2. Åtkomst till Azure-baserad RHUI begränsas till virtuella datorer [Azure-datacenter IP-adressintervall] (https://www.microsoft.com/download/details.aspx?id=41653).
 
3. Om du använder den nya konfigurationen har verifierat att den virtuella datorn ansluter från Azure IP-adressintervall och fortfarande inte kan ansluta till Azure RHUI, filen ett supportärende med Microsoft eller Red Hat.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Manuell uppdatering metod du använder Azure RHUI-servrar
Den här proceduren tillhandahålls endast för referens. RHEL PAYG bilder har redan rätt konfiguration för att ansluta till Azure RHUI. För att manuellt uppdatera konfigurationen för att använda Azure RHUI servrarna, gör du följande:

1. Hämta den offentliga nyckel signaturen via curl.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
   ```

2. Verifiera att den hämta nyckeln.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

3. Kontrollera utdata och sedan den `keyid` och `user ID packet`.

   ```bash
   Version: GnuPG v1.4.7 (GNU/Linux)
   :public key packet:
           version 4, algo 1, created 1446074508, expires 0
           pkey[0]: [2048 bits]
           pkey[1]: [17 bits]
           keyid: EB3E94ADBE1229CF
   :user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
   :signature packet: algo 1, keyid EB3E94ADBE1229CF
           version 4, created 1446074508, md5len 0, sigclass 0x13
           digest algo 2, begin of digest 1a 9b
           hashed subpkt 2 len 4 (sig created 2015-10-28)
           hashed subpkt 27 len 1 (key flags: 03)
           hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
           hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
           hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
           hashed subpkt 30 len 1 (features: 01)
           hashed subpkt 23 len 1 (key server preferences: 80)
           subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
           data: [2047 bits]
   ```

4. Installera den offentliga nyckeln.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

5. Hämta, kontrollera och installera en klient RPM Package Manager (RPM).
    
    >[!NOTE]
    >Paketversionerna ändra. Om du manuellt ansluta till Azure RHUI hittar du den senaste versionen av klientpaketet för varje RHEL familj av etablering senaste bilden från galleriet.
  
   a. Hämta. 
   
    - För RHEL 6:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
        ```
    
    - För RHEL 7:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
        ```

   b. Kontrollera.

   ```bash
   rpm -Kv azureclient.rpm
   ```

   c. Kontrollera utdata så att signaturen för paketet är OK.

   ```bash
   azureclient.rpm:
       Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
       Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
       V3 RSA/SHA256 Signature, key ID be1229cf: OK
       MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
   ```

   d. Installera RPM.

    ```bash
    sudo rpm -U azureclient.rpm
    ```

6. När du är klar kontrollerar du att du kan komma åt Azure RHUI från den virtuella datorn.

## <a name="next-steps"></a>Nästa steg
Skapa en Red Hat Enterprise Linux virtuell dator från en avbildning för Azure Marketplace PAYG och använda Azure-baserad RHUI, gå till den [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). 
