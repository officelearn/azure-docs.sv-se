---
title: Anslut AWS CloudTrail till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter AWS CloudTrail-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 5cbef1f31ea7088d4fab4888f5630af1b765a910
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588662"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Ansluta Azure Sentinel till AWS CloudTrail

Använd AWS-anslutningen för att strömma alla dina AWS CloudTrail-händelser till Azure Sentinel. Den här anslutnings processen delegerar åtkomst för Azure Sentinel till dina AWS-resurs loggar och skapar en förtroende relation mellan AWS CloudTrail och Azure Sentinel. Detta görs på AWS genom att skapa en roll som ger behörighet till Azure Sentinel för att få åtkomst till dina AWS-loggar.

## <a name="prerequisites"></a>Krav

Du måste ha Skriv behörighet på Azure Sentinel-arbetsytan.

> [!NOTE]
> Azure Sentinel samlar in CloudTrail-händelser från alla regioner. Vi rekommenderar att du inte strömmar händelser från en region till en annan.

## <a name="connect-aws"></a>Ansluta AWS 


1. I Azure Sentinel väljer du **data kopplingar** och väljer sedan den **Amazon Web Services** raden i tabellen och i fönstret AWS till höger klickar du på **Öppna kopplings sida**.

1. Följ anvisningarna under **konfigurationen** med hjälp av följande steg.
 
1.  I Amazon Web Services-konsolen, under **säkerhet, identitet & efterlevnad**, väljer du **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Välj **roller** och välj **skapa roll**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Välj **ett annat AWS-konto.** I fältet **konto-ID** anger du det **ID för Microsoft-konto** (**123412341234**) som finns på anslutnings sidan för AWS på Azure Sentinel-portalen.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Se till att **extern ID** är markerat och ange sedan det externa ID (arbetsyte-ID) som finns på anslutnings sidan för AWS på Azure Sentinel-portalen.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  Under **bifoga behörighets princip** väljer du **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Ange en tagg (valfritt).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Ange sedan ett **rollnamn** och välj knappen **skapa roll** .

    ![AWS7](./media/connect-aws/aws-7.png)

1.  I listan Roller väljer du den roll som du skapade.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Kopiera **rollens ARN**. I Azure Sentinel-portalen, i fönstret Amazon Web Services anslutning, klistrar du in det i fältet **roll som ska läggas** till och klickar på **Lägg till**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Om du vill använda det relevanta schemat i Log Analytics för AWS-händelser söker du efter **AWSCloudTrail**.



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter AWS-CloudTrail till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.

