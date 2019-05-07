---
title: Ansluta Azure Information Protection-data till Azure Sentinel-Preview | Microsoft Docs
description: Lär dig mer om att ansluta Azure Information Protection i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 4b73edd10521b23fb4befbe4fe7d9f0c7b496de3
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204302"
---
# <a name="connect-data-from-azure-information-protection"></a>Anslut data från Azure Information Protection

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma loggar från [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) i Azure Sentinel-med ett enda klick. Azure Information Protection hjälper dig att skydda dina data oavsett om de lagras i molnet eller i en lokal infrastruktur och kontroll och hjälper dig att skydda e-post, dokument och känsliga data som du delar utanför företaget. Förbättra dataskydd vid alla tidpunkter med Azure Information Protection enkel klassificering och inbäddade etiketter och behörigheter. När du ansluter Azure Information Protection till Azure Sentinel du stream alla aviseringar från Azure Information Protection i Azure Sentinel.


## <a name="prerequisites"></a>Nödvändiga komponenter

- Användare med global administratör, säkerhetsadministratör eller information protection-behörigheter


## <a name="connect-to-azure-information-protection"></a>Ansluta till Azure Information Protection

Om du redan har Azure Information Protection garanterar att de är [aktiverat i nätverket](https://docs.microsoft.com/azure/information-protection/activate-service).
Om Azure Information Protection har distribuerats och hämtar data aviseringsdata kan enkelt strömmas till Sentinel-Azure.


1. I Azure Sentinel väljer **datakopplingar** och klicka sedan på den **Azure Information Protection** panelen.

2. Gå till den [Azure Information Protection-portalen](https://portal.azure.com/?ScannerConfiguration=true&EndpointDiscovery=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/quickstartBlade) 

3. Under **anslutning**, ställa in strömning av loggar från Azure Information Protection till Azure Sentinel genom att klicka på [konfigurera analytics](https://portal.azure.com/#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/analyticsOnboardBlade)

4. Välj den arbetsyta som du har distribuerat Azure Sentinel. 

5. Klicka på **OK**.

6. Om du vill använda relevanta schemat i Log Analytics för Azure Information Protection-aviseringar, Sök efter **InformationProtectionLogs_CL**.




## <a name="next-steps"></a>Nästa steg
I det här dokumentet lärde du dig att ansluta Azure Information Protection till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).
