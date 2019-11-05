---
title: Skapa och klona Jupyter-anteckningsböcker i Azure
description: Azure Notebooks-projekt hanterar en samling antecknings böcker och relaterade filer, som du kan använda för att skapa nya eller klona från en annan källa.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 9b6a49e2-1d71-4c0b-9e5d-16e059427e38
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 3e0c49d1141d976558ece325baaa99fc9981275e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496705"
---
# <a name="create-and-clone-projects"></a>Skapa och klona projekt

Azure Notebooks ordnar dina Jupyter-anteckningsböcker och relaterade filer i logiska grupper som kallas *projekt*. Du skapar ett projekt först som en behållare och skapar eller klonar sedan en eller flera antecknings böcker i en mapp tillsammans med andra projektfiler. (Den här processen visas i [självstudien](tutorial-create-run-jupyter-notebook.md).)

Ett projekt upprätthåller även metadata och andra konfigurations inställningar som påverkar den server där antecknings böcker körs, inklusive anpassade installations steg och paket installation. Mer information finns i [Hantera och konfigurera projekt](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Använd instrument panelen för mina projekt

På instrument panelen för **Mina projekt** i `https://notebooks.azure.com/<userID>/projects` kan du Visa, hantera och skapa projekt:

[![instrument panelen för mina projekt i Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Vad du kan göra på instrument panelen beror på om du har loggat in med det konto som äger användar-ID: t:

| Kommando | Tillgänglig för | Beskrivning |
| --- | --- | --- |
| **Fungerar** | Ägare | Startar Project Server och öppnar projektmappen i Jupyter. (Mer ofta går du till en projektmapp först och startar sedan en antecknings bok där.) |
| **Ladda ned** | Personer | Laddar ned en kopia av det valda projektet som en ZIP-fil. |
| **Dela** | Personer | Visar popup-fönstret för delning som du kan använda för att hämta en URL till ett valt projekt, dela till sociala medier, skicka ett e-postmeddelande med URL: en och hämta både HTML-eller markdown-kod för med ett "lansera antecknings bok"-märke (se [Hämta en start-märke](#obtain-a-launch-badge)) med URL: en. |
| **Ta bort** | Ägare | Tar bort det markerade projektet. Du kan inte ångra den här åtgärden. |
| **Terminal** | Ägare | Startar Project Server och öppnar sedan ett nytt webbläsarfönster med bash-terminalen för den servern. |
| **+ Nytt projekt** | Ägare | Skapar ett nytt projekt. Se [skapa ett nytt projekt](#create-a-new-project). |
| **Ladda upp GitHub lagrings platsen** | Ägare | Importerar ett projekt från GitHub. [Importera ett projekt från GitHub](#import-a-project-from-github). |
| **Kopiera** | Personer | Kopierar ett markerat projekt till ditt eget konto. Du blir ombedd att logga in om du inte redan gjort det. Se [klona ett projekt](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Hämta en start-ikon

När du använder kommandot **dela** och väljer fliken **bädda in** , kan du kopiera antingen HTML-kod eller markdown som skapar en "starta antecknings bok"-märke:

![Starta antecknings bok märke](https://notebooks.azure.com/launch.png)

Om du inte har ett Azure Notebooks-projekt kan du skapa en länk som klonas från GitHub direkt med hjälp av följande mallar och ersätter rätt användar namn och databas namn:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

När du använder kommandot **+ nytt projekt** visas Azure Notebooks popup-fönstret **Skapa nytt projekt** . Ange följande information i den här popup-menyn och välj sedan **skapa**:

| Fält | Beskrivning |
| --- | --- |
| Projektnamn | Ett eget namn för ditt projekt som Azure Notebooks används för visning. Till exempel "mitt Notebook-projekt". |
| Projekt-ID | En anpassad identifierare som blir en del av den URL som du använder för att dela ett projekt (formuläret är `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Detta ID får endast använda bokstäver, siffror och bindestreck, får innehålla högst 30 tecken och får inte vara ett [reserverat projekt-ID](#reserved-project-ids). Om du är osäker på vad du ska använda, är en gemensam konvention att använda en gemen version av ditt projekt namn där blank steg är i bindestreck, till exempel "Min-anteckningsbok-Project" (trunkeras om det behövs för att få plats för längd begränsningen). |
| Offentligt | Om den här inställningen är aktive rad kan vem som helst med länken komma åt projektet. Ta bort det här alternativet när du skapar ett privat projekt. |
| Initiera projektet med ett viktigt | Om det här alternativet anges skapas en standard- *Readme.MD* -fil i projektet. I en *Readme.MD* -fil kan du ange dokumentation för ditt projekt om du vill. |

### <a name="reserved-project-ids"></a>Reserverade projekt-ID: n

Följande reserverade ord kan inte användas själva som projekt-ID. Dessa reserverade ord kan dock användas som en del av längre projekt-ID.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| tar | konto | Administrationsverktyg | api | blogg | klass rum |
| innehåll | instrumentpanel | upptäcka | Assurance | Hjälp | html |
| Bostad | export | Bibliotek | management | nytt | 1150 |
| antecknings böcker | PDF | förhandsversion | Priset | upphandlarprofil | Sök |
| status | Hjälp | test | | | |

Om du försöker använda något av dessa ord som projekt-ID visas popup-fönster för att **skapa nya projekt** -och **projekt inställningar** , "biblioteks-ID är en reserverad identifierare".

Eftersom ett projekt-ID också är en del av ett projekts webb adress kan Ad Blocker-program varan blockera användningen av vissa nyckelord, till exempel "annonser". I sådana fall använder du ett annat ord i projekt-ID: t.

## <a name="import-a-project-from-github"></a>Importera ett projekt från GitHub

Du kan enkelt importera en hel offentlig GitHub-lagrings platsen som ett projekt, inklusive data-och *Readme.MD* -filer. Använd kommandot **upload GitHub lagrings platsen** och ange följande information på popup-menyn och välj sedan **Importera**:

| Fält | Beskrivning |
| --- | --- |
| GitHub-lagringsplats | Namnet på käll lagrings platsen på github.com. Om du till exempel vill klona Jupyter-anteckningsböcker för Azure Cognitive Services på [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks), anger du "Microsoft/kognitiv-Services-Notebooks".  |
| Klona rekursivt | GitHub-databaser kan innehålla flera underordnade databaser. Ange det här alternativet om du vill klona den överordnade lagrings platsen och alla dess underordnade. Eftersom det är möjligt för en lagrings plats att ha många underordnade, låter du det här alternativet vara klart om du inte vet att du behöver den. |
| Projektnamn | Ett eget namn för ditt projekt som Azure Notebooks används för visning. |
| Projekt-ID | En anpassad identifierare som blir en del av den URL som du använder för att dela ett projekt (formuläret är `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Detta ID får endast använda bokstäver, siffror och bindestreck, får innehålla högst 30 tecken och får inte vara ett [reserverat projekt-ID](#reserved-project-ids). Om du är osäker på vad du ska använda, är en gemensam konvention att använda en gemen version av ditt projekt namn där blank steg är i bindestreck, till exempel "Min-anteckningsbok-Project" (trunkeras om det behövs för att få plats för längd begränsningen). |
| Offentligt | Om den här inställningen är aktive rad kan vem som helst med länken komma åt projektet. Ta bort det här alternativet när du skapar ett privat projekt. |

När du importerar en lagrings plats från GitHub importeras även historiken. Du kan använda standard git-kommandon från terminalen för att genomföra nya ändringar, Hämta ändringar från GitHub och så vidare.

## <a name="clone-a-project"></a>Klona ett projekt

Kloning skapar en kopia av ett befintligt projekt i ditt eget konto, där du sedan kan köra och ändra en antecknings bok eller annan fil i projektet. Du kan också använda kloning för att göra kopior av dina egna projekt där du gör experiment eller annat arbete utan att störa det ursprungliga projektet.

För att klona ett projekt:

1. På instrument panelen för **Mina projekt** högerklickar du på önskat projekt och väljer **kloning** (kortkommando: c).

    ![Klonings kommando i projektets snabb meny](media/clone-command.png)

1. I popup-fönstret **klona projekt** anger du ett namn och ID för klonen och anger om klonen är offentlig. De här inställningarna är desamma som för ett [nytt projekt](#create-a-new-project).

    ![Popup för klon projekt](media/clone-project.png)

1. När du har valt knappen **kloning** går Azure Notebooks direkt till kopian.

## <a name="next-steps"></a>Nästa steg

- [Utforska notebook-exempelfiler](azure-notebooks-samples.md)
- [Gör så här: Konfigurera och hantera projekt](configure-manage-azure-notebooks-projects.md)
- [Gör så här: installera paket inifrån en bärbar dator](install-packages-jupyter-notebook.md)
- [Så här gör du: Visa ett bild spel](present-jupyter-notebooks-slideshow.md)
- [Gör så här: arbeta med datafiler](work-with-project-data-files.md)
- [Så här gör du: åtkomst till data resurser](access-data-resources-jupyter-notebooks.md)
- [Gör så här: använda Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
