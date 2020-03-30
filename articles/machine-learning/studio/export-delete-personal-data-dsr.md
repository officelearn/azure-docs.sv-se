---
title: Exportera och ta bort data
titleSuffix: ML Studio (classic) - Azure
description: Produktdata som lagras av Azure Machine Learning Studio (klassisk) är tillgängliga för export och borttagning via Azure-portalen och även genom autentiserade REST-API:er. Telemetridata kan nås via Azure Privacy Portal. Den här artikeln visar hur du gör.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: c380d10d0c68794ec3810cea25341d68bb41400d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251692"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Exportera och ta bort användardata i produkten från Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Du kan ta bort eller exportera produktdata som lagras av Azure Machine Learning Studio (klassisk) med hjälp av Azure-portalen, Studio-gränssnittet (klassiskt), PowerShell och autentiserade REST-API:er. I den här artikeln får du lära dig hur du gör. 

Telemetridata kan nås via Azure Privacy-portalen. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Vilka typer av användardata samlar Studio (klassiskt) in?

För den här tjänsten består användardata av information om användare som har behörighet att komma åt arbetsytor och telemetriposter för användarinteraktioner med tjänsten.

Det finns två typer av användardata i Machine Learning Studio (klassisk):
- **Personuppgifter:** Konto-ID:er och e-postadresser som är kopplade till ett konto.
- **Kunddata:** Data som du har laddat upp för att analysera.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Studio (klassiska) kontotyper och hur data lagras

Det finns tre typer av konton i Machine Learning Studio (klassisk). Vilken typ av konto du har avgör hur dina data lagras och hur du kan ta bort eller exportera dem.

- En **gästarbetsyta** är ett gratis, anonymt konto. Du registrerar dig utan att ange autentiseringsuppgifter, till exempel en e-postadress eller ett lösenord.
    -  Data rensas när gästarbetsytan har gått ut.
    - Gästanvändare kan exportera kunddata via paketet UI, REST API:er eller PowerShell.
- En **kostnadsfri arbetsyta** är ett gratis konto som du loggar in på med Microsoft-kontouppgifter – en e-postadress och ett lösenord.
    - Du kan exportera och ta bort personliga uppgifter och kunddata som är föremål för registrerades rättigheter (DSR) förfrågningar.
    - Du kan exportera kunddata via paketet UI, REST-API:er eller PowerShell.
    - För kostnadsfria arbetsytor som inte använder Azure AD-konton kan telemetri exporteras med hjälp av sekretessportalen.
    - När du tar bort arbetsytan tar du bort alla personliga kunddata.
- En **vanlig arbetsyta** är ett betalkonto som du har åtkomst till med inloggningsuppgifter.
    - Du kan exportera och ta bort personliga data och kunddata som är föremål för DSR-begäranden.
    - Du kan komma åt data via Azure Privacy Portal
    - Du kan exportera personliga data och kunddata via UI-, REST-API:erna eller PowerShell-paketet
    - Du kan ta bort dina data i Azure-portalen.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Ta bort arbetsytedata i Studio (klassisk) 

### <a name="delete-individual-assets"></a>Ta bort enskilda tillgångar

Användare kan ta bort resurser på en arbetsyta genom att markera dem och sedan välja borttagningsknappen.

![Ta bort resurser i Machine Learning Studio (klassisk)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Ta bort en hel arbetsyta

Användare kan också ta bort hela arbetsytan:
- Betald arbetsyta: Ta bort via Azure-portalen.
- Ledig arbetsyta: Använd knappen Ta bort i fönstret **Inställningar.**

![Ta bort en ledig arbetsyta i Machine Learning Studio (klassisk)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Exportera Studio (klassiska) data med PowerShell
Använd PowerShell för att exportera all din information till ett bärbart format från Azure Machine Learning Studio (klassisk) med hjälp av kommandon. Mer information finns i [powershell-modulen för Azure Machine Learning Studio (klassisk)](powershell-module.md) artikel.

## <a name="next-steps"></a>Nästa steg

Dokumentation som omfattar webbtjänster och fakturering av åtagandeabonnemang finns i [Azure Machine Learning Studio (klassisk) REST API-referens](https://docs.microsoft.com/rest/api/machinelearning/). 
