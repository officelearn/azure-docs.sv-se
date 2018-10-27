---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 8f7eae06947a40117f3952a0a5624c22df750b34
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166100"
---
* I **Add vCenter** anger du ett eget namn för vSphere-värden eller vCenter-servern och sedan serverns IP-adress eller fullständiga domännamn. Lämna porten som 443 om inte dina VMware-servrar är konfigurerade för att lyssna efter begäranden på en annan port. Välj det konto som ska anslutas till VMware vCenter- eller vSphere ESXi-servern. Klicka på **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Om du lägger till VMware vCenter-servern eller VMware vSphere-värden med ett konto som inte har administratörsbehörigheter för vCenter- eller värdservern ska du se till att kontot har dessa behörigheter aktiverade: datacenter, datalager, mapp, värd, nätverk, resurs, virtuell dator och vSphere Distributed Switch. Dessutom behöver VMware vCenter-servern behörigheter för lagringsvisning aktiverad.
