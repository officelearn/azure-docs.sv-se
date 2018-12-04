---
title: Konfigurera och hantera Azure-anteckningsbok projekt | Microsoft Docs
description: Så här hanterar du projektet metadata, projektfiler, projektets miljö och installationsprogrammet visar både Användargränssnittet för Azure-anteckningsböcker och terminal direktåtkomst.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 35dd6ff1-a14a-4a2e-b173-6d8467de3e89
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 0e24d06cfeefd8315e21a45b833e6bf0e5f9dfd9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856833"
---
# <a name="manage-and-configure-projects"></a>Hantera och konfigurera projekt

Ett projekt i Azure-datorer är i grunden en konfiguration av den underliggande Linux-datorn som kör Jupyter-anteckningsböcker tillsammans med en filmapp och beskrivande metadata. Instrumentpanelen för projektet i Azure-datorer kan du hantera filer och konfigurera projektets egenskaper:

- Projekt-metadata innehåller ett namn, beskrivning, en identifierare som används när du delar projektet och om projektet är offentlig eller privat.
- Du hanterar i projektets anteckningsboken, data och andra filer som du gör med ett annat filsystem.
- Du konfigurerar ett projekts miljö via startskript eller direkt via terminalen.
- Du har åtkomst till loggar via terminalen.

> [!Note]
> Du kan inte hantera projekt som du äger inte om inte projektägare har gjort en medarbetare. Annars är hanterings- och funktioner som beskrivs här inte tillgängliga för dig.

Azure-datorer startar den underliggande virtuella datorn när du kör en bärbar dator eller en annan fil. Servern sparar filer automatiskt och stängs av efter 60 minuters inaktivitet. Du kan också stoppa servern när som helst med den **avstängning** kommando (kortkommandot: h).

## <a name="edit-project-metadata"></a>Redigera projekt metadata

På instrumentpanelen för projektet väljer **Projektinställningar**och välj sedan den **Information** fliken som innehåller projektets metadata enligt beskrivningen i följande tabell. Du kan ändra projekt metadata när som helst.

