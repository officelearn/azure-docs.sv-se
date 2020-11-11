---
title: Konfigurera export princip för NFS-volym – Azure NetApp Files
description: Beskriver hur du konfigurerar export policy för att kontrol lera åtkomsten till en NFS-volym med hjälp av Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 77630ddcd61d17f3b47e6cb5d43396c1a6f0e904
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517877"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurera exportprincipen för en NFS-volym

Du kan konfigurera export policy för att kontrol lera åtkomsten till en Azure NetApp Files volym. Azure NetApp Files export policy stöder volymer som använder NFS-protokollet (både NFSv3 och NFSv 4.1) och det dubbla protokollet (NFSv3 och SMB). 

Du kan skapa upp till fem export princip regler.

## <a name="configure-the-policy"></a>Konfigurera principen 

1.  På sidan **volymer** väljer du den volym som du vill konfigurera export principen för och väljer sedan **Exportera princip**. Du kan också konfigurera export principen när volymen skapas.

2.  Ange följande information för att skapa en export policy regel:   
    * **Index** : Ange index numret för regeln.  
      
      En exportpolicy kan bestå av upp till fem regler. Reglerna utvärderas enligt ordningen i listan med indexnummer. Regler med lägre indexnummer utvärderas först. Till exempel utvärderas regeln med indexnummer 1 före regeln med indexnummer 2. 

    * **Tillåtna klienter** : Ange värdet i något av följande format:  
      * IPv4-adress. Exempel: `10.1.12.24`
      * IPv4-adress med en nätmask uttryckt som ett antal bitar. Exempel: `10.1.12.10/4`
      * Kommaavgränsade IP-adresser. Du kan ange flera värd-IP: er i samma regel genom att avgränsa dem med kommatecken. Exempel: `10.1.12.25,10.1.12.28,10.1.12.29`

    * **Åtkomst** : Välj någon av följande åtkomst typer:  
      * Ingen åtkomst 
      * Läs- och skriv
      * Skrivskydd

    * **Skrivskyddad** och **Läs/skriv** : om du använder Kerberos-kryptering med nfsv 4.1 följer du anvisningarna i [Konfigurera nfsv 4.1 Kerberos-kryptering](configure-kerberos-encryption.md).  För prestanda påverkan av Kerberos, se [prestanda påverkan av Kerberos på nfsv 4.1](configure-kerberos-encryption.md#kerberos_performance). 

      ![Kerberos-säkerhetsalternativ](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Rot åtkomst** : Ange om `root` kontot har åtkomst till volymen.  Som standard är rot åtkomsten inställd på **på** och `root` kontot har åtkomst till volymen.

      ![Exportpolicy](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

## <a name="next-steps"></a>Nästa steg 
* [Montera eller demontera en volym för virtuella datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Hantera ögonblicksbilder](azure-netapp-files-manage-snapshots.md)
