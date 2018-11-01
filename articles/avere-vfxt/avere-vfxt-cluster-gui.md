---
title: Komma åt Avere vFXT Kontrollpanelen - Azure
description: Hur du ansluter till klustret vFXT och webbläsarbaserade Avere Kontrollpanelen för att konfigurera Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: c48f0d8f7ad34db585f4deae566641b6453357e8
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634539"
---
# <a name="access-the-vfxt-cluster"></a>Åtkomst till vFXT klustret

Ändra inställningarna och övervaka Avere vFXT klustret kan du använda Kontrollpanelen i Avere. Kontrollpanelen för Avere är en webbläsarbaserad grafiskt gränssnitt i klustret.

Eftersom vFXT klustret är placerad i ett privat virtuellt nätverk, måste du skapa en SSH-tunnel eller använda en annan metod för att nå IP-adress för hantering av klustrets. Det finns två grundläggande steg: 

1. Skapa en anslutning mellan din arbetsstation och privat virtuellt nätverk 
1. Använd IP-adress för hantering av klustrets att läsa in Kontrollpanelen i en webbläsare 

> [!NOTE] 
> Den här artikeln förutsätter att du har angett en offentlig IP-adress på kontrollanten klustret eller på en annan virtuell dator i ditt kluster virtuellt nätverk. Om du använder en VPN eller ExpressRoute för åtkomst till virtuella nätverk, gå vidare till [Anslut till Kontrollpanelen Avere](#connect-to-the-avere-control-panel-in-a-browser).

Innan du ansluter ska du kontrollera att SSH offentliga/privata nyckelparet som du använde när du skapar klustret controller är installerad på den lokala datorn. Läs dokumentationen för SSH-nycklar för [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) eller för [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) om du behöver hjälp.  

## <a name="access-with-a-linux-host"></a>Åtkomst med en Linux-värd

med det här formuläret: 

SSH -L *local_port*:*cluster_mgmt_ip*: 443 *controller_username*@*controller_public_IP* 

Det här kommandot ansluter till klustrets hantering av IP-adress via kontrollanten kluster IP-adress.

Exempel:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Autentisering sker automatiskt om du har använt din offentliga SSH-nyckel för att skapa klustret och motsvarande nyckel är installerad på klientdatorn.


## <a name="access-with-a-windows-host"></a>Åtkomst med en Windows-värd

Om du använder PuTTY, fyller du i den **värdnamn** med controller klusternamn och IP-adress: *your_username*@*controller_public_IP*.

Exempel: ``azureuser@203.0.113.51``

I den **Configuration** panelen:

1. Expandera **anslutning** > **SSH** till vänster. 
1. Klicka på **tunnlar**. 
1. Ange en källport som 8443. 
1. Ange vFXT klustrets IP-adress för hantering och port 443 för målet. 
   Exempel: ``203.0.113.51:443``
1. Klicka på **Lägg till**.
1. Klicka på **Open** (Öppna).

![Skärmbild av Putty-tillämpning som visar var du ska klicka för att lägga till en tunnel](media/avere-vfxt-ptty-numbered.png)

Autentisering sker automatiskt om du har använt din offentliga SSH-nyckel för att skapa klustret och motsvarande nyckel är installerad på klientdatorn.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Ansluta till Kontrollpanelen Avere i en webbläsare

Det här steget använder en webbläsare för att ansluta till konfigurationsverktyget körs i klustret vFXT.

Öppna webbläsaren och navigera till https://127.0.0.1:8443. 

Beroende på din webbläsare kan du behöva klicka på **Avancerat** och kontrollera att det är säkert att fortsätta till sidan.

Ange användarnamnet `admin` och det lösenord du angav när du skapar klustret.

![Skärmbild av Avere inloggningssidan fylls i med användarnamnet ”admin” och ett lösenord](media/avere-vfxt-gui-login.png)

Klicka på **inloggning** eller tryck på RETUR på tangentbordet.

## <a name="next-steps"></a>Nästa steg

Nu när du har åtkomst till klustret, aktivera [stöder](avere-vfxt-enable-support.md).
