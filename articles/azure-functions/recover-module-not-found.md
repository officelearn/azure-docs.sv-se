---
title: Felsöka python-ModuleNotFoundError i Azure Functions
description: Lär dig hur du felsöker Azure Functions modul som inte hittar fel i python-funktioner.
author: Hazhzeng
ms.topic: article
ms.date: 05/12/2020
ms.author: hazeng
ms.custom: tracking-python
ms.openlocfilehash: b2582caf407b3983b32c40482fa0f0275f00fb8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84554758"
---
# <a name="troubleshoot-python-module-errors-in-azure-functions"></a>Felsök fel i python-modul i Azure Functions

Den här artikeln hjälper dig att felsöka tjänstrelaterade fel i din python Function-app. Felen leder vanligt vis till följande Azure Functions fel meddelande:

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

Det här fel problemet uppstår när en python-Function-app inte kan läsa in en python-modul. Rotor saken till det här felet är ett av följande problem:

- [Det går inte att hitta paketet](#the-package-cant-be-found)
- [Paketet är inte löst med rätt Linux-hjul](#the-package-isnt-resolved-with-proper-linux-wheel)
- [Paketet är inte kompatibelt med python-tolkens version](#the-package-is-incompatible-with-the-python-interpreter-version)
- [Paketet är i konflikt med andra paket](#the-package-conflicts-with-other-packages)
- [Paketet stöder bara Windows-eller macOS-plattformar](#the-package-only-supports-windows-or-macos-platforms)

## <a name="view-project-files"></a>Visa projektfiler

För att identifiera den faktiska orsaken till ditt problem måste du hämta de python-projektfiler som körs i din Function-app. Om du inte har Project-filerna på den lokala datorn kan du hämta dem på något av följande sätt:

- Om Function-appen har `WEBSITE_RUN_FROM_PACKAGE` en app-inställning och dess värde är en URL, laddar du ned filen genom att kopiera och klistra in URL: en i webbläsaren.
- Om Function-appen har `WEBSITE_RUN_FROM_PACKAGE` och den är inställd på `1` , navigerar du till `https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages` och laddar ned filen från den senaste `href` URL: en.
- Om appen inte har den angivna program inställningen ovan går du till `https://<app-name>.scm.azurewebsites.net/api/settings` och letar upp URL: en under `SCM_RUN_FROM_PACKAGE` . Hämta filen genom att kopiera och klistra in webb adressen i webbläsaren.
- Om inget av dessa fungerar för dig kan du navigera till `https://<app-name>.scm.azurewebsites.net/DebugConsole` och visa innehållet under `/home/site/wwwroot` .

Resten av den här artikeln hjälper dig att felsöka eventuella orsaker till det här felet genom att granska innehållet i funktions appen, identifiera rotor saken och lösa problemet.

## <a name="diagnose-modulenotfounderror"></a>Diagnostisera ModuleNotFoundError

I det här avsnittet beskrivs möjliga Rotors Aker till fel i moduler. När du har avvisat vilket är den sannolika rotor saken kan du gå till den relaterade lösningen.

### <a name="the-package-cant-be-found"></a>Det går inte att hitta paketet

Bläddra till `.python_packages/lib/python3.6/site-packages/<package-name>` eller `.python_packages/lib/site-packages/<package-name>` . Om fil Sök vägen inte finns är den saknade sökvägen troligt vis rotor saken.

Det här problemet kan uppstå om du använder ett eller inaktuella verktyg från tredje part under distributionen.

Se [Aktivera fjärran sluten build](#enable-remote-build) eller [skapa egna beroenden](#build-native-dependencies) för minskning.

### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>Paketet är inte löst med rätt Linux-hjul

Gå till `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` eller `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` . Använd din favorit text redigerare för att öppna **hjul** filen och kontrol lera avsnittet **tag:** . Om taggens värde inte innehåller **Linux**kan detta vara ett problem.

Python-funktioner körs bara på Linux i Azure: Functions runtime v2. x körs på Debian utsträckt och v3. x-körningsmiljön på Debian Buster. Artefakten förväntas innehålla rätt Linux-binärfiler. Om `--build local` du använder flagga i Core-verktyg, tredje part eller inaktuella verktyg kan det leda till att äldre binärfiler används.

Se [Aktivera fjärran sluten build](#enable-remote-build) eller [skapa egna beroenden](#build-native-dependencies) för minskning.

### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>Paketet är inte kompatibelt med python-tolkens version

Gå till `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` eller `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` . Öppna METADATAFILEN i en text redigerare och kontrol lera avsnittet **klassificerare:** . Om avsnittet inte innehåller `Python :: 3` , `Python :: 3.6` , `Python :: 3.7` eller `Python :: 3.8` , innebär det att paket versionen är antingen för gammal eller förmodligen är paketet redan i underhålls läge.

Du kan kontrol lera python-versionen av din Function-app från [Azure Portal](https://portal.azure.com). Navigera till din Function-app, Välj **resurs Utforskaren**och välj **gå**.

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="Öppna appen Resursläsaren för Function-appen i portalen":::

När Explorer har lästs in söker du efter **LinuxFxVersion**, som visar python-versionen.

Se [Uppdatera paketet till den senaste versionen](#update-your-package-to-the-latest-version) eller [Ersätt paketet med motsvarande](#replace-the-package-with-equivalents) skydds åtgärder.

### <a name="the-package-conflicts-with-other-packages"></a>Paketet är i konflikt med andra paket

Om du har verifierat att paketet har lösts korrekt med riktiga Linux-hjul, kan det uppstå en konflikt med andra paket. I vissa paket kan PyPi-dokumenten klargöra de inkompatibla modulerna. I finns det t. ex. [`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/) en instruktion på följande sätt:

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

Du hittar dokumentationen för paket versionen i `https://pypi.org/project/<package-name>/<package-version>` .

Se [Uppdatera paketet till den senaste versionen](#update-your-package-to-the-latest-version) eller [Ersätt paketet med motsvarande](#replace-the-package-with-equivalents) skydds åtgärder.

### <a name="the-package-only-supports-windows-or-macos-platforms"></a>Paketet stöder bara Windows-eller macOS-plattformar

Öppna `requirements.txt` med en text redigerare och kontrol lera paketet i `https://pypi.org/project/<package-name>` . Vissa paket körs bara på Windows-eller macOS-plattformar. Till exempel körs pywin32 endast på Windows.

`Module Not Found`Felet kanske inte uppstår om du använder Windows eller MacOS för lokal utveckling. Paketet kan dock inte importeras på Azure Functions, som använder Linux vid körning. Detta beror troligen på `pip freeze` att du använder för att exportera den virtuella miljön till requirements.txt från din Windows-eller MacOS-dator under projekt initieringen.

Se [Ersätt paketet med motsvarande](#replace-the-package-with-equivalents) eller [Handcraft requirements.txt](#handcraft-requirementstxt) för att undvika problemet.

## <a name="mitigate-modulenotfounderror"></a>Minimera ModuleNotFoundError

Följande är möjliga åtgärder för problem med att åtgärda moduler. Använd [identifierarna ovan](#diagnose-modulenotfounderror) för att avgöra vilka av dessa åtgärder som kan utföras.

### <a name="enable-remote-build"></a>Aktivera fjärran sluten build

Kontrol lera att fjärran sluten version är aktive rad. Hur du gör detta beror på din distributions metod.

# <a name="visual-studio-code"></a>[Visuell Studio-kod](#tab/vscode)
Kontrol lera att den senaste versionen av [Azure Functions-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) är installerad. Kontrol lera att `.vscode/settings.json` den finns och att den innehåller inställningen `"azureFunctions.scmDoBuildDuringDeployment": true` . Om inte, skapar du den här filen med `azureFunctions.scmDoBuildDuringDeployment` inställningen aktive rad och distribuerar om projektet.

# <a name="azure-functions-core-tools"></a>[Azure Functions Core Tools](#tab/coretools)

Kontrol lera att den senaste versionen av [Azure Functions Core tools](https://github.com/Azure/azure-functions-core-tools/releases) är installerad. Gå till din lokala Function-projektmapp och Använd den `func azure functionapp publish <app-name>` för distribution.

# <a name="manual-publishing"></a>[Manuell publicering](#tab/manual)

Om du publicerar paketet manuellt i `https://<app-name>.scm.azurewebsites.net/api/zipdeploy` slut punkten ser du till att både **SCM_DO_BUILD_DURING_DEPLOYMENT** och **ENABLE_ORYX_BUILD** har angetts till **Sant**. Mer information finns i [så här arbetar du med program inställningar](functions-how-to-use-azure-function-app-settings.md#settings).

---

### <a name="build-native-dependencies"></a>Bygg egna beroenden

Se till att den senaste versionen av både **Docker** och [Azure Functions Core tools](https://github.com/Azure/azure-functions-core-tools/releases) är installerad. Gå till din lokala Function-projektmapp och Använd den `func azure functionapp publish <app-name> --build-native-deps` för distribution.

### <a name="update-your-package-to-the-latest-version"></a>Uppdatera paketet till den senaste versionen

Bläddra igenom den senaste paket versionen i `https://pypi.org/project/<package-name>` och kontrol lera avsnittet **klassificerare:** . Paketet bör vara `OS Independent` eller kompatibelt med `POSIX` eller `POSIX :: Linux` i **operativ systemet**. Dessutom bör programmeringsspråk innehålla `Python :: 3` , `Python :: 3.6` , `Python :: 3.7` eller `Python :: 3.8` .

Om dessa är korrekta kan du uppdatera paketet till den senaste versionen genom att ändra raden `<package-name>~=<latest-version>` i requirements.txt.

### <a name="handcraft-requirementstxt"></a>Handcraft requirements.txt

Vissa utvecklare använder `pip freeze > requirements.txt` för att generera listan med python-paket för deras utvecklings miljöer. Även om den här bekvämligheten bör fungera i de flesta fall kan det finnas problem i distributions scenarier mellan plattformar, till exempel utveckla funktioner lokalt på Windows eller macOS, men publicera till en Function-app som körs i Linux. I det här scenariot `pip freeze` kan du införa oväntade beroenden eller beroenden för operativ systemet i din lokala utvecklings miljö. Dessa beroenden kan bryta python Function-appen när den körs på Linux.

Det bästa sättet är att kontrol lera import-instruktionen från varje. py-fil i projektets källkod och bara checka in modulerna i requirements.txt-filen. Detta garanterar att paket lösningarna kan hanteras korrekt på olika operativ system.

### <a name="replace-the-package-with-equivalents"></a>Ersätt paketet med motsvarande

Först bör vi ta en titt på den senaste versionen av paketet i `https://pypi.org/project/<package-name>` . Det här paketet har vanligt vis sin egen GitHub-sida, gå till avsnittet **problem** på GitHub och Sök om problemet har åtgärd ATS. I så fall, uppdatera paketet till den senaste versionen.

Ibland kan paketet ha integrerats i [python standard library](https://docs.python.org/3/library/) (till exempel pathlib). I så fall, eftersom vi tillhandahåller en viss python-distribution i Azure Functions (python 3,6, python 3,7 och python 3,8), ska paketet i requirements.txt tas bort.

Men om du stöter på ett problem som inte har åtgärd ATS och du har en tids gräns. Jag uppmuntrar dig att göra en del forskning och hitta ett liknande paket för ditt projekt. I vanliga fall ger python-communityn en mängd olika liknande bibliotek som du kan använda.

## <a name="next-steps"></a>Nästa steg

Om du inte kan lösa problemet med en modul kan du rapportera detta till Functions-teamet:

> [!div class="nextstepaction"]
> [Rapportera ett olöst problem](https://github.com/Azure/azure-functions-python-worker/issues)
