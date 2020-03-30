---
title: Anslut AWS CloudTrail till Azure Sentinel | Microsoft-dokument
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588662"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Anslut Azure Sentinel till AWS CloudTrail

Använd AWS-kopplingen för att strömma alla dina AWS CloudTrail-händelser till Azure Sentinel. Den här anslutningsprocessen delegerar åtkomst för Azure Sentinel till dina AWS-resursloggar, vilket skapar en förtroenderelation mellan AWS CloudTrail och Azure Sentinel. Detta åstadkoms på AWS genom att skapa en roll som ger behörighet till Azure Sentinel att komma åt dina AWS-loggar.

## <a name="prerequisites"></a>Krav

Du måste ha skrivbehörighet på Azure Sentinel-arbetsytan.

> [!NOTE]
> Azure Sentinel samlar cloudTrail-händelser från alla regioner. Vi rekommenderar att du inte streamar händelser från en region till en annan.

## <a name="connect-aws"></a>Ansluta AWS 


1. I Azure Sentinel väljer du Datakopplingar och väljer sedan **Amazon Web Services-raden** i tabellen och i AWS-fönstret till höger klickar du på **Öppna kopplingssida**. **Data connectors**

1. Följ instruktionerna under **Konfiguration** med hjälp av följande steg.
 
1.  Välj **IAM**under **Säkerhet, & Compliance**i Konsolen Amazon Web Services.

    ![AWS1 (AVS1)](./media/connect-aws/aws-1.png)

1.  Välj **Roller** och välj **Skapa roll**.

    ![AWS2 (AVS2)](./media/connect-aws/aws-2.png)

1.  Välj **ett annat AWS-konto.** I fältet **Konto-ID** anger du **Microsoft Account ID** (**123412341234**) som finns på AWS-anslutningssidan i Azure Sentinel-portalen.

    ![AWS3 (AVS3)](./media/connect-aws/aws-3.png)

1.  Kontrollera att **Kräv externt ID** är markerat och ange sedan det externa ID (Workspace ID) som finns på AWS-anslutningssidan i Azure Sentinel-portalen.

    ![AWS4 (AVS4)](./media/connect-aws/aws-4.png)

1.  Under **Bifoga behörighetsprincip** väljer du **AWSCloudTrailReadOnlyAccess**.

    ![AWS5 (AVSA5)](./media/connect-aws/aws-5.png)

1.  Ange en tagg (valfritt).

    ![AWS6 (AVS6)](./media/connect-aws/aws-6.png)

1.  Ange sedan ett **rollnamn** och välj knappen **Skapa roll.**

    ![AWS7 (AVS7)](./media/connect-aws/aws-7.png)

1.  Välj den roll du skapade i listan Roller.

    ![AWS8 (AVS8)](./media/connect-aws/aws-8.png)

1.  Kopiera **rollen ARN**. I Azure Sentinel-portalen klistrar du in den i **fältet Roll i** Azure Sentinel-portalen och klickar på Lägg **till**.

    ![AWS9 (AVSE)](./media/connect-aws/aws-9.png)

1. Om du vill använda det relevanta schemat i Log Analytics för AWS-händelser söker du efter **AWSCloudTrail**.



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter AWS CloudTrail till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.

