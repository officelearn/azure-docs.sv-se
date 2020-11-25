---
title: Nätverks segment för VMware HCX
description: Det krävs fyra nätverk för VMware-HCX.
ms.topic: include
ms.date: 11/23/2020
ms.openlocfilehash: 48894c532c97b70cde1473fb8b81f406ded70343
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999644"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Fyra nätverk behövs för VMware-HCX:

- **Hanterings nätverk:** Vanligt vis är det samma hanterings nätverk som används i vSphere-klustret. Identifiera minst två IP-adresser på det här nätverks segmentet för VMware HCX. Du kan behöva större siffror, beroende på din distribution.

   > [!NOTE]
   > Metoden vi rekommenderar är att skapa ett/26-nätverk. I ett/26-nätverk kan du använda upp till 10 service nät och 60 nätverks Extender (-1 per service nät). Du kan sträcka ut åtta nätverk per nätverks Extender med hjälp av privata moln i Azure VMware-lösningen.
   >
   
- **vMotion nätverk:** Normalt är det samma nätverk som används för vMotion på vSphere-klustret.  Identifiera minst två IP-adresser på det här nätverks segmentet för VMware HCX. Du kan behöva större siffror, beroende på din distribution.  

   VMotion-nätverket måste exponeras på en distribuerad virtuell växel eller vSwitch0. Om det inte är det ändrar du miljön.

   > [!NOTE]
   > Det här nätverket kan vara privat (inte dirigerat).

- **Överordnat nätverk:** Du vill skapa ett nytt nätverk för VMware HCX-överordnad länk och utöka det till ditt vSphere-kluster via en port grupp. Identifiera minst två IP-adresser på det här nätverks segmentet för VMware HCX. Du kan behöva större siffror, beroende på din distribution.  

   > [!NOTE]
   > Metoden vi rekommenderar är att skapa ett/26-nätverk. I ett/26-nätverk kan du använda upp till 10 service nät och 60 nätverks Extender (-1 per service nät). Du kan sträcka ut åtta nätverk per nätverks Extender med hjälp av privata moln i Azure VMware-lösningen.
   >
   
- **Nätverk för replikering:** Detta är valfritt. Du vill skapa ett nytt nätverk för VMware HCX-replikering och utöka det nätverket till ditt vSphere-kluster via en port grupp. Identifiera minst två IP-adresser på det här nätverks segmentet för VMware HCX. Du kan behöva större siffror, beroende på din distribution.

   > [!NOTE]
   > Den här konfigurationen är bara möjlig när lokala kluster värdar använder ett dedikerat VMkernel nätverk.  Om ditt lokala kluster inte har ett dedikerat VMkernel nätverk definierat, behöver du inte skapa det här nätverket.
