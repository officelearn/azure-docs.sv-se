---
title: Exportera avisering loggar till en Azure Log Analytics-arbetsyta | Microsoft Docs
description: Den här artikeln förklarar hur du exporterar de aviseringar som har genererats av Azure Security Center en Log Analytics-arbetsyta.
services: security-center
documentationcenter: na
author: monhaber
manager: mbaldwin
editor: ''
ms.assetid: 2bc67ce5-ec3a-49df-afc3-b4bad5d8ce21
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/13/2019
ms.author: monhaber
ms.openlocfilehash: 12b8b376a0ff149cbfafc7fe69dd8da636280de8
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56653726"
---
# <a name="export-azure-security-center-alerts"></a>Exportera Azure Security Center-aviseringar
Den här artikeln förklarar hur du exporterar aviseringarna genereras av Azure Security Center till en plats som kan användas av andra verktyg.

Några av Security Center-kunder uttryckt en behovet av att använda data som genereras av Security Center från en central plats som innehåller alla aggregerade eller bearbetade data (till exempel hotidentifieringsaviseringar, säkerhetsrekommendationer för principen, täckning skydda poäng, osv.). På så sätt kan data som ska analyseras ytterligare och bearbetas. Eller så det kan också vara ett annat sätt att programmässigt använder data, istället för att använda Security Center-portalen.

Eftersom Azure Log Analytics-arbetsytor som används för att lagra och bearbeta rådata som samlas in från virtuella datorer, den **exportera till Log Analytics** funktionen har lösningen för detta behov av strömmande data till en användardefinierad arbetsyta.

## <a name="prerequisites"></a>Förutsättningar
Du behöver en Log Analytics-arbetsyta. Vi rekommenderar att du använder samma Log Analytics-arbetsytan som du definierade i inställningar för insamling. När du definierar en för insamling av data. [Log Analytics-arbetsyta för datainsamling](security-center-enable-data-collection.md)


## <a name="export-the-alerts"></a>Exportera aviseringarna
1. Klicka på **säkerhetsprincip**.
1. På raden för den prenumeration som du vill använda, klickar du på **redigera inställningar för**.
  ![Redigera inställningar för](./media/security-center-alert-export/edit_settings.png "redigera inställningar")
1. Klicka på **datainsamling**.
1. Rulla ned till **Store Security Center utökat data** och klickar på växlingsknappen kan **på**.

   ![Store dataalternativet](./media/security-center-alert-export/store_data_option.png "säkerhetsprincip")
1. Välj den arbetsyta som du vill exportera aviseringar till.

    > [!NOTE]
    > Du kan inte exportera loggaviseringar till standardarbetsytan Security Center. Som vi nämnde i den [krav](#Prerequisites), måste du använda en Log Analytics-arbetsyta.

1. Klicka på **spara** (överst på skärmen).