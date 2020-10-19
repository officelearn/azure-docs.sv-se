---
title: Nätverks segment för VMware HCX
description: Det krävs fyra nätverk för VMware-HCX.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 8137b4383d2a243d53db317db6f5a78b3bc68e67
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173622"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Fyra nätverk behövs för VMware-HCX:

- **Hanterings nätverk:** Vanligt vis är det samma hanterings nätverk som används i vSphere-klustret. Identifiera minst två IP-adresser på det här nätverks segmentet för VMware HCX. (Du kan behöva större siffror, beroende på din distribution.)

- **vMotion nätverk:** Normalt är det samma nätverk som används för vMotion på vSphere-klustret.  Identifiera minst två IP-adresser på det här nätverks segmentet för VMware HCX. (Du kan behöva större siffror, beroende på din distribution.)  

   VMotion-nätverket måste exponeras på en distribuerad virtuell växel eller vSwitch0. Om det inte är det ändrar du miljön.

   > [!NOTE]
   > Om det här nätverket inte dirigeras (privat) är det OK.

- **Överordnat nätverk:** Du vill skapa ett nytt nätverk för VMware HCX-överordnad länk och utöka det till ditt vSphere-kluster via en port grupp. Identifiera minst två IP-adresser på det här nätverks segmentet för VMware HCX. (Du kan behöva större siffror, beroende på din distribution.)  

   > [!NOTE]
   > Den rekommenderade metoden är att skapa ett/29-nätverk, men nätverks storleken kommer att göras.

- **Nätverk för replikering:** Du vill skapa ett nytt nätverk för VMware HCX-replikering och utöka det nätverket till ditt vSphere-kluster via en port grupp. Identifiera minst två IP-adresser på det här nätverks segmentet för VMware HCX. (Du kan behöva större siffror, beroende på din distribution.)

   > [!NOTE]
   > Den rekommenderade metoden är att skapa ett/29-nätverk, men nätverks storleken kommer att göras.