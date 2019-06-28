---
title: Eskalera privilegier för privat moln – Azure VMware-lösning genom CloudSimple
description: Beskriver hur du skaffa sig fler behörigheter i ditt privata moln för administrativa funktioner i vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d11c88b91b13cca13120a9203e376fdc2c3d6d8d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333129"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Eskalera privilegier för privat moln vCenter från CloudSimple-portalen 

För administrativ åtkomst till ditt vCenter för privat moln kan du tillfälligt eskalera CloudSimple-privilegier.  Med förhöjd behörighet kan du installera VMware-lösningar, Lägg till källor med identity och hantera användare.

Nya användare kan skapas på domänen för enkel inloggning vCenter och få åtkomst till vCenter.  När du skapar nya användare, lägga till dem i CloudSimple builtin grupper för åtkomst till vCenter.  Mer information finns i [CloudSimple privat moln behörighetsmodellen av VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Inte göra några konfigurationsändringar för komponenterna. Åtgärder som vidtagits under eskalerade Privilegierade tillståndet kan påverka systemet negativt eller kan orsaka att systemet blir otillgänglig.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Eskalera privilegier

1. Åtkomst till den [CloudSimple portal](access-cloudsimple-portal.md).

2. Öppna den **resurser** väljer du den privata moln som du vill skaffa sig fler behörigheter.

3. Nästan längst ned på sidan Sammanfattning under **ändra vSphere privilegier**, klickar du på **eskalera**.

    ![Ändra vSphere-behörighet](media/escalate-private-cloud-privilege.png)

4. Välj typ för vSphere-användare.  Endast **CloudOwner@cloudsimple.local** lokal användare kan vara eskaleras.

5. Välj tidsintervall eskalera från listrutan. Välj kortast som gör att du kan slutföra uppgiften.

6. Markera kryssrutan för att bekräfta att du förstår riskerna.

    ![Eskalera privilegier dialogrutan](media/escalate-private-cloud-privilege-dialog.png)

7. Klicka på **OK**.

8. Eskaleringsprocessen kan ta några minuter. När du är klar klickar du på **OK**.

Attack via behörighetseskalering börjar och varar fram till slutet av det valda intervallet.  Du kan logga in till ditt privata moln vCenter att utföra administrativa uppgifter.

> [!IMPORTANT]
> Endast en användare kan eskalera privilegier.  Du måste ta bort eskalera behörigheter för användaren innan du kan skaffa sig fler behörigheter för en annan användare.

## <a name="extend-privilege-escalation"></a>Utöka eskalering

Om du behöver mer tid att slutföra dina aktiviteter kan utöka du privilegier eskalering period.  Välj de ytterligare eskalera tidsintervall som gör det möjligt att utföra administrativa uppgifter.

1. På den **resurser** > **privata moln** i CloudSimple-portalen väljer du de privata moln som du vill utöka eskalering.

2. Längst ned på fliken Sammanfattning klickar du på **utöka eskalering**.

    ![Utöka eskalering](media/de-escalate-private-cloud-privilege.png)

3. Välj ett tidsintervall för eskalera från listrutan. Granska nya eskalering sluttid.

4. Klicka på **spara** att utöka intervallet.

## <a name="de-escalate-privileges"></a>Ta bort eskalera privilegier

När dina administrativa aktiviteter är slutförda, bör du ta bort eskalera dina behörigheter.  

1. På den **resurser** > **privata moln** i CloudSimple-portalen väljer du det privata molnet som du vill ta bort eskalera privilegier.

2. Klicka på **ta bort eskalera**.

3. Klicka på **OK**.

> [!IMPORTANT]
> Logga ut från vCenter och logga in igen när du ta bort eskalera privilegier för att undvika eventuella fel.

## <a name="next-steps"></a>Nästa steg

* [Ställa in vCenter identitet källor för att använda Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Installera lösning för säkerhetskopiering till [säkerhetskopiera arbetsbelastning-datorer](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)