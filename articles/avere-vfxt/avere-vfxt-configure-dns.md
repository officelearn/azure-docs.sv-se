---
title: Avere vFXT DNS - Azure
description: Konfigurera en DNS-server för resursallokering-belastningsutjämning med Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 38f15acd16acca2edd558a36ba434a1b0ab045fb
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634598"
---
# <a name="avere-cluster-dns-configuration"></a>Avere kluster DNS-konfiguration

Det här avsnittet förklarar grunderna för att konfigurera en DNS-systemet för belastningsutjämning Avere vFXT klustret. 

Det här dokumentet *omfattar inte* instruktioner för att konfigurera och hantera en DNS-server i Azure-miljön. 

Istället för att använda resursallokering DNS för att belastningsutjämna ett vFXT-kluster i Azure, Överväg att använda manuella metoder för att tilldela IP-adresser jämnt mellan klienter när de är monterade. Flera metoder beskrivs i [montera Avere klustret](avere-vfxt-mount-clients.md). 

Ha detta i åtanke när du bestämmer huruvida du ska använda en DNS-server: 

* Om datorn används av NFS-klienter, med hjälp av DNS är inte obligatoriskt – det är möjligt att ange alla adresser i nätverket med hjälp av numeriska IP-adresser. 

* Om systemet har stöd för SMB (CIFS) åtkomst, krävs DNS, eftersom du måste ange en DNS-domän för Active Directory-servern.

* DNS krävs om du vill använda Kerberos-autentisering.

## <a name="load-balancing"></a>Belastningsutjämning

Konfigurera din DNS-domän för att använda belastningsutjämning med resursallokering för klientinriktade IP-adresser för att distribuera den allmänna belastningen.

## <a name="configuration-details"></a>Konfigurationsinformation

När klienter har åtkomst till klustret, balanserar RRDNS automatiskt sina begäranden mellan alla tillgängliga gränssnitt.

Konfigurera din DNS-server för att hantera klientinriktade kluster-adresser som du ser i följande diagram för optimala prestanda.

En kluster-vserver visas till vänster och IP-adresser visas i mitten och till höger. Konfigurera varje klientåtkomstpunkt med A-poster och pekare som på bilden.

![Avere kluster resursallokering DNS-diagram](media/avere-vfxt-rrdns-diagram.png) 
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Varje klientinriktade IP-adress måste ha ett unikt namn för intern användning av klustret. (I det här diagrammet klientens IP-adresser är namngivna vs1-klient - IP-* för tydlighetens skull, men i produktion bör du välja något kortare, som klienten *.)

Klienter montera klustret med namnet vserver som argumentet server. 

Ändra din DNS-server ``named.conf`` fil att ange cykliskt för frågor till dina vserver. Det här alternativet innebär att alla tillgängliga värden gås igenom. Lägg till en instruktion som liknar följande:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Följande kommandon för nsupdate ge ett exempel på hur du konfigurerar DNS korrekt:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>Klustret DNS-inställningar

Ange DNS-server som vFXT klustret använder i den **kluster** > **administrativa nätverket** inställningssidan. Inställningarna på sidan innefattar:

* DNS-serveradress
* DNS-domännamn
* DNS-sökdomäner

Läs [DNS-inställningarna](<http://library.averesystems.com/ops_guide/4_7/gui_admin_network.html#gui-dns>) i Avere kluster-konfigurationsguide för mer information om hur du använder den här sidan.


