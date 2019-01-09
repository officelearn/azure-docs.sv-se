---
title: Snabbstart Ladda ned en inloggningsrapport med Azure-portalen | Microsoft Docs
description: Lär dig hur du laddar ned en inloggningsrapport med Azure-portalen
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 3e20af1c90f0e8a7a582d2d01dc4218a14496c40
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653296"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Snabbstart: Hämta en inloggningsrapport med Azure-portalen

I den här snabbstarten får du lära dig hur du hämtar inloggningsloggarna för din klient för de senaste 24 timmarna.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver:

* En Azure Active Directory-klient, med en premiumlicens för att visa inloggningsrapporten. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version.
* En användare som har rollen **säkerhetsadministratör,**,  **säkerhetsläsare**, **rapportläsare** eller **global administratör** för klienten. Dessutom kan alla användare i klienten kan komma åt sina egna inloggningsloggar.

## <a name="quickstart-download-a-sign-in-report"></a>Snabbstart: Ladda ned en inloggningsrapport

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj **Azure Active Directory** från det vänstra navigeringsfönstret och använd **Växla katalog** för att välja din active directory.
3. Från instrumentpanelen väljer du **Azure Active Directory** och sedan **Inloggningsloggar**. 
4. Välj **senaste 24 timmarna** i filterlistrutan **datum** och välj **Tillämpa** för att visa inloggningsloggarna för de senaste 24 timmarna. 
5. Välj knappen **Hämta** för att ladda ned en CSV-fil som innehåller de filtrerade posterna. 

![Rapportering](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Nästa steg

* [Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen](concept-sign-ins.md)
* [Azure Active Directory-rapporteringskvarhållning](reference-reports-data-retention.md)
* [Azure Active Directory-rapporteringssvarstider](reference-reports-latencies.md)
