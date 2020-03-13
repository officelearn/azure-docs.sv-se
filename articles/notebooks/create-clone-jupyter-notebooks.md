---
title: Skapa och klona Jupyter-anteckningsböcker – Azure Notebooks för hands version
description: Azure Notebooks förhandsgranska projekt hanterar en samling antecknings böcker och relaterade filer, som du kan använda för att skapa nya eller klona från en annan källa.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: b29ff336c09a3bbf05a57c8a3a503b1875b76e54
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280578"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Skapa och klona projekt i Azure Notebooks för hands version

Azure Notebooks ordnar dina Jupyter-anteckningsböcker och relaterade filer i logiska grupper som kallas *projekt*. Du skapar ett projekt först som en behållare och sedan skapa eller klona en eller flera datorer i en mapp tillsammans med andra projektfiler. (Den här processen visas i [självstudien](tutorial-create-run-jupyter-notebook.md).)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Ett projekt har också enhetsspecifika metadata och andra konfigurationsinställningar som påverkar servern på vilken anteckningsböcker som körs, inklusive steg för anpassad konfiguration och installation av paket. Mer information finns i [Hantera och konfigurera projekt](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Använd instrumentpanelen för Mina projekt

På instrument panelen för **Mina projekt** i `https://notebooks.azure.com/<userID>/projects` kan du Visa, hantera och skapa projekt:

[![instrument panelen för mina projekt i Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Vad du kan göra på instrumentpanelen beror på om du har loggat in med kontot som äger det användar-ID:

| Kommando | Tillgänglig för | Beskrivning |
| --- | --- | --- |
| **Fungerar** | Ägare | Startar projektserver och öppnar projektmappen i Jupyter. (Vanligare, du navigerar till en projektmapp först och sedan starta en anteckningsbok därifrån.) |
| **Ladda ned** | Vem som helst | Laddar ned en kopia av det valda projektet som en ZIP-fil. |
| **Dela** | Vem som helst | Visar popup-fönstret för delning som du kan använda för att hämta en URL till ett valt projekt, dela till sociala medier, skicka ett e-postmeddelande med URL: en och hämta både HTML-eller markdown-kod för med ett "lansera antecknings bok"-märke (se [Hämta en start-märke](#obtain-a-launch-badge)) med URL: en. |
| **Ta bort** | Ägare | Tar bort det valda projektet. Du kan inte ångra den här åtgärden. |
| **Terminal** | Ägare | Startar projektserver, och sedan öppnas ett nytt webbläsarfönster med bash terminal för den här servern. |
| **+ Nytt projekt** | Ägare | Skapar ett nytt projekt. Se [skapa ett nytt projekt](#create-a-new-project). |
| **Ladda upp GitHub lagrings platsen** | Ägare | Importerar en projektet från GitHub. [Importera ett projekt från GitHub](#import-a-project-from-github). |
| **Kopiera** | Vem som helst | Kopierar ett valt projekt till ditt eget konto. Du uppmanas att logga in om den inte redan. Se [klona ett projekt](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Hämta en start-märket

När du använder kommandot **dela** och väljer fliken **bädda in** , kan du kopiera antingen HTML-kod eller markdown som skapar en "starta antecknings bok"-märke:

![Starta notebook märket](https://notebooks.azure.com/launch.png)

Om du inte har ett projekt med Azure-datorer kan skapa du en länk som klonar från GitHub direkt med hjälp av följande mallar, och Ersätt rätt användarnamn och databasnamn:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

När du använder kommandot **+ nytt projekt** visas Azure Notebooks popup-fönstret **Skapa nytt projekt** . Ange följande information i den här popup-menyn och välj sedan **skapa**:

| Field | Beskrivning |
| --- | --- |
| Projektnamn | Ett eget namn för ditt projekt som Azure-datorer använder för visning. Till exempel "mitt Notebook-projekt". |
| Projekt-ID | En anpassad identifierare som blir en del av den URL som du använder för att dela ett projekt (formuläret är `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Detta ID får endast använda bokstäver, siffror och bindestreck, får innehålla högst 30 tecken och får inte vara ett [reserverat projekt-ID](#reserved-project-ids). Om du är osäker vad du använder, är en gemensam konvention att använda en gemen version av ditt projektnamn där blanksteg är aktiverade i bindestreck, till exempel ”min-notebook-projekt” (trunkerad vid behov för att passa längdbegränsningen). |
| Offentlig | Om ange, kan vem som helst med en länk till projektet. När du skapar ett privat projekt kan du avmarkera det här alternativet. |
| Initiera det här projektet med ett viktigt | Om det här alternativet anges skapas en standard- *Readme.MD* -fil i projektet. I en *Readme.MD* -fil kan du ange dokumentation för ditt projekt om du vill. |

### <a name="reserved-project-ids"></a>Reserverade projekt-ID: n

Följande reserverade ord kan inte användas själva som projekt-ID. Dessa reserverade ord kan dock användas som en del av längre projekt-ID.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| tar | konto | administration | api | blogg | klass rum |
| innehåll | instrumentpanel | upptäcka | Assurance | hjälp | - |
| start | export | bibliotek | management | Nyårs | 1150 |
| notebooks | PDF | förhandsversion | priset | profile | Sök |
| status | support | test | | | |

Om du försöker använda något av dessa ord som projekt-ID visas popup-fönster för att **skapa nya projekt** -och **projekt inställningar** , "biblioteks-ID är en reserverad identifierare".

Eftersom ett projekt-ID också är en del av ett projekts webb adress kan Ad Blocker-program varan blockera användningen av vissa nyckelord, till exempel "annonser". I sådana fall använder du ett annat ord i projekt-ID: t.

## <a name="import-a-project-from-github"></a>Importera ett projekt från GitHub

Du kan enkelt importera en hel offentlig GitHub-lagrings platsen som ett projekt, inklusive data-och *Readme.MD* -filer. Använd kommandot **upload GitHub lagrings platsen** och ange följande information på popup-menyn och välj sedan **Importera**:

| Field | Beskrivning |
| --- | --- |
| GitHub-lagringsplats | Namnet på källdatabasen på github.com. Om du till exempel vill klona Jupyter-anteckningsböcker för Azure Cognitive Services på [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks), anger du "Microsoft/kognitiv-Services-Notebooks".  |
| Klona rekursivt | GitHub-lagringsplatser kan innehålla flera underordnade databaser. Ange det här alternativet om du vill klona överordnade databasen och alla dess underordnade. Eftersom det är möjligt för en databas har många underordnade låter du avmarkera alternativet om du inte vet du behöver den. |
| Projektnamn | Ett eget namn för ditt projekt som Azure-datorer använder för visning. |
| Projekt-ID | En anpassad identifierare som blir en del av den URL som du använder för att dela ett projekt (formuläret är `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Detta ID får endast använda bokstäver, siffror och bindestreck, får innehålla högst 30 tecken och får inte vara ett [reserverat projekt-ID](#reserved-project-ids). Om du är osäker vad du använder, är en gemensam konvention att använda en gemen version av ditt projektnamn där blanksteg är aktiverade i bindestreck, till exempel ”min-notebook-projekt” (trunkerad vid behov för att passa längdbegränsningen). |
| Offentlig | Om ange, kan vem som helst med en länk till projektet. När du skapar ett privat projekt kan du avmarkera det här alternativet. |

Importera en databas från GitHub importerar även dess historik. Du kan använda standard Git-kommandon från terminalen för att genomföra nya ändringar, hämta ändringarna från GitHub och så vidare.

## <a name="clone-a-project"></a>Klona ett projekt

Kloning av skapar en kopia av ett befintligt projekt i ditt eget konto där du kan köra och ändra alla olika bärbara datorer eller andra filen i projektet. Du kan också använda kloning för att göra kopior av dina egna projekt som du använder för att göra till experiment eller annat arbete utan att störa det ursprungliga projektet.

Att klona ett projekt:

1. På instrument panelen för **Mina projekt** högerklickar du på önskat projekt och väljer **kloning** (kortkommando: c).

    ![Kloningskommandot på snabbmenyn för projektet](media/clone-command.png)

1. I popup-fönstret **klona projekt** anger du ett namn och ID för klonen och anger om klonen är offentlig. De här inställningarna är desamma som för ett [nytt projekt](#create-a-new-project).

    ![Klona projektet popup-fönstret](media/clone-project.png)

1. När du har valt knappen **kloning** går Azure Notebooks direkt till kopian.

## <a name="next-steps"></a>Nästa steg

- [Utforska notebook-exempelfiler](azure-notebooks-samples.md)
- [Gör så här: Konfigurera och hantera projekt](configure-manage-azure-notebooks-projects.md)
- [Gör så här: installera paket inifrån en bärbar dator](install-packages-jupyter-notebook.md)
- [Så här gör du: Visa ett bild spel](present-jupyter-notebooks-slideshow.md)
- [Gör så här: arbeta med datafiler](work-with-project-data-files.md)
- [Så här gör du: åtkomst till data resurser](access-data-resources-jupyter-notebooks.md)
- [Gör så här: använda Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
