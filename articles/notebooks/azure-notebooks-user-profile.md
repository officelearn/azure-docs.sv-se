---
title: Användar profil och ID för användning med Azure Notebooks för hands version
description: 'Hur du skapar och hanterar din användar profil och ditt användar-ID med Azure Notebooks, som blir en del av URL: en för delade antecknings böcker.'
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: d90eebf1b7b463e038bc5e54f51df0eb6ca746c4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646287"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Din profil och ditt användar-ID för Azure Notebooks för hands version

Inom Azure anteckningsböcker kraftfulla, samarbetsfunktioner utrymme anger din användarprofil offentliga avbildningen till andra:

[![en Azure Notebooks profil sida](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Ditt användar-ID är en del av URL: er som du använder för att dela projekt och anteckningsböcker. I följande lista beskrivs de olika URL-mönster:

- `https://notebooks.azure.com/<user_id>`: Din profilsida.
- `https://notebooks.azure.com/<user_id>/projects`: Ditt projekt. Du ser alla projekt. andra användare ser bara dina offentliga projekt.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Project-filer.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Kloner med ett specifikt projekt.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: Den HTML-förhandsgranskningen av en specifik anteckningsboken eller fil.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="your-user-id"></a>Ditt användar-ID

När du loggar in Azure-anteckningsböcker för första gången, tilldelas automatiskt en tillfällig användar-ID, till exempel ”anon-idr3ca” i ditt konto. Så länge som du har ett användar-ID som börjar med ”anon-”, Azure anteckningsböcker uppmanas du att ändra den när du loggar in:

![Fråga om för att skapa ett användar-ID när du loggar in Azure-anteckningsböcker](media/accounts/create-user-id.png)

En **konfigurera användar-ID** kommando visas också bredvid det tillfälliga användarnamnet:

![Konfigurera användar-ID-kommandot som visas när du använder ett tillfälligt ID](media/accounts/configure-user-id-command.png)

Du kan också ändra ditt användar-ID när som helst på din profilsida.

Ett användar-ID måste bestå av mellan fyra och sexton bokstäver, siffror och bindestreck. Inga andra tecken tillåts och användar-ID får inte börja eller sluta med ett bindestreck eller använda flera bindestreck i rad. Eftersom användar-ID: n är unika för alla Azure Notebooks-konton kan du se meddelandet "användar-ID används redan". (Meddelandet visas också om du försöker använda ett Microsoft-varumärke som användar-ID.) I dessa fall väljer du ett annat användar-ID.

> [!Important]
> Ändra ditt ID upphäver alla URL: er som du kanske har delat med ditt tidigare-ID. Du kan ändra ditt ID tillbaka till din tidigare ID ska verifiera länkarna. Det är dock möjligt för en annan användare att hämta en oanvända ID under tiden.

## <a name="your-profile"></a>Din profil

Din profil består av allmänheten information på URL `https://notebooks.azure.com/<user_id>`. Din profilsida visar även dina senast använda projekt och alla stjärnmärkta projekt.

Redigera din profil genom att använda den **redigera profilinformation** på din profilsida. Avsnitt i din profil är följande:

| Section | Innehåll |
| --- | --- |
| Profilfoto | En bild som visas på din profilsida. |
| Kontoinformation | Visningsnamn, användar-ID och offentlig e-postkonto. Det här e-postkontot och ger andra användare en medelvärde att kontakta dig och kan skilja sig från den [konto](azure-notebooks-user-account.md) du använder för att logga in på Azure-datorer själva. |
| Profilinformation | Din plats, företag, befattning, webbplats och en kort beskrivning av själv. |
| Sociala profiler | GItHub, Twitter och Facebook-ID, om du vill dela dem. |
| Sekretessinställningar | Innehåller två kommandon:<ul><li>**Exportera min profil**: skapas och hämtas en *.zip* -fil som innehåller all information som Azure anteckningsböcker sparar i din profil, inklusive foto, profilinformation och säkerhetsloggar.</li><li>**Ta bort mitt konto**: tar bort din personliga information som lagras i Azure-anteckningsböcker.</li></ul> |
| Aktivera funktioner | Gör att du kan styra beteendet för Azure-datorer:<ul><li>**Enhetlig klientdel för bärbara datorer**: möjliggör snabbare notebook-start och bättre persistence.</li><li>**Kör i JupyterLab som standard**: som standard Azure-anteckningsböcker ger ett enkelt användargränssnitt som passar för de flesta användare. JupyterLab tillhandahåller en mer omfattande men mer komplicerad gränssnitt för erfarna användare.</li><li>**VNext webbplats**: aktiverar modernare Webblayout som visas i den här dokumentationen.</li></ul> |

## <a name="next-steps"></a>Nästa steg  

> [!div class="nextstepaction"]
> [Självstudier: skapa en kör en Jupyter-anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
