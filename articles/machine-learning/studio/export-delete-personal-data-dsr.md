---
title: Exportera och ta bort dina data från Machine Learning Studio - Azure | Microsoft Docs
description: 'I produkten data som lagras av Azure Machine Learning Studio är tillgängliga för export och tas bort via Azure portal och även via autentiserade REST API: er. Telemetridata kan nås via sekretess Azure-portalen. Den här artikeln visar hur du gör.'
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: cgronlun
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.date: 05/25/2018
ms.openlocfilehash: 6317d4baba5775c1e5a4fda66de80dd8299d8fed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655610"
---
# <a name="export-and-delete-in-product-user-data-from-machine-learning-studio"></a>Exportera och ta bort i produkten användardata från Machine Learning Studio

Du kan ta bort eller exportera i produkten data lagras av Azure Machine Learning Studio med hjälp av Azure-portalen gränssnittet Studio, PowerShell och autentiseras REST API: er. Den här artikeln visar hur du gör. 

Telemetridata kan nås via sekretess i Azure-portalen. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>Vilka typer av användardata samlar Studio?

Användardata består av information om användare som har behörighet att komma åt arbetsytor och telemetri poster för användarinteraktioner med tjänsten för den här tjänsten.

Det finns två typer av användardata i Machine Learning Studio:
- **Personlig data:** konto-ID och e-postadresser som är associerad med ett konto.
- **Kundinformation:** som användes för att analysera Data.

## <a name="studio-account-types-and-how-data-is-stored"></a>Studio kontotyper och hur data lagras

Det finns tre typer av konton i Machine Learning Studio. Typ av konto som du bestämmer hur data lagras och hur du kan ta bort eller exportera den.

- En **gäst arbetsytan** är ett kostnadsfritt, anonyma konto. Du kan registrera dig för utan att ange autentiseringsuppgifter, till exempel en e-postadress eller lösenord.
    -  Data tas bort när arbetsytan gäst upphör att gälla.
    - Gästanvändare kan exportera kunddata via Användargränssnittet, REST API: er eller PowerShell-paketet.
- En **ledigt arbetsytan** är ett kostnadsfritt konto som du loggar in med Microsoft kontoautentiseringsuppgifter - ett e-postadress och lösenord.
    - Du kan exportera och ta bort personliga och kunden data, som ämne begäranden av rättigheter (DSR).
    - Du kan exportera kunddata via Användargränssnittet, REST API: er eller PowerShell-paketet.
    - Gratis kan arbetsytor som inte använder Azure AD-konton, telemetri exporteras med hjälp av sekretess-portalen.
    - När du tar bort arbetsytan kan du ta bort alla personliga kundens data.
- En **standard arbetsytan** är ett betalt konto som du har åtkomst till med inloggningsuppgifter.
    - Du kan exportera och ta bort personliga och kunden data, som DSR begäranden.
    - Du kan komma åt data på sekretess för Azure-portalen
    - Du kan exportera personliga och kunden data via Användargränssnittet, REST API: er eller PowerShell-paket
    - Du kan ta bort dina data i Azure-portalen.

## <a name="delete-workspace-data-in-studio"></a>Ta bort arbetsytan data i Studio 

### <a name="delete-individual-assets"></a>Ta bort enskilda tillgångar

Användare kan ta bort tillgångar i en arbetsyta genom att markera dem och sedan välja knappen Ta bort.

![Ta bort tillgångar i Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Ta bort en hela arbetsytan

Användare kan också ta bort deras hela arbetsytan:
- Betald arbetsytan: ta bort via Azure-portalen.
- Ledigt arbetsytan: Använd knappen Ta bort i den **inställningar** fönstret.

![Ta bort en kostnadsfri arbetsyta i Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>Exportera Studio data med PowerShell
Använd PowerShell för att exportera din information till en bärbar format från Azure Machine Learning Studio med hjälp av kommandon. Mer information finns i [PowerShell-modulen för Azure Machine Learning](powershell-module.md) artikel.

## <a name="next-steps"></a>Nästa steg

Dokumentation som omfattar webbtjänster och åtagande plan fakturering finns [Azure Machine Learning REST API-referens](https://docs.microsoft.com/en-us/rest/api/machinelearning/). 
