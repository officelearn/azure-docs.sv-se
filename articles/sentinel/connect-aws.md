---
title: Anslut Symantec AWS-data till Azure Sentinel-Preview | Microsoft Docs
description: Lär dig hur du ansluter Symantec AWS-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 214269bc5c854aa4d3bfd508b0adb5a53ec096df
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673978"
---
# <a name="connect-azure-sentinel-to-aws"></a>Ansluta Azure Sentinel till AWS

Använda AWS-anslutningen för att strömma alla AWS CloudTrail-händelser till Azure Sentinel. Den här anslutningsprocessen delegerar åtkomst för Azure Sentinel till din AWS resurs-loggar en förtroenderelation mellan AWS CloudTrail och Azure Sentinel skapas. Detta åstadkoms på AWS genom att skapa en roll som ger behörighet till Azure Sentinel-åtkomst till dina AWS-loggar.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha behörighet att skriva i Azure Sentinel-arbetsytan.

## <a name="connect-aws"></a>Ansluta AWS 
 
1.  I Amazon Web Services-konsolen under **säkerhet, identitet och efterlevnad**, klicka på **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

2.  Välj **roller** och klicka på **skapa roll**.

    ![AWS2](./media/connect-aws/aws-2.png)

3.  Välj **en annan AWS-konto.** I den **konto-ID** fältet, anger du den **Microsoft-kontos ID** (**123412341234**) som finns på sidan för AWS-anslutningen i Sentinel-Azure-portalen.

    ![AWS3](./media/connect-aws/aws-3.png)

4.  Se till att **kräver externt ID** har valts och sedan och ange det externa ID (arbetsyte-ID) som finns på sidan för AWS-anslutningen i Sentinel-Azure-portalen.

    ![AWS4](./media/connect-aws/aws-4.png)

5.  Under **koppla behörigheter princip** Välj **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

6.  Ange en tagg (valfritt).

    ![AWS6](./media/connect-aws/aws-6.png)

7.  Ange sedan en **rollnamn** och klicka på den **skapa roll** knappen.

    ![AWS7](./media/connect-aws/aws-7.png)

8.  Välj den roll som du skapade i listan över roller.

    ![AWS8](./media/connect-aws/aws-8.png)

9.  Kopiera den **rollen ARN** och klistra in den i den **roll att lägga till** i Azure Sentinel-portalen.

    ![AWS9](./media/connect-aws/aws-9.png)

10. Om du vill använda relevanta schemat i Log Analytics för AWS-händelser, söka efter **AWSCloudTrail**.



## <a name="next-steps"></a>Nästa steg
I det här dokumentet lärde du dig att ansluta AWS CloudTrail till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

