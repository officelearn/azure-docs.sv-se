---
title: Azure VMware Solution by CloudSimple – Konfigurera arbetsbelastning DNS och DHCP för privat moln
description: Beskriver hur du konfigurerar DNS och DHCP för program och arbetsbelastningar som körs i din CloudSimple Private Cloud-miljö
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ef1266b783034cf18dc2b3ea4be5ebc01bc12c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024695"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>Konfigurera DNS- och DHCP-program och arbetsbelastningar i ditt CloudSimple Private Cloud

Program och arbetsbelastningar som körs i en privat molnmiljö kräver namnmatchning och DHCP-tjänster för uppslag och IP-adresstilldelning.  En korrekt DHCP- och DNS-infrastruktur krävs för att tillhandahålla dessa tjänster.  Du kan konfigurera en virtuell dator för att tillhandahålla dessa tjänster i din privata molnmiljö.  

## <a name="prerequisites"></a>Krav

* En distribuerad portgrupp med VLAN konfigurerad
* Dirigera installation till lokala eller Internetbaserade DNS-servrar
* Mall för virtuell dator eller ISO för att skapa en virtuell dator

## <a name="linux-based-dns-server-setup"></a>Linux-baserad DNS-serverinstallation

Linux erbjuder olika paket för att konfigurera DNS-servrar.  Här är ett [exempel setup från DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) med instruktioner för att ställa in en öppen källkod BIND DNS-server.

## <a name="windows-based-setup"></a>Installation av Windows-baserad

I de här avsnitten i Microsoft beskrivs hur du konfigurerar en Windows-server som DNS-server och som DHCP-server.

* [Windows Server som DNS-server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server som DHCP-server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
