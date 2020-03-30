---
title: Azure VMware-lösning från CloudSimple – Konfigurera DNS för CloudSimple Private Cloud
description: Beskriver hur du ställer in DNS-namnmatchning för åtkomst till vCenter-server i ett CloudSimple Private Cloud från lokala arbetsstationer
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2d69d21eb46d502a45c9df1dfaaa947d26ef7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246115"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>Konfigurera DNS för namnmatchning för privat moln vCenter-åtkomst från lokala arbetsstationer

Om du vill komma åt vCenter-servern i ett CloudSimple Private Cloud från lokala arbetsstationer måste du konfigurera DNS-adressmatchning så att vCenter-servern kan åtgärdas med värdnamn och IP-adress.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Skaffa IP-adressen för DNS-servern för ditt privata moln

1. Logga in på [CloudSimple-portalen](access-cloudsimple-portal.md).

2. Navigera till **Resurser** > **privata moln** och välj det privata moln som du vill ansluta till.

3. På **sidan Sammanfattning** i det privata molnet under **Grundläggande information**kopierar du IP-adressen för den privata dns-servern i molnet.

    ![Privata DNS-servrar i molnet](media/private-cloud-dns-server.png)


Använd något av dessa alternativ för DNS-konfigurationen.

* [Skapa en zon på DNS-servern för *.cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Skapa en villkorlig vidarebefordrare på den lokala DNS-servern för att lösa *.cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Skapa en zon på DNS-servern för *.cloudsimple.io

Du kan ställa in en zon som en stub-zon och peka på DNS-servrarna i det privata molnet för namnmatchning. Det här avsnittet innehåller information om hur du använder en BIND DNS-server eller en Microsoft Windows DNS-server.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Skapa en zon på en BIND DNS-server

Den specifika filen och parametrarna som ska konfigureras kan variera beroende på din individuella DNS-inställning.

För standardkonfigurationen för BIND-servern redigerar du till exempel filen /etc/named.conf på DNS-servern och lägger till följande zoninformation.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Skapa en zon på en Microsoft Windows DNS-server

1. Högerklicka på DNS-servern och välj **Ny zon**. 
  
    ![Ny zon](media/DNS01.png)
2. Välj **Stub-zon** och klicka på **Nästa**.

    ![Ny zon](media/DNS02.png)
3. Välj lämpligt alternativ beroende på din miljö och klicka på **Nästa**.

    ![Ny zon](media/DNS03.png)
4. Välj **Sökzon framåt** och klicka på **Nästa**.

    ![Ny zon](media/DNS01.png)
5. Ange zonnamnet och klicka på **Nästa**.

    ![Ny zon](media/DNS05.png)
6. Ange IP-adresserna för DNS-servrarna för ditt privata moln som du har fått från CloudSimple-portalen.

    ![Ny zon](media/DNS06.png)
7. Klicka på **Nästa** efter behov för att slutföra guideinställningarna.

## <a name="create-a-conditional-forwarder"></a>Skapa en villkorlig vidarebefordrare

En villkorlig vidarebefordrare vidarebefordrar alla DNS-namnmatchningsbegäranden till den angivna servern. Med den här inställningen vidarebefordras alla begäranden till *.cloudsimple.io till DNS-servrarna som finns i det privata molnet. Följande exempel visar hur du konfigurerar vidarebefordrare på olika typer av DNS-servrar.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Skapa en villkorsriktare på en BIND DNS-server

Den specifika filen och parametrarna som ska konfigureras kan variera beroende på din individuella DNS-inställning.

För standardkonfigurationen för BIND-servern redigerar du till exempel filen /etc/named.conf på DNS-servern och lägger till följande information om villkorsstyrd vidarebefordring.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Skapa en villkorsriktare på en Microsoft Windows DNS-server

1. Öppna DNS-hanteraren på DNS-servern.
2. Högerklicka på **Villkorliga vidarebefordrare** och välj alternativet om du vill lägga till en ny villkorlig vidarebefordrare.

    ![Villkorlig vidarebefordrare 1 Windows DNS](media/DNS08.png)
3. Ange DNS-domänen och IP-adressen för DNS-servrarna i det privata molnet och klicka på **OK**.
