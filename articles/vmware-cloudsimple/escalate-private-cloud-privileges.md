---
title: Eskalera privata molnbehörigheter
titleSuffix: Azure VMware Solution by CloudSimple
description: Beskriver hur du eskalerar privilegier i ditt privata moln för administrativa funktioner i vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d06f3e8be449e7050c65c75339a0cff6efe19e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025324"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Eskalera privata moln vCenter-privilegier från CloudSimple-portalen

För administrativ åtkomst till ditt privata moln vCenter kan du tillfälligt eskalera dina CloudSimple-privilegier.  Med förhöjda privilegier kan du installera VMware-lösningar, lägga till identitetskällor och hantera användare.

Nya användare kan skapas på vCenter SSO-domänen och få åtkomst till vCenter.  När du skapar nya användare lägger du till dem i CloudSimple-inbyggda grupper för åtkomst till vCenter.  Mer information finns i [CloudSimple Private Cloud-behörighetsmodellen för VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Gör inga konfigurationsändringar för hanteringskomponenter. Åtgärder som vidtas under det eskalerade privilegierade tillståndet kan påverka systemet negativt eller orsaka att systemet blir otillgängligt.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Eskalera privilegier

1. Öppna [CloudSimple-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **Resurser,** välj det privata moln som du vill eskalera privilegier för.

3. Klicka på **Eskalera**längst ned på sidan Sammanfattning under **Ändra vSphere-behörigheter**.

    ![Ändra vSphere-behörighet](media/escalate-private-cloud-privilege.png)

4. Välj användartypen vSphere.  Endast `CloudOwner@cloudsimple.local` den lokala användaren kan eskaleras.

5. Välj det eskalerande tidsintervallet i listrutan. Välj den kortaste period som gör att du kan slutföra uppgiften.

6. Markera kryssrutan för att bekräfta att du förstår riskerna.

    ![Dialogrutan Eskalera behörighet](media/escalate-private-cloud-privilege-dialog.png)

7. Klicka på **OK**.

8. Eskaleringsprocessen kan ta ett par minuter. När du är klar klickar du på **OK**.

Privilegiet eskalering börjar och varar fram till slutet av det valda intervallet.  Du kan logga in på ditt privata moln vCenter för att utföra administrativa uppgifter.

> [!IMPORTANT]
> Endast en användare kan ha eskalerade privilegier.  Du måste de-eskalera användarens behörigheter innan du kan eskalera en annan användares behörigheter.

> [!CAUTION]
> Nya användare får endast läggas till *i Cloud-Owner-Group,* *Cloud-Global-Cluster-Admin-Group,* *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* eller *Cloud-Global-VM-Admin-Group*.  Användare som läggs till i gruppen *Administratörer* tas bort automatiskt.  Endast tjänstkonton får läggas till *i grupp-* och tjänstkonton för administratörer får inte användas för att logga in på vSphere-webbgränssnittet.

## <a name="extend-privilege-escalation"></a>Utöka eskalering av privilegier

Om du behöver ytterligare tid för att slutföra dina uppgifter kan du förlänga eskaleringsperioden för privilegiet.  Välj det extra intervall för eskalerande tid som gör att du kan slutföra de administrativa uppgifterna.

1. På **de privata molnen Resurser** > **Private Clouds** i CloudSimple-portalen väljer du det privata moln som du vill utöka eskalering av privilegier.

2. Klicka på **Utöka eskalering**av privilegier längst ned på fliken Sammanfattning.

    ![Utöka eskalering av privilegier](media/de-escalate-private-cloud-privilege.png)

3. Välj ett eskalerande tidsintervall i listrutan. Granska den nya eskaleringssluttiden.

4. Klicka på **Spara** om du vill förlänga intervallet.

## <a name="de-escalate-privileges"></a>De-eskalera privilegier

När dina administrativa uppgifter är slutförda bör du de-eskalera dina privilegier.  

1. På **de privata molnen Resurser** > **Private Clouds** i CloudSimple-portalen väljer du det privata moln som du vill de-eskalera privilegier för.

2. Klicka på **De-eskalera**.

3. Klicka på **OK**.

> [!IMPORTANT]
> Undvik eventuella fel genom att logga ut från vCenter och logga in igen efter de-eskalerande privilegier.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera vCenter-identitetskällor för att använda Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Installera säkerhetskopieringslösning för att [arbetsbelastningsdatorer för säkerhetskopiering](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)