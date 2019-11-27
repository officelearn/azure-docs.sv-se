---
title: Användarprofil och ID för användning med Azure-anteckningsböcker
description: 'Hur du skapar och hanterar din användar profil och ditt användar-ID med Azure Notebooks, som blir en del av URL: en för delade antecknings böcker.'
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 0874fe8223ae9c49ccfe4e8efedf2620117a0f8c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277616"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>Din profil och användar-ID för Azure-anteckningsböcker

Inom Azure anteckningsböcker kraftfulla, samarbetsfunktioner utrymme anger din användarprofil offentliga avbildningen till andra:

[![en Azure Notebooks profil sida](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Ditt användar-ID är en del av URL: er som du använder för att dela projekt och anteckningsböcker. I följande lista beskrivs de olika URL-mönster:

- `https://notebooks.azure.com/<user_id>`: din profil sida.
- `https://notebooks.azure.com/<user_id>/projects`: dina projekt. Du ser alla projekt. andra användare ser bara dina offentliga projekt.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: projektfiler.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: klonar av ett enskilt projekt.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: HTML-förhands granskningen av en enskild antecknings bok eller fil.

## <a name="your-user-id"></a>Ditt användar-ID

När du loggar in Azure-anteckningsböcker för första gången, tilldelas automatiskt en tillfällig användar-ID, till exempel ”anon-idr3ca” i ditt konto. Så länge som du har ett användar-ID som börjar med ”anon-”, Azure anteckningsböcker uppmanas du att ändra den när du loggar in:

![Fråga om för att skapa ett användar-ID när du loggar in Azure-anteckningsböcker](media/accounts/create-user-id.png)

Ett **Konfigurera användar-ID-** kommando visas också bredvid det tillfälliga användar namnet:

![Konfigurera användar-ID-kommandot som visas när du använder ett tillfälligt ID](media/accounts/configure-user-id-command.png)

Du kan också ändra ditt användar-ID när som helst på din profilsida.

Ett användar-ID måste bestå av mellan fyra och sexton bokstäver, siffror och bindestreck. Inga andra tecken tillåts och användar-ID får inte börja eller sluta med ett bindestreck eller använda flera bindestreck i rad. Eftersom användar-ID: n är unika för alla Azure Notebooks-konton kan du se meddelandet "användar-ID används redan". (Meddelandet visas också om du försöker använda ett Microsoft-varumärke som användar-ID.) I dessa fall väljer du ett annat användar-ID.

> [!Important]
> Ändra ditt ID upphäver alla URL: er som du kanske har delat med ditt tidigare-ID. Du kan ändra ditt ID tillbaka till din tidigare ID ska verifiera länkarna. Det är dock möjligt för en annan användare att hämta en oanvända ID under tiden.

## <a name="your-profile"></a>Din profil

Din profil består av offentligt synlig information på URL: en `https://notebooks.azure.com/<user_id>`. Din profilsida visar även dina senast använda projekt och alla stjärnmärkta projekt.

Om du vill redigera din profil använder du kommandot **Redigera profil information** på din profil sida. Avsnitt i din profil är följande:

| Section | Innehåll |
| --- | --- |
| Profilfoto | En bild som visas på din profilsida. |
| Kontoinformation | Visningsnamn, användar-ID och offentlig e-postkonto. E-postkontot här ger andra användare ett medel att kontakta dig och kan skilja sig från det [konto](azure-notebooks-user-account.md) som du använder för att logga in på Azure Notebooks. |
| Profilinformation | Din plats, företag, befattning, webbplats och en kort beskrivning av själv. |
| Sociala profiler | GItHub, Twitter och Facebook-ID, om du vill dela dem. |
| Sekretessinställningar | Innehåller två kommandon:<ul><li>**Exportera min profil**: skapar och laddar ned en *. zip* -fil som innehåller all information som Azure Notebooks sparar i din profil, inklusive ditt fotografi, profil information och säkerhets loggar.</li><li>**Ta bort mitt konto**: all personlig information som lagras i Azure Notebooks tas bort permanent.</li></ul> |
| Aktivera funktioner | Gör att du kan styra beteendet för Azure-datorer:<ul><li>**Enhetlig klient del för bärbara datorer**: möjliggör snabbare start av antecknings böcker och bättre beständighet.</li><li>**Kör i JupyterLab som standard**: Azure Notebooks tillhandahåller ett enkelt användar gränssnitt som är lämpligt för de flesta användare. JupyterLab tillhandahåller en mer omfattande men mer komplicerad gränssnitt för erfarna användare.</li><li>**VNext webbplats**: aktiverar den moderna Webblayouten som visas i den här dokumentationen.</li></ul> |

## <a name="next-steps"></a>Nästa steg  

> [!div class="nextstepaction"]
> [Självstudie: skapa en kör en Jupyter-anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
