---
title: Felsöka Azure Bastion | Microsoft-dokument
description: I den här artikeln får du lära dig hur du felsöker Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 749d7125c013f419197ef8243d2475e612dc81b5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619172"
---
# <a name="troubleshoot-azure-bastion"></a>Felsöka Azure Bastion

Den här artikeln visar hur du felsöker Azure Bastion.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Det går inte att skapa en NSG på AzureBastionSubnet

**F:** När jag försöker skapa en NSG på undernätet För Azure Bastion får jag följande felmeddelande: *"Nätverkssäkerhetsgruppen <NSG name> har inga nödvändiga regler för Azure Bastion Subnet AzureBastionSubnet".*

**A.** Om du skapar och tillämpar en NSG på *AzureBastionSubnet*kontrollerar du att du har lagt till följande regler i NSG. Om du inte lägger till dessa regler misslyckas NSG-skapandet/uppdateringen.

1. Kontrollplansanslutning – Inkommande på 443 från GatewayManager
2. Diagnostikloggning och andra – Utgående på 443 till AzureCloud (Regionala taggar i det här tjänstmärket stöds inte ännu.)
3. Mål-VM – utgående för 3389 och 22 till VirtualNetwork

Ett exempel på NSG-reglerna finns att referera till i [snabbstartsmallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion).
Mer information finns i [NSG-vägledning för Azure Bastion](bastion-nsg.md).

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Det går inte att använda min SSH-nyckel med Azure Bastion

**F:** När jag försöker bläddra i min SSH-nyckelfil får jag följande felmeddelande: *"SSH Private key must start with -----BEGIN RSA PRIVATE KEY----- and ends with -----END RSA PRIVATE KEY-----"*.

**A.** Azure Bastion stöder endast RSA SSH-nycklar, vid denna tidpunkt. Se till att du bläddrar i en nyckelfil som är RSA-privat nyckel för SSH, med offentlig nyckel etablerad på måldatorn. 

Som ett exempel kan du använda följande kommando för att skapa en ny RSA SSH-nyckel:

**ssh-keygen -t rsa -b 4096 -C "email@domain.com"**

Resultat:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Det går inte att logga in på min virtuella dator med Windows-domän

**F:** Jag kan inte ansluta till min virtuella Windows-dator som är domänansluten.

**A.** Azure Bastion stöder domänansluten VM-inloggning för endast användarnamnslösenordsbaserad domän inloggning. När du anger domänautentiseringsuppgifterna i Azure-portalen använder du UPN-formatet (username@domaini stället för *domän\användarnamn* för att logga in. Detta stöds för domänanslutna eller hybridans anslutna (både domänanslutna och Azure AD-anslutna) virtuella datorer. Det stöds inte för virtuella Azure AD-anslutna datorer.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Problem med filöverföring

**F:** Stöds filöverföring med Azure Bastion?

**A.** Filöverföring stöds inte just nu. Vi arbetar på att lägga till stöd.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Svart skärm i Azure-portalen

**F:** När jag försöker ansluta med Azure Bastion får jag en svart skärm i Azure-portalen.

**A.** Detta händer när det antingen finns ett problem med nätverksanslutningen mellan din webbläsare och Azure Bastion (din klient-Internet-brandvägg kan blockera WebSockets-trafik eller liknande) eller mellan Azure Bastion och din virtuella mål.This happens when there is either a network connectivity issue between your web browser and Azure Bastion (your client Internet firewall may be blocking WebSockets traffic or similar), or between the Azure Bastion and your target VM. De flesta fall inkluderar en NSG som tillämpas antingen på AzureBastionSubnet eller på ditt virtuella mål-VM-undernät som blockerar RDP/SSH-trafiken i det virtuella nätverket. Tillåt WebSockets-trafik på klientens internetbrandvägg och kontrollera NSG:erna i mål-VM-undernätet.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Vanliga frågor om Bastion](bastion-faq.md).