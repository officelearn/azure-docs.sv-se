---
title: Konfigurera och hantera Azure Notebooks för hands version
description: Lär dig hur du hanterar projekt-metadata, projektfiler, projektets miljö och installations steg via både Azure Notebooks användar gränssnitt och direkt åtkomst till terminalen.
ms.topic: how-to
ms.date: 02/28/2020
ms.openlocfilehash: 1674effda2cb9bda45f49c91ca618225b0a75f0c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360663"
---
# <a name="a-idmanage-and-configure-projects--manage-and-configure-projects-in-azure-notebooks-preview"></a><a id="manage-and-configure-projects" /> hantera och konfigurera projekt i Azure Notebooks för hands version

Ett projekt i Azure Notebooks för hands versionen är i grunden en konfiguration av den underliggande virtuella Linux-datorn där Jupyter-anteckningsböcker körs, tillsammans med en filmapp och beskrivande metadata. 

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Instrumentpanelen för projektet i Azure-datorer kan du hantera filer och konfigurera projektets egenskaper:

- Beräknings nivån som projektet körs på, vilket kan vara den kostnads fria nivån eller en virtuell Azure-dator.
- Project-metadata, som innehåller ett namn, en beskrivning, en identifierare som används vid delning av projektet och om projektet är offentligt eller privat.
- Projektets bärbara dator, data och andra filer som du hanterar precis som andra fil system.
- Ett projekts miljö, som du hanterar antingen via Start skript eller direkt via terminalen.
- Loggar, som du kommer åt via terminalen.

> [!Note]
> De hanterings-och konfigurations funktioner som beskrivs här är endast tillgängliga för den projekt ägare som skapade projektet från början. Du kan dock klona projektet till ditt eget konto, i vilket fall du blir ägare och kan konfigurera projektet efter behov.

Azure-datorer startar den underliggande virtuella datorn när du kör en bärbar dator eller en annan fil. Servern sparar filer automatiskt och stängs av efter 60 minuters inaktivitet. Du kan också stoppa servern när som helst med kommandot **shutdown** (kortkommando: h).

## <a name="compute-tier"></a>Beräknings nivå

