---
title: Exportera och ta bort dina data från Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: 'I produkten data som lagras av Azure Machine Learning Studio är tillgängliga för export eller tas bort via Azure-portalen och även via autentiserade REST API: er. Dessa data kan nås via Azure sekretess-portalen. Den här artikeln visar hur du gör.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 096f310b7c82718ea836d1b2048ca665a1e1e2f4
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55994967"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio"></a>Exportera och ta bort i produkten användardata från Azure Machine Learning Studio

Du kan ta bort eller export-produktdata som lagras av Azure Machine Learning Studio med hjälp av Azure portal, gränssnittet Studio, PowerShell, och autentiserade REST API: er. Den här artikeln visar hur du gör. 

Dessa data kan nås via sekretess för Azure-portalen. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>Vilka typer av användardata samlar Studio?

Användardata består av information om användare som har behörighet att komma åt arbetsytor och telemetri poster i användarinteraktioner med tjänsten för den här tjänsten.

Det finns två typer av användardata i Machine Learning Studio:
- **Personligt kontodata:** Account ID: N och e-postadresser som är associerad med ett konto.
- **Kundinformation:** Data som du laddade upp för att analysera.

## <a name="studio-account-types-and-how-data-is-stored"></a>Studio-kontotyper och hur data lagras

Det finns tre typer av konton i Machine Learning Studio. Vilken typ av konto som du har avgör hur dina data lagras och hur du kan ta bort eller exportera den.

- En **gäst arbetsytan** är ett kostnadsfritt, anonym-konto. Du registrera dig utan att ange autentiseringsuppgifter, till exempel ett e-postadress eller lösenord.
    -  Data tas bort när arbetsytan gäst upphör att gälla.
    - Gästanvändare kan exportera kunddata via Användargränssnittet, REST API: er eller PowerShell-paketet.
- En **kostnadsfri arbetsyta** är ett kostnadsfritt konto som du loggar in med Microsoft-konto autentiseringsuppgifter – ett e-postadress och lösenord.
    - Du kan exportera och ta bort personliga och kundens data som omfattas av DSR-begäranden för (datasubjektsrättigheter).
    - Du kan exportera kunddata via Användargränssnittet, REST API: er eller PowerShell-paketet.
    - Kostnadsfritt kan arbetsytor som inte använder Azure AD-konton, telemetri exporteras med hjälp av sekretess-portalen.
    - När du tar bort arbetsytan kan du ta bort alla personliga kunddata.
- En **standardarbetsytan** är ett betalt konto som du har åtkomst till med inloggningsuppgifter.
    - Du kan exportera och ta bort personliga och kundens data som omfattas av DSR-begäranden.
    - Du kan komma åt data via sekretess för Azure-portalen
    - Du kan exportera personliga och kundens data via Användargränssnittet, REST API: er eller PowerShell-paketet
    - Du kan ta bort dina data i Azure-portalen.

## <a name="delete"></a>Ta bort arbetsytedata i Studio 

### <a name="delete-individual-assets"></a>Ta bort enskilda tillgångar

Användare kan ta bort tillgångar i en arbetsyta genom att markera dem och sedan välja knappen Ta bort.

![Ta bort tillgångar i Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Ta bort en hel arbetsyta

Användare kan också ta bort hela arbetsytan:
- Betalda arbetsyta: Ta bort via Azure portal.
- Kostnadsfri arbetsyta: Använd knappen Ta bort i den **inställningar** fönstret.

![Ta bort en kostnadsfri arbetsyta i Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>Exportera Studio data med PowerShell
Exportera din information till en bärbar format från Azure Machine Learning Studio med hjälp av kommandon med hjälp av PowerShell. Mer information finns i den [PowerShell-modulen för Azure Machine Learning](powershell-module.md) artikeln.

## <a name="next-steps"></a>Nästa steg

Dokumentation som täcker webbtjänster och åtagande plan fakturering finns [Azure Machine Learning REST API-referens](https://docs.microsoft.com/rest/api/machinelearning/). 
