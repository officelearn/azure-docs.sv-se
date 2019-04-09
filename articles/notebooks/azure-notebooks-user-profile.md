---
title: Användarprofil och ID för användning med Azure-anteckningsböcker
description: Så här att skapa och hantera din användarprofil och användar-ID med Azure-datorer.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 7d069d86-660f-4c94-b6e3-0c0f38c52d0e
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: b8c21b908ca9162a7e44c7af1e222babc6ee1eb7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265618"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>Din profil och användar-ID för Azure-anteckningsböcker

Inom Azure anteckningsböcker kraftfulla, samarbetsfunktioner utrymme anger din användarprofil offentliga avbildningen till andra:

[![An Azure anteckningsböcker profilsida](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Ditt användar-ID är en del av URL: er som du använder för att dela projekt och anteckningsböcker. I följande lista beskrivs de olika URL-mönster:

- `https://notebooks.azure.com/<user_id>`: Din profilsida.
- `https://notebooks.azure.com/<user_id>/projects`: Dina projekt. Du ser alla projekt. andra användare ser bara dina offentliga projekt.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Projektfiler.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Kloner med ett specifikt projekt.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: HTML-förhandsgranskning av en specifik anteckningsboken eller fil.

## <a name="your-user-id"></a>Ditt användar-ID

När du loggar in Azure-anteckningsböcker för första gången, tilldelas automatiskt en tillfällig användar-ID, till exempel ”anon-idr3ca” i ditt konto. Så länge som du har ett användar-ID som börjar med ”anon-”, Azure anteckningsböcker uppmanas du att ändra den när du loggar in:

![Fråga om för att skapa ett användar-ID när du loggar in Azure-anteckningsböcker](media/accounts/create-user-id.png)

En **konfigurera användar-ID** kommando visas också bredvid det tillfälliga användarnamnet:

![Konfigurera användar-ID-kommandot som visas när du använder ett tillfälligt ID](media/accounts/configure-user-id-command.png)

Du kan också ändra ditt användar-ID när som helst på din profilsida.

Ett användar-ID måste bestå av mellan fyra och sexton bokstäver, siffror och bindestreck. Inga andra tecken tillåts och användar-ID får inte börja eller sluta med ett bindestreck eller använda flera bindestreck i rad. Eftersom användar-ID är unikt för alla anteckningsböcker i Azure-konton kan du kanske ser meddelandet, ”användar-ID används redan”. (Meddelandet visas också om du försöker använda en av Microsofts varumärken som ett användar-ID.) I dessa fall kan du välja ett annat användar-ID.

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
| Sekretessinställningar | Innehåller två kommandon:<ul><li>**Exportera min profil**: skapas och hämtas en *.zip* -fil som innehåller all information som Azure anteckningsböcker sparar i din profil, inklusive foto, profilinformation och säkerhetsloggar.</li><li>**Ta bort mitt konto**: Tar bort din personliga information som lagras i Azure-anteckningsböcker.</li></ul> |
| Aktivera funktioner | Gör att du kan styra beteendet för Azure-datorer:<ul><li>**Enhetlig klientdel för bärbara datorer**: möjliggör snabbare notebook-start och bättre persistence.</li><li>**Kör i JupyterLab som standard**: Som standard ger ett enkelt användargränssnitt som passar för de flesta användare i Azure-anteckningsböcker. JupyterLab tillhandahåller en mer omfattande men mer komplicerad gränssnitt för erfarna användare.</li><li>**VNext webbplats**: aktiverar modernare Webblayout som visas i den här dokumentationen.</li></ul> |

## <a name="next-steps"></a>Nästa steg  

> [!div class="nextstepaction"]
> [Självstudier: skapa en kör en Jupyter-anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
