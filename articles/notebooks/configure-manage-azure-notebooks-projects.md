---
title: Konfigurera och hantera förhandsversionen av Azure-anteckningsböcker
description: Lär dig hur du hanterar projektmetadata, projektfiler, projektets miljö och installationssteg via både Azure Notebooks-användargränssnittet och direkt terminalåtkomst.
ms.topic: how-to
ms.date: 02/28/2020
ms.openlocfilehash: 1674effda2cb9bda45f49c91ca618225b0a75f0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280604"
---
# <a name="manage-and-configure-projects-in-azure-notebooks-preview"></a><a id="manage-and-configure-projects" />Hantera och konfigurera projekt i förhandsversionen av Azure Notebooks

Ett projekt i förhandsversionen av Azure Notebooks är i huvudsak en konfiguration av den underliggande virtuella Linux-datorn där Jupyter-anteckningsböcker körs, tillsammans med en filmapp och beskrivande metadata. 

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Med projektinstrumentpanelen i Azure Notebooks kan du hantera filer och på annat sätt konfigurera projektets egenskaper:

- Beräkningsnivån som projektet körs på, vilket kan vara den kostnadsfria nivån eller en virtuell Azure-dator.
- Projektmetadata, som innehåller ett namn, en beskrivning, en identifierare som används när projektet delas och om projektet är offentligt eller privat.
- Projektets anteckningsbok, data och andra filer, som du hanterar som alla andra filsystem.
- Ett projekts miljö, som du hanterar antingen genom startskript eller direkt via terminalen.
- Loggar, som du kommer åt via terminalen.

> [!Note]
> Hanterings- och konfigurationsfunktionerna som beskrivs här är endast tillgängliga för projektägaren som skapade projektet från början. Du kan dock klona projektet till ditt eget konto, i vilket fall du blir ägare och kan konfigurera projektet som önskat.

Azure Notebooks startar den underliggande virtuella datorn när du kör en anteckningsbok eller annan fil. Servern sparar automatiskt filer och stängs av efter 60 minuters inaktivitet. Du kan också stoppa servern när som helst med kommandot **Avstängning** (kortkommando: h).

## <a name="compute-tier"></a>Beräkningsnivå

