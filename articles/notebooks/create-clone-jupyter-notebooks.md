---
title: Skapa och klona Jupyter-anteckningsböcker i Azure
description: Projekt för Azure anteckningsböcker hantera en samling bärbara datorer och relaterade filer som du kan skapa en ny eller klona från en annan källa.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9b6a49e2-1d71-4c0b-9e5d-16e059427e38
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: c4325f0c9de0d945d1612a3ab4171a2bfe1be25b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106523"
---
# <a name="create-and-clone-projects"></a>Skapa och klona projekt

Azure anteckningsböcker ordnar dina Jupyter-anteckningsböcker och relaterade filer i logiska grupper som kallas *projekt*. Du skapar ett projekt först som en behållare och sedan skapa eller klona en eller flera datorer i en mapp tillsammans med andra projektfiler. (Den här processen visas i den [självstudien](tutorial-create-run-jupyter-notebook.md).)

Ett projekt har också enhetsspecifika metadata och andra konfigurationsinställningar som påverkar servern på vilken anteckningsböcker som körs, inklusive steg för anpassad konfiguration och installation av paket. Mer information finns i [hantera och konfigurera projekt](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Använd instrumentpanelen för Mina projekt

Din **Mina projekt** instrumentpanelen på `https://notebooks.azure.com/<userID>/projects` kan du visa, hantera, och skapa projekt:

[![](media/my-projects-dashboard.png "Min instrumentpanel för projekt i Azure-anteckningsböcker")](media/my-projects-dashboard.png#lightbox)

Vad du kan göra på instrumentpanelen beror på om du har loggat in med kontot som äger det användar-ID:

| Kommando | Tillgänglig för | Beskrivning |
| --- | --- | --- |
| **Kör** | Ägare | Startar projektserver och öppnar projektmappen i Jupyter. (Vanligare, du navigerar till en projektmapp först och sedan starta en anteckningsbok därifrån.) |
| **Ladda ned** | Vem som helst | Laddar ned en kopia av det valda projektet som en ZIP-fil. |
| **Filresurs** | Vem som helst | Visar delningsapplikationen popup-fönstret genom vilka du kan hämta en URL till ett valt projekt, dela till sociala medier, skicka ett e-postmeddelande med URL-Adressen och hämta HTML- eller Markdown-kod för med en ”dagbok Start”-märket (se [skaffa en start-märket](#obtain-a-launch-badge)) med URL-Adressen. |
| **Ta bort** | Ägare | Tar bort det valda projektet. Du kan inte ångra den här åtgärden. |
| **Terminal** | Ägare | Startar projektserver, och sedan öppnas ett nytt webbläsarfönster med bash terminal för den här servern. |
| **+ Nytt projekt** | Ägare | Skapar ett nytt projekt. Se [skapa ett nytt projekt](#create-a-new-project). |
| **Ladda upp Github-lagringsplatsen** | Ägare | Importerar en projektet från GitHub. [Importera ett projekt från GitHub](#import-a-project-from-github). |
| **Klona** | Vem som helst | Kopierar ett valt projekt till ditt eget konto. Du uppmanas att logga in om den inte redan. Se [klona en projektet](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Hämta en start-märket

När du använder den **resursen** kommandot och välj den **bädda in** fliken kan du kopiera HTML-kod eller Markdown som skapar en ”dagbok Start” symbol:

![Starta notebook märket ](https://notebooks.azure.com/launch.png)

Om du inte har ett projekt med Azure-datorer kan skapa du en länk som klonar från GitHub direkt med hjälp av följande mallar, och Ersätt rätt användarnamn och databasnamn:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

När du använder den **+ nytt projekt** kommandot Azure anteckningsböcker visar en **Skapa nytt projekt** popup-fönstret. Ange följande information i det här popup-fönstret och välj sedan **skapa**:

| Fält | Beskrivning |
| --- | --- |
| Projektnamn | Ett eget namn för ditt projekt som Azure-datorer använder för visning. Till exempel ”Ny anteckningsbok projektet”. |
| Projekt-ID | En anpassad identifierare som blir en del av URL: en som du använder för att dela ett projekt. Detta ID kan använda endast bokstäver, siffror och bindestreck och är begränsade till 30 tecken. Om du är osäker vad du använder, är en gemensam konvention att använda en gemen version av ditt projektnamn där blanksteg är aktiverade i bindestreck, till exempel ”min-notebook-projekt” (trunkerad vid behov för att passa längdbegränsningen). |
| Offentligt | Om ange, kan vem som helst med en länk till projektet. När du skapar ett privat projekt kan du avmarkera det här alternativet. |
| Initiera det här projektet med ett viktigt | Om angetts, skapas ett *README.md* filen i projektet. En *README.md* fil ger du dokumentationen för ditt projekt, om så önskas. |

## <a name="import-a-project-from-github"></a>Importera ett projekt från GitHub

Du kan enkelt importera en hel offentliga GitHub-lagringsplatsen som ett projekt, inklusive alla data och *README.md* filer. Använd den **överför GitHub-lagringsplatsen** kommandot, anger du följande information i popup-fönstret och välj sedan **Import**:

| Fält | Beskrivning |
| --- | --- |
| GitHub-lagringsplats | Namnet på källdatabasen på github.com. Till exempel för att klona Jupyter-anteckningsböcker för Azure Cognitive Services på [ https://github.com/Microsoft/cognitive-services-notebooks ](https://github.com/Microsoft/cognitive-services-notebooks), ange ”Microsoft/cognitive-services-bärbara datorer”.  |
| Klona rekursivt | GitHub-lagringsplatser kan innehålla flera underordnade databaser. Ange det här alternativet om du vill klona överordnade databasen och alla dess underordnade. Eftersom det är möjligt för en databas har många underordnade låter du avmarkera alternativet om du inte vet du behöver den. |
| Projektnamn | Ett eget namn för ditt projekt som Azure-datorer använder för visning. |
| Projekt-ID | En anpassad identifierare som blir en del av URL: en som du använder för att dela ett projekt. Detta ID kan använda endast bokstäver, siffror och bindestreck. |
| Offentligt | Om ange, kan vem som helst med en länk till projektet. När du skapar ett privat projekt kan du avmarkera det här alternativet. |

Importera en databas från GitHub importerar även dess historik. Du kan använda standard Git-kommandon från terminalen för att genomföra nya ändringar, hämta ändringarna från GitHub och så vidare.

## <a name="clone-a-project"></a>Klona ett projekt

Kloning av skapar en kopia av ett befintligt projekt i ditt eget konto där du kan köra och ändra alla olika bärbara datorer eller andra filen i projektet. Du kan också använda kloning för att göra kopior av dina egna projekt som du använder för att göra till experiment eller annat arbete utan att störa det ursprungliga projektet.

Att klona ett projekt:

1. På den **Mina projekt** instrumentpanelen, högerklicka på önskad projektet och välj **klona** (kortkommandot: c).

    ![Kloningskommandot på snabbmenyn för projektet](media/clone-command.png)

1. I den **klona projektet** popup-fönstret, ange ett namn och ID för klonade och ange om klonen är offentliga. De här inställningarna är samma som för en [nytt projekt](#create-a-new-project).

    ![Klona projektet popup-fönstret](media/clone-project.png)

1. När du har valt den **klona** knappen, Azure anteckningsböcker navigerar direkt till kopian.

## <a name="next-steps"></a>Nästa steg

- [Utforska exempelanteckningsböcker](azure-notebooks-samples.md)
- [Så här: konfigurera och hantera projekt](configure-manage-azure-notebooks-projects.md)
- [Så här: Installera paket från inom en anteckningsbok](install-packages-jupyter-notebook.md)
- [Så här: presentera ett bildspel](present-jupyter-notebooks-slideshow.md)
- [Så här: arbeta med filer](work-with-project-data-files.md)
- [Så här: åtkomst till dataresurser](access-data-resources-jupyter-notebooks.md)
- [Så här: använda Azure Machine Learning Services](use-machine-learning-services-jupyter-notebooks.md)