Som standard körs projekt på den **kostnads fria beräknings** nivån, som är begränsad till 4 GB minne och 1 GB data för att förhindra missbruk. Du kan kringgå de här begränsningarna och öka beräknings kraften genom att använda en annan virtuell dator som du har etablerad i en Azure-prenumeration. Mer information finns i [så här använder du data vetenskap Virtual Machines](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Redigera projekt metadata

På instrument panelen för projektet väljer du **projekt inställningar**och väljer sedan fliken **information** som innehåller projektets metadata enligt beskrivningen i följande tabell. Du kan ändra projekt metadata när som helst.

| Inställning | Beskrivning |
| --- | --- |
| Projektnamn | Ett eget namn för ditt projekt som Azure-datorer använder för visning. Till exempel ”Hello World i Python”. |
| Projekt-ID | En anpassad identifierare som blir en del av URL: en som du använder för att dela ett projekt. Detta ID får endast använda bokstäver, siffror och bindestreck, får innehålla högst 30 tecken och får inte vara ett [reserverat projekt-ID](create-clone-jupyter-notebooks.md#reserved-project-ids). Om du är osäker vad du använder, är en gemensam konvention att använda en gemen version av ditt projektnamn där blanksteg är aktiverade i bindestreck, till exempel ”min-notebook-projekt” (trunkerad vid behov för att passa längdbegränsningen). |
| Offentliga projekt | Om ange, kan vem som helst med en länk till projektet. När du skapar ett privat projekt kan du avmarkera det här alternativet. |
| Dölj kloner | Om du anger, andra användare inte kan se en lista över kloner som har gjorts för det här projektet. Dölja kloner är användbart för projekt som delas med många personer som inte tillhör samma organisation, till exempel när du använder en bärbar dator för undervisar en klass. |

> [!Important]
>
> Ändra projekt-ID upphäver alla länkar i projektet som du kanske har delat tidigare.

## <a name="manage-project-files"></a>Hantera projektfiler

Innehållet i projektets mappsystem visas i instrumentpanelen för projektet. Du kan använda olika kommandon för att hantera dessa filer.

### <a name="create-new-files-and-folders"></a>Skapa nya filer och mappar

Kommandot **+ New** (kortkommando: n) skapar nya filer eller mappar. När du använder kommandot måste du först välja vilken typ av objekt som ska skapas:

| Objekttyp | Beskrivning | Kommandobeteendet |
| --- | --- | --- |
| **1150** | En Jupyter-anteckningsbok | Visar ett fönster där du anger denna notebook filnamnet och språk. |
| **Mapp** | En undermapp | Skapar ett redigerbart fält i projektets fillistan som du anger namnet på mappen. |
| **Tom fil** | En fil som du kan lagra innehåll, till exempel text, data, osv. | Skapar ett redigerbart fält i projektets fillistan som du anger namnet på filen. |
| **Markdown** | En markdownfil. | Skapar ett redigerbart fält i projektets fillistan som du anger namnet på filen. |

### <a name="upload-files"></a>Överföra filer

**Uppladdnings** kommandot innehåller två alternativ för att importera data från andra platser: **från URL** och **från dator**. Mer information finns i [arbeta med datafiler i Azure Notebook-projekt](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Välj fil-fil

Varje objekt i listan över filer i projektets innehåller kommandon via en snabbmenyn:

![Kommandon på en snabbmeny för fil](media/project-file-commands.png)

| Kommando | Kortkommando | Åtgärd |
| --- | --- | --- |
| Kör | r (eller klicka på) | Kör en notebook-fil. Andra filtyper öppnas för visning.  |
| Kopiera länk | Y | Kopierar en länk till filen till Urklipp. |
| Kör i Jupyter-labb | J | Kör en anteckningsbok i JupyterLab, vilket är ett mer utvecklarorienterade gränssnitt än Jupyter ger normalt. |
| Förhandsversion | p | Öppnar en HTML-förhandsgranskning av filen. för bärbara datorer är förhandsgranskningen en skrivskyddad återgivningen av anteckningsboken. Mer information finns i avsnittet för [hands version](#preview) . |
| Redigera filen | Jag | Filen öppnas för redigering. |
| Ladda ned | d | Laddar ned en zip-fil som innehåller filen eller innehållet i en mapp. |
| Byt namn | a | Frågar efter ett nytt namn för filen eller mappen. |
| Ta bort | x | Uppmanas att bekräfta och sedan tar permanent bort filen från projektet. Borttagningar kan inte ångras. |
| Flytta | m | Flyttar en fil till en annan mapp i samma projekt. |

#### <a name="preview"></a>Förhandsversion

En förhandsversion av en fil eller en bärbar dator är en skrivskyddad vy av innehållet. köra anteckningsboken celler är inaktiverad. En förhandsgranskning visas för alla som har en länk till filen eller bärbar dator, men inte har loggat in Azure-anteckningsböcker. När du har loggat in, en användare kan klona anteckningsboken på sina egna konton, eller de kan ladda ned den bärbara datorn till den lokala datorn.

Förhandsgranskningssidan har stöd för flera verktygskommandon med kortkommandon:

| Kommando | Kortkommando | Åtgärd |
| --- | --- | --- |
| Resurs | s | Visar delningsapplikationen popup-fönstret som du kan få en länk, dela till sociala medier, hämta HTML för att bädda in och skicka ett e-postmeddelande. |
| Klona | c  | Klona anteckningsboken för att ditt konto. |
| Kör | R | Körs den bärbara datorn om du har behörighet att göra detta. |
| Ladda ned | d | Laddar ned en kopia av den bärbara datorn. |

## <a name="configure-the-project-environment"></a>Konfigurera projektet

Det finns tre sätt att konfigurera miljön för den underliggande virtuella datorn där dina anteckningsböcker körs:

- Inkludera en enstaka Initieringsskript
- Använd Projektinställningar miljö för att ange steg för konfiguration
- Åtkomst till den virtuella datorn via en terminal.

Alla former av projektkonfigurationen tillämpas när den virtuella datorn har startats och därmed påverkar alla anteckningsböcker i projektet.

### <a name="one-time-initialization-script"></a>Initieringsskript för enstaka

Första gången Azure Notebooks skapar en server för projektet söker den efter en fil i projektet som heter *aznbsetup.sh*. Om den här filen finns körs Azure Notebooks. Utdata från skriptet lagras i projektmappen som *. aznbsetup. log*.

### <a name="environment-setup-steps"></a>Steg för konfiguration av miljö

Du kan använda i projektets miljöinställningar för att skapa enskilda steg som konfigurerar miljön.

På instrument panelen för projektet väljer du **projekt inställningar**och väljer sedan fliken **miljö** där du lägger till, tar bort och ändrar installations stegen för projektet:

![Projektet inställningar popup-fönstret med fliken miljö valt](media/project-settings-environment-steps.png)

Om du vill lägga till ett steg väljer du **+ Lägg till**och väljer sedan en stegtyp i list rutan **åtgärd** :

![Åtgärden väljaren för en ny miljö installationssteget](media/project-settings-environment-details.png)

Vilken information som du sedan projicera beror på vilken typ av åtgärd som du har valt:

- **Krav. txt**: i den andra List rutan väljer du en fil med *kraven. txt* som redan finns i projektet. Välj sedan en Python-version från den tredje listan som visas. Genom att använda en *Requirements. txt* -fil körs Azure Notebooks `pip install -r` med filen *Requirements. txt* när du startar en Notebook-Server. Du behöver inte uttryckligen installera paket från i anteckningsboken själva.

- **Gränssnitts skript**: i den andra List rutan väljer du ett bash-gränssnitts skript i projektet (vanligt vis en fil med fil namns tillägget *. sh* ) som innehåller alla kommandon som du vill köra för att initiera miljön.

- **Miljö. yml**: i den andra List rutan väljer du en *miljö. yml* -fil för python-projekt med hjälp av en Conda-miljö.

   > [!WARNING]
   > Eftersom det här är en för hands versions tjänst under utveckling finns det ett känt problem där `Environment.yml` inställningen inte tillämpas på projektet som förväntat. Projektet och Jupyter-anteckningsbokarna i läser inte in den angivna miljö filen för närvarande.

När du är klar med att lägga till steg väljer du **Spara**.

### <a name="use-the-terminal"></a>Använda terminalen

På instrument panelen för projektet öppnar **Terminal** -kommandot en Linux-terminal som ger direkt åtkomst till servern. I terminalen kan du hämta data, redigera eller hantera filer, granska processer och även använda verktyg som vi och nano.

> [!Note]
> Om du har startskript i projektets miljö visas att öppna terminalen ett meddelande som anger att inställningarna pågår fortfarande.

Du kan utfärda alla standard Linux-kommandon i terminalen. Du kan också använda `ls` i arbetsmappen för att se de olika miljöer som finns på den virtuella datorn, till exempel *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*och *R*, tillsammans med en *projektmapp* som innehåller projektet:

![Projektet terminal i Azure-anteckningsböcker](media/project-terminal.png)

Om du vill påverka en viss miljö, byter du katalog till mappen miljö först.

För python-miljöer hittar du `pip` och `conda` i *bin* -mappen i varje miljö. Du kan också använda inbyggda alias för:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Ändringar som görs på servern gäller endast för den aktuella sessionen, förutom filer och mappar som du skapar i själva *projektmappen* . Till exempel är redigering av en fil i projektmappen beständig mellan sessioner, men paket med `pip install` är inte.

> [!Note]
> Om du använder `python` eller `python3`anropar du systeminstallerade versioner av python, som inte används för antecknings böcker. Du har inte behörighet för åtgärder som `pip install` antingen, så se till att använda de versions bara aliasen.

## <a name="access-notebook-logs"></a>Åtkomstloggar för anteckningsboken

Om du stöter på problem när du kör en bärbar dator lagras utdata från Jupyter i en mapp med namnet *. Obs. log*. Du kan komma åt dessa loggar via **Terminal** -kommandot eller instrument panelen för projektet.

Vanligtvis när du kör Jupyter lokalt kanske du har startat den från ett terminalfönster. Terminalfönstret visar utdata, till exempel kernel status.

Om du vill visa loggar, använder du följande kommando i terminalen:

```bash
cat .nb.log
```

Du kan även använda kommandot från en kodcell i en Python notebook:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Nästa steg

- [Gör så här: arbeta med projektfiler](work-with-project-data-files.md)
- [Komma åt moln data i en bärbar dator](access-data-resources-jupyter-notebooks.md)