Som standard körs projekt på den **kostnadsfria** beräkningsnivån, som är begränsad till 4 GB minne och 1 GB data för att förhindra missbruk. Du kan kringgå dessa begränsningar och öka beräkningskraften med hjälp av en annan virtuell dator som du har etablerat i en Azure-prenumeration. Mer information finns i [Så här använder du virtuella data science-datorer](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Redigera projektmetadata

På instrumentpanelen i projektet väljer du **Projektinställningar**och väljer sedan fliken **Information** som innehåller projektets metadata enligt beskrivningen i följande tabell. Du kan ändra projektmetadata när som helst.

| Inställning | Beskrivning |
| --- | --- |
| Projektnamn | Ett eget namn för ditt projekt som Azure Notebooks använder för visningsändamål. Till exempel "Hello World in Python". |
| Projekt-ID | En anpassad identifierare som blir en del av webbadressen som du använder för att dela ett projekt. Det här ID:t kan bara använda bokstäver, siffror och bindestreck, är begränsat till 30 tecken och kan inte vara ett [reserverat projekt-ID](create-clone-jupyter-notebooks.md#reserved-project-ids). Om du är osäker på vad du ska använda är en gemensam konvention att använda en gemen version av projektnamnet där blanksteg omvandlas till bindestreck, till exempel "min-notebook-project" (trunkeras om det behövs för att passa längdgränsen). |
| Offentligt projekt | Om den är inställd kan alla som har länken komma åt projektet. När du skapar ett privat projekt avmarkerar du det här alternativet. |
| Dölj kloner | Om den är inställd kan andra användare inte se en lista över kloner som har gjorts för det här projektet. Att dölja kloner är användbart för projekt som delas med många personer som inte ingår i samma organisation, till exempel när du använder en anteckningsbok för att undervisa en klass. |

> [!Important]
>
> Om du ändrar projekt-ID:et ogiltigförklaras alla länkar till projektet som du kan ha delat tidigare.

## <a name="manage-project-files"></a>Hantera projektfiler

Projektinstrumentpanelen visar innehållet i projektets mappsystem. Du kan använda olika kommandon för att hantera dessa filer.

### <a name="create-new-files-and-folders"></a>Skapa nya filer och mappar

Kommandot **+ Nytt** (kortkommando: n) skapar nya filer eller mappar. När du använder kommandot väljer du först den typ av objekt som ska skapas:

| Typ av artikel | Beskrivning | Kommandobeteende |
| --- | --- | --- |
| **Notebook-fil** | En Jupyter anteckningsbok | Visar ett popup-fönster där du anger anteckningsbokens filnamn och språk. |
| **Mapp** | En undermapp | Skapar ett redigeringsfält i projektets fillista där du anger mappnamnet. |
| **Tom fil** | En fil där du kan lagra innehåll som text, data, etc. | Skapar ett redigeringsfält i projektets fillista där du anger filnamnet. |
| **Markdown** | En Markdown-fil. | Skapar ett redigeringsfält i projektets fillista där du anger filnamnet. |

### <a name="upload-files"></a>Överföra filer

Kommandot **Ladda upp** innehåller två alternativ för att importera data från andra platser: Från **URL** och Från **dator**. Mer information finns [i Arbeta med datafiler i Azure Notebook-projekt](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Välj filspecifika kommandon

Varje objekt i projektets fillista innehåller kommandon via en snabbmeny för högerklickning:

![Kommandon på en snabbmeny för filer](media/project-file-commands.png)

| Kommando | Kortkommando | Åtgärd |
| --- | --- | --- |
| Kör | r (eller klicka) | Kör en anteckningsboksfil. Andra filtyper öppnas för visning.  |
| Kopiera länk | y | Kopierar en länk till filen till Urklipp. |
| Kör i Jupyter Lab | J | Kör en anteckningsbok i JupyterLab, som är ett mer utvecklarorienterat gränssnitt än Jupyter normalt ger. |
| Förhandsversion | P | Öppnar en HTML-förhandsgranskning av filen. För anteckningsböcker är förhandsgranskningen en skrivskyddad återgivning av anteckningsboken. Mer information finns i avsnittet [Förhandsgranska.](#preview) |
| Redigera fil | i | Öppnar filen för redigering. |
| Ladda ned | d | Hämtar en zip-fil som innehåller filen eller innehållet i en mapp. |
| Byt namn | a | Frågar efter ett nytt namn för filen eller mappen. |
| Ta bort | x | Frågar efter bekräftelse och tar sedan bort filen permanent från projektet. Det går inte att ångra borttagningar. |
| Flytta | m | Flyttar en fil till en annan mapp i samma projekt. |

#### <a name="preview"></a>Förhandsversion

En förhandsgranskning av en fil eller anteckningsbok är en skrivskyddad vy av innehållet. att köra anteckningsceller är inaktiverat. En förhandsgranskning visas för alla som har en länk till filen eller anteckningsboken men som inte har loggat in på Azure Notebooks. När en användare har loggat in kan han eller hon klona anteckningsboken till sitt eget konto eller hämta anteckningsboken till den lokala datorn.

Förhandsgranskningssidan stöder flera kommandon i verktygsfältet med kortkommandon:

| Kommando | Kortkommando | Åtgärd |
| --- | --- | --- |
| Dela | s | Visar delningspopta som du kan få en länk från, dela till sociala medier, hämta HTML för inbäddning och skicka ett e-postmeddelande. |
| Klona | c  | Klona anteckningsboken till ditt konto. |
| Kör | r | Kör anteckningsboken om du får göra det. |
| Ladda ned | d | Hämtar en kopia av anteckningsboken. |

## <a name="configure-the-project-environment"></a>Konfigurera projektmiljön

Det finns tre sätt att konfigurera miljön för den underliggande virtuella datorn där dina anteckningsböcker körs:

- Inkludera ett engångsiniteringsskript
- Använd projektets miljöinställningar för att ange inställningssteg
- Öppna den virtuella datorn via en terminal.

Alla former av projektkonfiguration tillämpas när den virtuella datorn startas och påverkar därmed alla anteckningsböcker i projektet.

### <a name="one-time-initialization-script"></a>Skript för initiering i en gång

Första gången Azure Notebooks skapar en server för projektet, söker den efter en fil i projektet som kallas *aznbsetup.sh*. Om den här filen finns kör Azure Notebooks den. Utdata för skriptet lagras i projektmappen som *.aznbsetup.log*.

### <a name="environment-setup-steps"></a>Steg för miljöinställningar

Du kan använda projektets miljöinställningar för att skapa enskilda steg som konfigurerar miljön.

På instrumentpanelen i projektet väljer du **Projektinställningar**och väljer sedan fliken **Miljö** där du lägger till, tar bort och ändrar inställningssteg för projektet:

![Popup-popup för projektinställningar med fliken Miljö markerad](media/project-settings-environment-steps.png)

Om du vill lägga till ett steg väljer du först **+ Lägg till**och väljer sedan en stegtyp i listrutan **Operation:**

![Åtgärdsväljare för ett nytt miljöinställningssteg](media/project-settings-environment-details.png)

Vilken information du sedan projicerar beror på vilken typ av åtgärd du har valt:

- **Requirements.txt**: I den andra listrutan väljer du en *requirements.txt-fil* som redan finns i projektet. Välj sedan en Python-version från den tredje listrutan som visas. Genom att använda en *requirements.txt-fil* körs `pip install -r` Azure Notebooks med *filen requirements.txt* när du startar en anteckningsboksserver. Du behöver inte uttryckligen installera paket från själva anteckningsboken.

- **Shell-skript:** I den andra listrutan väljer du ett bash shell-skript i projektet (vanligtvis en fil med *tillägget .sh)* som innehåller alla kommandon som du vill köra för att initiera miljön.

- **Environment.yml**: I den andra listrutan väljer du en *environments.yml-fil* för Python-projekt med hjälp av en conda-miljö.

   > [!WARNING]
   > Eftersom det här är en förhandsversionstjänst under `Environment.yml` utveckling finns det för närvarande ett känt problem där inställningen inte tillämpas på projektet som förväntat. Projektet och Jupyter-anteckningsböckerna inom läser för närvarande inte in den angivna miljöfilen.

När du är klar med att lägga till steg väljer du **Spara**.

### <a name="use-the-terminal"></a>Använd terminalen

På projektinstrumentpanelen öppnar kommandot **Terminal** en Linux-terminal som ger dig direkt åtkomst till servern. Inom terminalen kan du ladda ner data, redigera eller hantera filer, inspektera processer och till och med använda verktyg som vi och nano.

> [!Note]
> Om du har startskript i projektets miljö kan det hända att det fortfarande pågår ett meddelande om att installationen fortfarande pågår om du öppnar terminalen.

Du kan utfärda alla vanliga Linux-kommandon i terminalen. Du kan `ls` också använda i hemmappen för att se de olika miljöer som finns på den virtuella datorn, till exempel *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*och *R*, tillsammans med en *projektmapp* som innehåller projektet:

![Projektterminal i Azure-anteckningsböcker](media/project-terminal.png)

Om du vill påverka en viss miljö ändrar du katalogerna till den miljömappen först.

För Python-miljöer kan du `pip` `conda` hitta och i *lagerplatsmappen* för varje miljö. Du kan också använda inbyggda alias för miljöerna:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Ändringar som görs på servern gäller endast för den aktuella sessionen, med undantag för filer och mappar som du skapar i själva *projektmappen.* Det sparas till exempel att redigera en fil i projektmappen mellan sessioner, men paket med `pip install` är det inte.

> [!Note]
> Om du `python` `python3`använder eller anropar du de systeminstallerade versionerna av Python, som inte används för bärbara datorer. Du har inte behörigheter för `pip install` åtgärder som antingen, så se till att använda de versionsspecifika aliasen.

## <a name="access-notebook-logs"></a>Loggar för accessanteckningsböcker

Om du stöter på problem när du kör en anteckningsbok lagras utdata från Jupyter i en mapp med namnet *.nb.log*. Du kan komma åt dessa loggar via kommandot **Terminal** eller projektinstrumentpanelen.

Vanligtvis när du kör Jupyter lokalt kan du ha startat den från ett terminalfönster. Terminalfönstret visar utdata som kernel-status.

Om du vill visa loggar använder du följande kommando i terminalen:

```bash
cat .nb.log
```

Du kan också använda kommandot från en kodcell i en Python-anteckningsbok:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Nästa steg

- [Så här: Arbeta med projektdatafiler](work-with-project-data-files.md)
- [Komma åt molndata i en notebook-fil](access-data-resources-jupyter-notebooks.md)
