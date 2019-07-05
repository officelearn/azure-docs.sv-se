---
title: Komma åt Avere vFXT Kontrollpanelen - Azure
description: Hur du ansluter till klustret vFXT och webbläsarbaserade Avere Kontrollpanelen för att konfigurera Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: v-erkell
ms.openlocfilehash: 830be92d37f304598cca05c3ac80973158c38a59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439977"
---
# <a name="access-the-vfxt-cluster"></a>Åtkomst till vFXT klustret

Ändra inställningarna och övervaka Avere vFXT klustret kan du använda Kontrollpanelen i Avere. Kontrollpanelen för Avere är en webbläsarbaserad grafiskt gränssnitt i klustret.

Eftersom vFXT klustret är placerad i ett privat virtuellt nätverk, måste du skapa en SSH-tunnel eller använda en annan metod för att nå IP-adress för hantering av klustrets. Det finns två grundläggande steg: 

1. Skapa en anslutning mellan din arbetsstation och privat virtuellt nätverk 
1. Läsa in klustrets Kontrollpanelen i en webbläsare 

> [!NOTE] 
> Den här artikeln förutsätter att du har angett en offentlig IP-adress på kontrollanten klustret eller på en annan virtuell dator i ditt kluster virtuellt nätverk. Den här artikeln beskriver hur du använder den virtuella datorn som värd för åtkomst till klustret. Om du använder en VPN eller ExpressRoute för åtkomst till virtuella nätverk, gå vidare till [Anslut till Kontrollpanelen Avere](#connect-to-the-avere-control-panel-in-a-browser).

Innan du ansluter ska du kontrollera att SSH offentliga/privata nyckelparet som du använde när du skapar klustret controller är installerad på den lokala datorn. Läs dokumentationen för SSH-nycklar för [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) eller för [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) om du behöver hjälp. (Om du har använt ett lösenord i stället för en offentlig nyckel, uppmanas du att ange den när du ansluter.) 

## <a name="create-an-ssh-tunnel"></a>Skapa en SSH-tunnel 

Du kan skapa en SSH-tunnel från kommandoraden för en Linux-baserade eller Windows 10 klientsystemet. 

Använd en SSH tunneling kommandot med det här formuläret: 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

Det här kommandot ansluter till klustrets hantering av IP-adress via kontrollanten kluster IP-adress.

Exempel:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Autentisering sker automatiskt om du har använt din offentliga SSH-nyckel för att skapa klustret och motsvarande nyckel är installerad på klientdatorn. Om du har använt ett lösenord uppmanas du att ange den.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Ansluta till Kontrollpanelen Avere i en webbläsare

Det här steget använder en webbläsare för att ansluta till konfigurationsverktyget körs i klustret vFXT.

* För en SSH-tunnel-anslutning, öppna webbläsaren och navigera till `https://127.0.0.1:8443`. 

  Du ansluts till klustret IP-adress när du har skapat tunneln, så du behöver bara använda localhost-IP-adress i webbläsaren. Om du använder en annan lokal port än 8443, använder du din portnummer.

* Om du använder en VPN eller ExpressRoute för att nå klustret, navigerar du till klustrets hantering av IP-adress i webbläsaren. Exempel: ``https://203.0.113.51``

Beroende på din webbläsare kan du behöva klicka på **Avancerat** och kontrollera att det är säkert att fortsätta till sidan.

Ange användarnamnet `admin` och administrativa lösenordet du angav när du skapar klustret.

![Skärmbild av Avere inloggningssidan fylls i med användarnamnet ”admin” och ett lösenord](media/avere-vfxt-gui-login.png)

Klicka på **inloggning** eller tryck på RETUR på tangentbordet.

## <a name="next-steps"></a>Nästa steg

Nu när du har åtkomst till klustret, aktivera [stöder](avere-vfxt-enable-support.md).
