---
title: Tilldela MSI-åtkomst till en Azure-resurs, med hjälp av Azure portal
description: Stegvisa instruktioner för att tilldela en MSI åtkomst till en resurs till en annan resurs med hjälp av Azure portal.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2af83762fdcd3302f2af9b08585febab42387d79
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035990"
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Tilldela en hanterad tjänstidentitet åtkomst till en resurs med hjälp av Azure-portalen

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

När du har konfigurerat en Azure-resurs med en hanterad tjänstidentitet (MSI), kan du ge MSI-åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Den här artikeln visar hur du ge en Azure virtuell dator MSI åtkomst till ett Azure storage-konto med hjälp av Azure portal.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Använd RBAC för att tilldela MSI-åtkomst till en annan resurs

När du har aktiverat MSI på en Azure-resurs [, till exempel en Azure VM](msi-qs-configure-portal-windows-vm.md):

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen som du har konfigurerat MSI.

2. Navigera till önskad resurs som du vill ändra åtkomstkontroll. I det här exemplet ger vi åtkomst till en Azure virtuell dator till ett lagringskonto, så vi navigerar du till lagringskontot.

3. Välj den **åtkomstkontroll (IAM)** i resursen och välj **+ Lägg till**. Ange sedan den **rollen**, **tilldela åtkomst till virtuell dator**, och ange motsvarande **prenumeration** och **resursgrupp** där resursen finns. Du bör se resursen under området för sökvillkor. Markera resursen och välj **spara**. 

   ![Skärmbild för åtkomst-åtkomstkontroll (IAM)](../managed-service-identity/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  

4. Du kommer tillbaka till huvudfönstret **åtkomstkontroll (IAM)** sidan där du ser en ny post för den resursen MSI. I det här exemplet ”SimpleWinVM” virtuell dator från resursgruppen Demo har **deltagare** åtkomst till lagringskontot.

   ![Skärmbild för åtkomst-åtkomstkontroll (IAM)](../managed-service-identity/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

## <a name="troubleshooting"></a>Felsökning

Om MSI för resursen inte visas i listan över tillgängliga identiteter, kontrollerar du att MSI har aktiverats. I vårt fall kan vi gå tillbaka till den virtuella Azure-datorn och kontrollera följande:

- Titta på den **Configuration** sidan och se till att värdet för **MSI aktiverat** är **Ja**.
- Titta på den **tillägg** sidan och se till att MSI-tillägget har distribuerats.

Om något är fel, kan du behöva distribuera om MSI på resursen igen eller felsöka distributionsfel.

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](msi-overview.md).
- För att aktivera MSI på en Azure VM, se [konfigurera en Azure VM hanterad tjänstidentitet (MSI) med Azure portal](msi-qs-configure-portal-windows-vm.md).


