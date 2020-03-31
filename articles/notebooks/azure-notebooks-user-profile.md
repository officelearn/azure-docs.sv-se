---
title: Användarprofil och ID för användning med förhandsversionen av Azure Notebooks
description: Så här skapar och hanterar du din användarprofil och ditt användar-ID med Azure Notebooks, som blir en del av URL:en för delade anteckningsböcker.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: d90eebf1b7b463e038bc5e54f51df0eb6ca746c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646287"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Ditt profil- och användar-ID för förhandsversionen av Azure Notebooks

Inom det kraftfulla samarbetsutrymmet i Azure Notebooks presenterar din användarprofil din offentliga avbildning för andra:

[![Profilsida för en profilsida för Azure-anteckningsböcker](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Ditt användar-ID är en del av webbadresserna som du använder för att dela projekt och anteckningsböcker. I följande lista beskrivs de olika URL-mönstren:

- `https://notebooks.azure.com/<user_id>`: Din profilsida.
- `https://notebooks.azure.com/<user_id>/projects`: Dina projekt. Du ser alla projekt; andra användare ser bara dina offentliga projekt.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Projektfiler.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Kloner av ett specifikt projekt.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: HTML-förhandsgranskningen av en viss anteckningsbok eller fil.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="your-user-id"></a>Ditt användar-ID

När du loggar in på Azure Notebooks för första gången tilldelas ditt konto automatiskt ett tillfälligt användar-ID, till exempel "anon-idr3ca". Så länge du har ett användar-ID som börjar med "anon-", uppmanas Azure Notebooks dig att ändra det när du loggar in:

![Fråga om att skapa ett användar-ID när du loggar in på Azure Notebooks](media/accounts/create-user-id.png)

Ett **kommandot Konfigurera användar-ID** visas också bredvid det tillfälliga användarnamnet:

![Konfigurera kommandot Användar-ID som visas när du använder ett tillfälligt ID](media/accounts/configure-user-id-command.png)

Du kan också ändra ditt användar-ID när som helst på din profilsida.

Ett användar-ID måste bestå av mellan fyra och sexton bokstäver, siffror och bindestreck. Inga andra tecken är tillåtna och användar-ID kan inte börja eller sluta med ett bindestreck eller använda flera bindestreck på en rad. Eftersom användar-ID:er är unika för alla Azure Notebooks-konton kan meddelandet "Användar-ID redan används". (Meddelandet visas också om du försöker använda ett Microsoft-varumärke som användar-ID.) I dessa fall väljer du ett annat användar-ID.

> [!Important]
> Om du ändrar ID:et ogiltigförklaras alla webbadresser som du kan ha delat med ditt tidigare ID. Du kan ändra ditt ID tillbaka till ditt tidigare ID för att förnya länkarna. Det är dock möjligt för en annan användare att göra anspråk på ett oanvänt ID under tiden.

## <a name="your-profile"></a>Din profil

Din profil består av offentligt synlig information `https://notebooks.azure.com/<user_id>`på webbadressen. Din profilsida visar också dina nyligen använda projekt och alla stjärnmärkta projekt.

Om du vill redigera din profil använder du kommandot **Redigera profilinformation** på din profilsida. Avsnitten i din profil är följande:

| Section | Innehåll |
| --- | --- |
| Profilfoto | En bild som visas på din profilsida. |
| Kontoinformation | Ditt visningsnamn, användar-ID och ditt offentliga e-postkonto. E-postkontot här ger andra användare ett sätt att kontakta dig och kan skilja sig från det [konto](azure-notebooks-user-account.md) du använder för att logga in på Azure Notebooks själv. |
| Profilinformation | Din plats, företag, befattning, webbplats och en kort beskrivning av dig själv. |
| Sociala profiler | Dina GItHub-, Twitter- och Facebook-ID:n om du vill dela dem. |
| Sekretessinställningar | Innehåller två kommandon:<ul><li>**Exportera min profil**: skapar och hämtar en *ZIP-fil* som innehåller all information som Azure Notebooks sparar i din profil, inklusive ditt fotografi, profilinformation och säkerhetsloggar.</li><li>**Ta bort mitt konto:** Tar permanent bort all personlig information som lagras i Azure-anteckningsböcker.</li></ul> |
| Aktivera webbplatsfunktioner | Gör att du kan styra aspekter av beteendet hos Azure Notebooks:<ul><li>**Unified Frontend for Notebooks**: möjliggör snabbare start av bärbara datorer och bättre uthållighet.</li><li>**Kör i JupyterLab som standard:** Som standard tillhandahåller Azure Notebooks ett enkelt användargränssnitt som passar de flesta användare. JupyterLab ger ett rikare men mer komplicerat gränssnitt för erfarna användare.</li><li>**VNext Webbplats:** möjliggör den moderniserade webblayout som visas i denna dokumentation.</li></ul> |

## <a name="next-steps"></a>Nästa steg  

> [!div class="nextstepaction"]
> [Självstudiekurs: skapa en kör en Jupyter anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