| Inställning | Beskrivning |
| --- | --- |
| Projektnamn | Ett eget namn för ditt projekt som Azure-datorer använder för visning. Till exempel ”Hello World i Python”. |
| Projekt-ID | En anpassad identifierare som blir en del av URL: en som du använder för att dela ett projekt (formuläret är `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Detta ID kan använda endast bokstäver, siffror och bindestreck och är begränsade till 30 tecken. Om du är osäker vad du använder, är en gemensam konvention att använda en gemen version av ditt projektnamn där blanksteg är aktiverade i bindestreck, till exempel ”min projektnamn” blir ”mitt-projekt – namn”. |
| Offentliga projekt | Om ange, kan vem som helst med en länk till projektet. När du skapar ett privat projekt kan du avmarkera det här alternativet. |
| Dölj kloner | Om du anger, andra användare inte kan se en lista över kloner som har gjorts för det här projektet. Dölja kloner är användbart för projekt som delas med många personer som inte tillhör samma organisation, till exempel när du använder en bärbar dator för undervisar en klass. |

> [!Important]
>
> Ändra projekt-ID upphäver alla länkar i projektet som du kanske har delat tidigare.

## <a name="manage-project-files"></a>Hantera projektfiler

Innehållet i projektets mappsystem visas i instrumentpanelen för projektet. Du kan använda olika kommandon för att hantera dessa filer.

### <a name="create-new-files-and-folders"></a>Skapa nya filer och mappar

Den **+ ny** kommando (kortkommandot: n) skapar nya filer eller mappar. När du använder kommandot måste du först välja vilken typ av objekt som ska skapas:

| Typ av objekt | Beskrivning | Kommandobeteendet |
| --- | --- | --- |
| **Notebook** | En Jupyter-anteckningsbok | Visar ett fönster där du anger denna notebook filnamnet och språk. |
| **Mapp** | En undermapp | Skapar ett redigerbart fält i projektets fillistan som du anger namnet på mappen. |
| **Tom fil** | En fil som du kan lagra innehåll, till exempel text, data, osv. | Skapar ett redigerbart fält i projektets fillistan som du anger namnet på filen. |
| **Markdown** | En markdownfil. | Skapar ett redigerbart fält i projektets fillistan som du anger namnet på filen. |

### <a name="upload-files"></a>Överföra filer

Den **överför** kommandot ger två alternativ för att importera data från andra platser: **från URL: en** och **från datorn**. Mer information finns i [arbeta med datafiler i Azure-anteckningsbok projekt](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Välj fil-fil

Varje objekt i listan över filer i projektets innehåller kommandon via en snabbmenyn:

![Kommandon på en snabbmeny för fil](media/project-file-commands.png)

| Kommando | Kortkommando | Åtgärd |
| --- | --- | --- |
| Kör | r (eller klicka på) | Kör en notebook-fil. Andra filtyper öppnas för visning.  |
| Kopiera länk | Y | Kopierar en länk till filen till Urklipp. |
| Kör i Jupyter-labb | J | Kör en anteckningsbok i JupyterLab, vilket är ett mer utvecklarorienterade gränssnitt än Jupyter ger normalt. |
| Förhandsversion | p | Öppnar en HTML-förhandsgranskning av filen. för bärbara datorer är förhandsgranskningen en skrivskyddad återgivningen av anteckningsboken. Mer information finns i den [förhandsversion](#preview) avsnittet. |
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
| Dela | S | Visar delningsapplikationen popup-fönstret som du kan få en länk, dela till sociala medier, hämta HTML för att bädda in och skicka ett e-postmeddelande. |
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

Azure-anteckningsböcker för första gången skapar en server för projektet, det ser ut för en fil i projektet med namnet *aznbsetup.sh*. Om den här filen finns körs det i Azure-anteckningsböcker. Utdata från skriptet lagras i en projektmapp som *. aznbsetup.log*.

### <a name="environment-setup-steps"></a>Steg för konfiguration av miljö

Du kan använda i projektets miljöinställningar för att skapa enskilda steg som konfigurerar miljön.

På instrumentpanelen för projektet väljer **Projektinställningar**och välj sedan den **miljö** fliken där du lägger till, ta bort och ändra inställningsstegen för projektet:

![Projektet inställningar popup-fönstret med fliken miljö valt](media/project-settings-environment-steps.png)

Om du vill lägga till ett steg, väljer du först **+ Lägg till**, Välj en steg i den **åtgärden** listrutan:

![Åtgärden väljaren för en ny miljö installationssteget](media/project-settings-environment-details.png)

Vilken information som du sedan projicera beror på vilken typ av åtgärd som du har valt:

- **Requirements.txt**: I den andra listan väljer du en *requirements.txt* fil som redan finns i projektet. Välj sedan en Python-version från den tredje listan som visas. Med hjälp av en *requirements.txt* fil, Azure-datorer körs `pip install -r` med den *requirements.txt* filen när du startar en notebook-server. Du behöver inte uttryckligen installera paket från i anteckningsboken själva.

- **Kommandoskriptet**: I den andra listan väljer du ett bash shell-skript i projektet (vanligtvis en fil med det *.sh* tillägget) som innehåller alla kommandon som du vill köra för att initiera miljön.

- **Environment.yml**: I den andra listan väljer du en *environments.yml* -filen för Python-projekt med hjälp av en conda-miljö.

När du är klar att lägga till steg, Välj **spara**.

### <a name="use-the-terminal"></a>Använda terminalen

På instrumentpanelen för projektet i **Terminal** kommandot öppnar en Linux-terminal som ger direktåtkomst till servern. I terminalen kan du hämta data, redigera eller hantera filer, granska processer och även använda verktyg som vi och nano.

> [!Note]
> Om du har startskript i projektets miljö visas att öppna terminalen ett meddelande som anger att inställningarna pågår fortfarande.

Du kan utfärda alla standard Linux-kommandon i terminalen. Du kan också använda `ls` i arbetsmappen till finns i olika miljöer som finns på den virtuella datorn, till exempel *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*, och *R*, tillsammans med en *projekt* mapp som innehåller projektet:

![Projektet terminal i Azure-anteckningsböcker](media/project-terminal.png)

Om du vill påverka en viss miljö, byter du katalog till mappen miljö först.

För Python-miljöer kan du hitta `pip` och `conda` i *bin* mapp för varje miljö. Du kan också använda inbyggda alias för:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Ändringar som görs till servern gäller endast för den aktuella sessionen, utom filer och mappar som du skapar i den *projekt* själva mappen. Till exempel Filredigering i projektmappen beständiga mellan sessioner, men paket med `pip install` inte.

> [!Note]
> Om du använder `python` eller `python3`, du anropa system-installerade versioner av Python som inte används för bärbara datorer. Du har inte behörighet för åtgärder som `pip install` , så var noga med att använda versionsspecifika alias.

## <a name="access-notebook-logs"></a>Åtkomstloggar för anteckningsboken

Om du stöter på problem när du kör en bärbar dator, utdata från Jupyter lagras i en mapp med namnet *. nb.log*. Du kan komma åt de här loggarna via den **Terminal** kommando eller instrumentpanelen för projektet.

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

- [Så här: arbeta med data projektfiler](work-with-project-data-files.md)
- [Åtkomst till molndata på en bärbar dator](access-data-resources-jupyter-notebooks.md)
