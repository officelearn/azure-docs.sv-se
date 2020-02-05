---
title: Eskalera behörigheter för molnets privata moln – Azure VMware-lösning enligt AVS
description: Beskriver hur du eskalerar behörigheter för ditt moln privata moln för administrativa funktioner i vCenter
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 211960af359e19f93afef58162c5b09ae1d9b23f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025324"
---
# <a name="escalate-avs-private-cloud-vcenter-privileges-from-the-avs-portal"></a>Eskalera behörigheter för molnets privata moln vCenter från AVS-portalen

För administrativ åtkomst till ditt molns privata moln vCenter kan du tillfälligt eskalera dina AVS-behörigheter. Med utökade privilegier kan du installera VMware-lösningar, lägga till identitets källor och hantera användare.

Nya användare kan skapas i vCenter SSO-domänen och tilldelas åtkomst till vCenter. När du skapar nya användare kan du lägga till dem i de AVS-fördefinierade grupperna för åtkomst till vCenter. Mer information finns i [behörighets modellen för molnets privata moln i VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Gör inga konfigurations ändringar för hanterings komponenter. Åtgärder som vidtas under det eskalerade privilegierade läget kan påverka systemet negativt eller kan orsaka att systemet blir otillgängligt.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Eskalera privilegier

1. Få åtkomst till [AVS-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **resurser** , Välj det AVS-privata moln som du vill eskalera privilegier för.

3. Klicka på **eskalera**längst ned på sidan Sammanfattning under **ändra vSphere-privilegier**.

    ![Ändra vSphere-behörighet](media/escalate-private-cloud-privilege.png)

4. Välj användar typen vSphere. Endast `CloudOwner@cloudsimple.local` lokal användare kan eskaleras.

5. Välj intervallet för eskalerad tid i list rutan. Välj den kortaste perioden som gör att du kan slutföra uppgiften.

6. Markera kryss rutan för att bekräfta att du förstår riskerna.

    ![Dialog rutan eskalera privilegier](media/escalate-private-cloud-privilege-dialog.png)

7. Klicka på **OK**.

8. Det kan ta några minuter att eskalera processen. När du är klar klickar du på **OK**.

Behörighets eskaleringen börjar och varar tills det valda intervallet har slutförts. Du kan logga in på ditt molns privata moln vCenter för att utföra administrativa uppgifter.

> [!IMPORTANT]
> Endast en användare kan ha eskalerade privilegier. Du måste ta bort användarens behörigheter innan du kan eskalera en annan användares privilegier.

> [!CAUTION]
> Nya användare får endast läggas till i *moln-ägar-grupp*, *Cloud-global-Cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin-Group* eller, *Cloud-global-VM-admin-Group*.  Användare som lagts till i gruppen *Administratörer* tas då bort automatiskt.  Endast tjänst konton måste läggas till i *Administratörs* gruppen och tjänst kontona får inte användas för att logga in på vSphere-webbgränssnittet.

## <a name="extend-privilege-escalation"></a>Utöka eskalering av privilegier

Om du behöver ytterligare tid för att slutföra dina uppgifter kan du utöka behörighets eskalerings perioden. Välj det ytterligare eskalerade tidsintervall som gör att du kan slutföra de administrativa uppgifterna.

1. På **resurserna** > **AVS-privata moln** i AVS-portalen väljer du det AVS-privata moln som du vill utöka behörighets eskaleringen för.

2. Nästan längst ned på fliken Sammanfattning klickar du på **utöka eskalering av privilegier**.

    ![Utöka eskalering av privilegier](media/de-escalate-private-cloud-privilege.png)

3. Välj ett intervall för eskalering i list rutan. Granska den nya slut tiden för eskalering.

4. Klicka på **Spara** för att utöka intervallet.

## <a name="de-escalate-privileges"></a>Ta bort behörigheter för att eskalera

När dina administrativa uppgifter har slutförts bör du eskalera dina privilegier. 

1. På **resurserna** > **moln privata moln** i AVS-portalen väljer du det AVS-privata moln som du vill ta bort behörigheter för.

2. Klicka på **eskalera**.

3. Klicka på **OK**.

> [!IMPORTANT]
> Undvik eventuella fel genom att logga ut från vCenter och logga in igen efter att de eskalerats.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera vCenter-identitets källor som ska använda Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Installera säkerhets kopierings lösning för [säkerhets kopiering av virtuella datorer i arbets belastning](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)