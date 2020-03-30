---
title: Skapa och klona Jupyter-anteckningsböcker - Förhandsversionen av Azure-anteckningsböcker
description: Förhandsversionsprojekt för Azure Notebooks hanterar en samling anteckningsböcker och relaterade filer som du kan skapa nya eller klona från en annan källa.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: b29ff336c09a3bbf05a57c8a3a503b1875b76e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280578"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Skapa och klona projekt i förhandsversionen av Azure Notebooks

Azure Notebooks organiserar dina Jupyter-anteckningsböcker och relaterade filer i logiska grupper som kallas *projekt*. Du skapar ett projekt först som en behållare och skapar eller klonar sedan en eller flera anteckningsböcker i en mapp tillsammans med andra projektfiler. (Den här processen visas i [självstudien](tutorial-create-run-jupyter-notebook.md).)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Ett projekt underhåller också metadata och andra konfigurationsinställningar som påverkar servern där anteckningsböcker körs, inklusive anpassade installationssteg och paketinstallation. Mer information finns i [Hantera och konfigurera projekt](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Använda instrumentpanelen Mina projekt

Instrumentpanelen Mina `https://notebooks.azure.com/<userID>/projects` **projekt** är där du visar, hanterar och skapar projekt:

[![Instrumentpanelen Mina projekt i Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Vad du kan göra på instrumentpanelen beror på om du är inloggad med kontot som äger användar-ID:

| Kommando | Tillgänglig för | Beskrivning |
| --- | --- | --- |
| **Köra** | Ägare | Startar projektservern och öppnar projektmappen i Jupyter. (Du kan först navigera till en projektmapp och sedan starta en anteckningsbok därifrån.) |
| **Ladda ned** | Vem som helst | Hämtar en kopia av det valda projektet som en ZIP-fil. |
| **Dela** | Vem som helst | Visar delningspopupen genom vilken du kan få en webbadress till ett valt projekt, dela till sociala medier, skicka ett e-postmeddelande med webbadressen och hämta både HTML- eller Markdown-kod för med ett "launch notebook"-märke (se [hämta ett startmärke)](#obtain-a-launch-badge)med webbadressen. |
| **Ta bort** | Ägare | Tar bort det markerade projektet. Du kan inte ångra den här åtgärden. |
| **Terminal** | Ägare | Startar projektservern och öppnar sedan ett nytt webbläsarfönster med bash-terminalen för den servern. |
| **+ Nytt projekt** | Ägare | Skapar ett nytt projekt. Se [Skapa ett nytt projekt](#create-a-new-project). |
| **Ladda upp GitHub Repo** | Ägare | Importerar ett projekt från GitHub. [Importera ett projekt från GitHub](#import-a-project-from-github). |
| **Klona** | Vem som helst | Kopierar ett valt projekt till ditt eget konto. Uppmanar dig att logga in om inte redan. Se [Klona ett projekt](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Skaffa ett startmärke

När du använder kommandot **Dela** och väljer fliken **Bädda in** kan du kopiera antingen HTML-kod eller Markdown som skapar ett "launch notebook"-märke:

![Starta anteckningsmärke](https://notebooks.azure.com/launch.png)

Om du inte har ett Azure Notebooks-projekt kan du skapa en länk som klonar från GitHub direkt med hjälp av följande mallar och ersätter lämpliga användarnamn och databasnamn:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

När du använder kommandot **+ Nytt projekt** visar Azure Notebooks en **popup-fönster skapa nytt projekt.** I det här popup-fönstret anger du följande information och väljer sedan **Skapa:**

| Field | Beskrivning |
| --- | --- |
| Projektnamn | Ett eget namn för ditt projekt som Azure Notebooks använder för visningsändamål. Till exempel "Mitt anteckningsboksprojekt". |
| Projekt-ID | En anpassad identifierare som blir en del av webbadressen `https://notebooks.azure.com/<user_id>/projects/<project_id>`som du använder för att dela ett projekt (formuläret är ). Det här ID:t kan bara använda bokstäver, siffror och bindestreck, är begränsat till 30 tecken och kan inte vara ett [reserverat projekt-ID](#reserved-project-ids). Om du är osäker på vad du ska använda är en gemensam konvention att använda en gemen version av projektnamnet där blanksteg omvandlas till bindestreck, till exempel "min-notebook-project" (trunkeras om det behövs för att passa längdgränsen). |
| Offentlig | Om den är inställd kan alla som har länken komma åt projektet. När du skapar ett privat projekt avmarkerar du det här alternativet. |
| Initiera detta projekt med en README | Om den är inställd skapar du en *standardfil README.md* i projektet. En *README.md* fil är där du tillhandahåller dokumentation för ditt projekt, om så önskas. |

### <a name="reserved-project-ids"></a>Reserverade projekt-ID:er

Följande reserverade ord kan inte användas av dem själva som projekt-ID: er. Dessa reserverade ord kan dock användas som en del av längre projekt-ID.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| om | konto | administration | api | blogg | klassrum |
| innehåll | instrumentpanel | utforska | Faq | Hjälp | html |
| start | importera | Bibliotek | management | ny | Anteckningsboken |
| notebooks | pdf | förhandsgranska | Prissättning | profil | sök |
| status | support | test | | | |

Om du försöker använda något av dessa ord som ett projekt-ID visas **popup-fönster** för Skapa nytt projekt och **Projektinställningar:** "Biblioteks-ID är en reserverad identifierare".

Eftersom ett projekt-ID också är en del av ett projekts URL kan annonsblockeringsprogram blockera användningen av vissa sökord, till exempel "annons". I sådana fall använder du ett annat ord i projekt-ID:t.

## <a name="import-a-project-from-github"></a>Importera ett projekt från GitHub

Du kan enkelt importera en hel offentlig GitHub-repo som ett projekt, inklusive data och *README.md* filer. Använd kommandot **Ladda upp GitHub Repo,** ange följande information i popup-fönstret och välj sedan **Importera:**

| Field | Beskrivning |
| --- | --- |
| GitHub-lagringsplats | Namnet på källdatabasen på github.com. Om du till exempel vill klona Jupyter-anteckningsböckerna för Azure Cognitive Services på [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)anger du "Microsoft/cognitive-services-notebooks".  |
| Klona rekursivt | GitHub-databaser kan innehålla flera underordnade databaser. Ange det här alternativet om du vill klona den överordnade databasen och alla dess underordnade. Eftersom det är möjligt för en databas att ha många underordnade, lämna det här alternativet klart om du inte vet att du behöver det. |
| Projektnamn | Ett eget namn för ditt projekt som Azure Notebooks använder för visningsändamål. |
| Projekt-ID | En anpassad identifierare som blir en del av webbadressen `https://notebooks.azure.com/<user_id>/projects/<project_id>`som du använder för att dela ett projekt (formuläret är ). Det här ID:t kan bara använda bokstäver, siffror och bindestreck, är begränsat till 30 tecken och kan inte vara ett [reserverat projekt-ID](#reserved-project-ids). Om du är osäker på vad du ska använda är en gemensam konvention att använda en gemen version av projektnamnet där blanksteg omvandlas till bindestreck, till exempel "min-notebook-project" (trunkeras om det behövs för att passa längdgränsen). |
| Offentlig | Om den är inställd kan alla som har länken komma åt projektet. När du skapar ett privat projekt avmarkerar du det här alternativet. |

Om du importerar en databas från GitHub importeras också dess historik. Du kan använda vanliga Git-kommandon från terminalen för att genomföra nya ändringar, hämta ändringar från GitHub och så vidare.

## <a name="clone-a-project"></a>Klona ett projekt

Kloning skapar en kopia av ett befintligt projekt i ditt eget konto, där du sedan kan köra och ändra en anteckningsbok eller annan fil i projektet. Du kan också använda kloning för att göra kopior av dina egna projekt där du gör experiment eller annat arbete utan att störa det ursprungliga projektet.

Så här klonar du ett projekt:

1. Högerklicka på önskat projekt på instrumentpanelen **Mina projekt** och välj **Klon (kortkommando:** c).

    ![Klona kommandot på projektets snabbmeny](media/clone-command.png)

1. I **popup-fönstret Klona projekt** anger du ett namn och ID för klonen och anger om klonen är offentlig. Dessa inställningar är desamma som för ett [nytt projekt](#create-a-new-project).

    ![Klona projekt popup](media/clone-project.png)

1. När du har valt **kloningsknappen** navigerar Azure Notebooks direkt till kopian.

## <a name="next-steps"></a>Nästa steg

- [Utforska notebook-exempelfiler](azure-notebooks-samples.md)
- [Så här konfigurerar och hanterar du projekt](configure-manage-azure-notebooks-projects.md)
- [Så här installerar du paket från en bärbar dator](install-packages-jupyter-notebook.md)
- [Så här presenterar du ett bildspel](present-jupyter-notebooks-slideshow.md)
- [Så här: Arbeta med datafiler](work-with-project-data-files.md)
- [Så här kommer du åt åtkomst till dataresurser](access-data-resources-jupyter-notebooks.md)
- [Så här använder du Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
