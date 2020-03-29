---
title: Få tillgång till kontrollpanelen Avere vFXT - Azure
description: Så här ansluter du till vFXT-klustret och den webbläsarbaserade Avere-kontrollpanelen för att konfigurera Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: fe2fc062f690498f3d1f588887279aa33d2434b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75416154"
---
# <a name="access-the-vfxt-cluster"></a>Komma åt vFXT-klustret

Om du vill justera klusterinställningarna och övervaka klustret använder du Kontrollpanelen i Avere. Avere Kontrollpanelen är ett webbläsarbaserat grafiskt gränssnitt till klustret.

Eftersom vFXT-klustret finns i ett privat virtuellt nätverk måste du skapa en SSH-tunnel eller använda en annan metod för att nå klustrets hanterings-IP-adress.

Det finns två grundläggande steg:

1. Skapa en anslutning mellan arbetsstationen och det privata virtuella nätverket
1. Läsa in klustrets kontrollpanel i en webbläsare

> [!NOTE]
> Den här artikeln förutsätter att du har angett en offentlig IP-adress på klusterstyrenheten eller på en annan virtuell dator i klustrets virtuella nätverk. I den här artikeln beskrivs hur du använder den virtuella datorn som värd för att komma åt klustret. Om du använder en VPN eller ExpressRoute för virtuell nätverksåtkomst hoppar du till [Anslut till Avere Kontrollpanelen](#connect-to-the-avere-control-panel-in-a-browser).

Innan du ansluter kontrollerar du att det offentliga/privata SSH-par som du använde när klusterstyrenheten skapade är installerat på den lokala datorn. Läs dokumentationen för SSH-nycklar för [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) eller [För Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) om du behöver hjälp. Om du har använt ett lösenord i stället för en offentlig nyckel uppmanas du att ange det när du ansluter.

## <a name="create-an-ssh-tunnel"></a>Skapa en SSH-tunnel

Du kan skapa en SSH-tunnel från kommandoraden i ett Linux-baserat eller Windows 10-klientsystem.

Använd ett SSH-tunnelkommando med det här formuläret:

ssh -L *local_port*: cluster_mgmt_ip:443 *controller_username*\@*controller_public_IP* *cluster_mgmt_ip*

Det här kommandot ansluter till klustrets hanterings-IP-adress via klusterstyrenhetens IP-adress.

Exempel:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Autentisering sker automatiskt om du använde den offentliga SSH-nyckeln för att skapa klustret och matchningsnyckeln är installerad på klientsystemet. Om du har använt ett lösenord uppmanas du att ange det.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Anslut till Avere-kontrollpanelen i en webbläsare

Det här steget använder en webbläsare för att ansluta till konfigurationsverktyget i vFXT-klustret.

* För en SSH-tunnelanslutning öppnar du `https://127.0.0.1:8443`webbläsaren och navigerar till .

  Du anslöt till klustrets IP-adress när du skapade tunneln, så du behöver bara använda localhost IP-adressen i webbläsaren. Om du har använt en annan lokal port än 8443 använder du portnumret i stället.

* Om du använder en VPN eller ExpressRoute för att nå klustret navigerar du till IP-adressen för klusterhantering i webbläsaren. Exempel: ``https://203.0.113.51``

Beroende på din webbläsare kan du behöva klicka på **Avancerat** och kontrollera att det är säkert att gå vidare till sidan.

Ange användarnamnet `admin` och det administrativa lösenord som du angav när du skapade klustret.

![Skärmbild av Avere-inloggningssidan ifylld med användarnamnet "admin" och ett lösenord](media/avere-vfxt-gui-login.png)

Klicka på **Logga in** eller tryck enter på tangentbordet.

## <a name="next-steps"></a>Nästa steg

När du har loggat in på klustrets kontrollpanel aktiverar du [stöd](avere-vfxt-enable-support.md).
