---
title: Konfigurera standarddomänen NFSv4.1 för Azure NetApp-filer | Microsoft-dokument
description: Beskriver hur du konfigurerar NFS-klienten för att använda NFSv4.1 med Azure NetApp Files.
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 77178a23206eadae941794c92b8dd99fe2ca1e05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906291"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Konfigurera NFSv4.1-standarddomän för Azure NetApp Files

NFSv4 introducerar konceptet med en autentiseringsdomän. Azure NetApp-filer stöder för närvarande root-only-användarmappning från tjänsten till NFS-klienten. Om du vill använda NFSv4.1-funktionen med Azure NetApp-filer måste du uppdatera NFS-klienten.

## <a name="default-behavior-of-usergroup-mapping"></a>Standardbeteende för mappning av användare/grupper

Rotmappning är `nobody` användaren som standard eftersom NFSv4-domänen är inställd på `localdomain`. När du monterar en Azure NetApp Files NFSv4.1-volym som rot visas filbehörigheter på följande sätt:  

![Standardbeteende för mappning av användare/grupper för NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Som exemplet ovan visar ska `file1` användaren `root`för vara `nobody` , men den mappar till som standard.  Den här artikeln visar `file1` hur `root`du ställer in användaren på .  

## <a name="steps"></a>Steg 

1. Redigera `/etc/idmapd.conf` filen på NFS-klienten.   
    Avkommenta `#Domain` raden (det vill `#` exempel ta bort från `localdomain` `defaultv4iddomain.com`raden) och ändra värdet till . 

    Inledande konfiguration: 
    
    ![Inledande konfiguration för NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Uppdaterad konfiguration:
    
    ![Uppdaterad konfiguration för NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Avmontera alla NFS-volymer som för närvarande är monterade.
3. Uppdatera `/etc/idmapd.conf` filen.
4. Starta `rpcbind` om tjänsten på`service rpcbind restart`värden ( ), eller helt enkelt starta om värden.
5. Montera NFS-volymerna efter behov.   

    Se [Montera eller avmontera en volym för virtuella Windows- eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

I följande exempel visas den resulterande ändringen av användare/grupp: 

![Resulterande konfiguration för NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Som exemplet visar har användaren/gruppen nu `nobody` `root`ändrats från till .

## <a name="behavior-of-other-non-root-users-and-groups"></a>Beteende hos andra (icke-rot) användare och grupper

Azure NetApp Files stöder lokala användare (användare som skapats lokalt på en värd) som har behörigheter associerade med filer eller mappar i NFSv4.1-volymer. Tjänsten stöder dock för närvarande inte mappning av användare/grupper över flera noder. Användare som skapats på en värd mappas därför inte som standard till användare som skapats på en annan värd. 

I följande exempel `Host1` har tre befintliga`testuser01`testanvändarkonton ( , `testuser02`, `testuser03`): 

![Resulterande konfiguration för NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

Observera `Host2`att testanvändarkontona inte har skapats, men samma volym är monterad på båda värdarna:

![Resulterande konfiguration för NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Nästa steg 

[Montera eller demontera en volym för virtuella Windows- eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

