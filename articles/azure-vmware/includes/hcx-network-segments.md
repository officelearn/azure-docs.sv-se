---
title: Nätverks segment för VMware HCX
description: Det krävs fyra nätverk för VMware-HCX.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: f01aba13d688dfed573acfc8c4344511fbfd9fa8
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583399"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Det krävs fyra nätverk för VMware-HCX:

- **Hanterings nätverk:** Vanligt vis är det samma hanterings nätverk som används i vSphere-klustret.  Identifiera minst två IP-adresser på det här nätverks segmentet för VMware HCX (större nummer kan krävas beroende på din distribution).

- **vMotion nätverk:** Normalt är det samma nätverk som används för vMotion på vSphere-klustret.  Identifiera minst två IP-adresser på det här nätverks segmentet för VMware HCX (större nummer kan krävas beroende på din distribution).  

   VMotion-nätverket måste exponeras på en distribuerad virtuell växel eller vSwitch0. Om det inte är det ändrar du miljön.

   > [!NOTE]
   > Om det här nätverket inte dirigeras (privat), är det OK.

- **Överordnat nätverk:** Du vill skapa ett nytt nätverk för VMware HCX-överordnad länk och utöka det till ditt vSphere-kluster via en port grupp.  Identifiera minst två IP-adresser på det här nätverks segmentet för VMware HCX (större nummer kan krävas beroende på din distribution).  

   > [!NOTE]
   > Den rekommenderade metoden är att skapa ett/29-nätverk, men nätverks storleken kommer att göras.

- **Nätverk för replikering:** Du vill skapa ett nytt nätverk för VMware HCX-replikering och utöka det nätverket till ditt vSphere-kluster via en port grupp.  Identifiera minst två IP-adresser på det här nätverks segmentet för VMware HCX (större nummer kan krävas beroende på din distribution).

   > [!NOTE]
   > Den rekommenderade metoden är att skapa ett/29-nätverk, men nätverks storleken kommer att göras.