En del paket kanske inte installeras med pip när det körs på Azure.  Det kan vara så att paketet inte finns tillgängligt på Python Package Index.  Det kan vara så att det krävs en kompilator (det finns ingen kompilator tillgänglig på datorn som kör webbappen i Azure App Service).

I det här avsnittet kommer vi att titta på olika sätt att åtgärda problemet.

### <a name="request-wheels"></a>Begär hjul
Om installationen kräver en kompilator, bör du kontakta paketets ägare för att begära att hjul görs tillgängliga för paketet.

Med nyligen blivit [Microsoft Visual C++-kompilatorn för Python 2.7][Microsoft Visual C++ Compiler for Python 2.7], är det nu enklare att skapa paket som har inbyggd kod för Python 2.7.

### <a name="build-wheels-requires-windows"></a>Skapa hjul (kräver Windows)
Obs: När du använder det här alternativet, se till att kompilera paketet med en Python-miljö som matchar den plattform/arkitektur/version som används i webbappen i Azure App Service (Windows/32-bitars/2.7 eller 3.4).

Om paketet inte installeras eftersom det kräver en kompilator, kan du installera kompilatorn på den lokala datorn och skapa ett hjul för paketet, som du sedan inkluderar i ditt centrallager.

Mac/Linux-användare: Om du inte har åtkomst till en Windows-dator, se [skapa en virtuell dator som kör Windows] [ Create a Virtual Machine Running Windows] för hur du skapar en virtuell dator på Azure.  Du kan använda den för att bygga hjul, lägga till dem i centrallagret och sedan ta bort VM:en om du vill. 

För Python 2.7, kan du installera [Microsoft Visual C++-kompilatorn för Python 2.7][Microsoft Visual C++ Compiler for Python 2.7].

För Python 3.4, kan du installera [Microsoft Visual C++ 2010 Express][Microsoft Visual C++ 2010 Express].

För att skapa hjul, behöver du hjulpaketet:

    env\scripts\pip install wheel

Du använder `pip wheel` för att kompilera ett beroende:

    env\scripts\pip wheel azure==0.8.4

Det skapar en .whl-fil i mappen \wheelhouse.  Lägg till mappen \wheelhouse och hjulfilerna till ditt centrallager.

Redigera din requirements.txt för att lägga till `--find-links`-alternativet överst. Det instruerar pip att leta efter en exakt matchning i den lokala mappen innan den går till Python Package Index.

    --find-links wheelhouse
    azure==0.8.4

Om du vill inkludera alla dina beroenden i mappen \wheelhouse och inte använda dig av Python Package Index alls så kan du tvinga pip att ignorera paketindexet genom att lägga till `--no-index` i början av din requirements.txt.

    --no-index

### <a name="customize-installation"></a>Anpassa installationen
Du kan anpassa distributionsskriptet för att installera ett paket i den virtuella miljön med hjälp av ett alternativt installationsprogram som easy\_install.  Se deploy.cmd för ett exempel som har kommenterats bort.  Se till att de paketen inte listats i requirements.txt för att förhindra att pip installerar dem.

Lägg till det här i distributionsskriptet:

    env\scripts\easy_install somepackage

Det är också möjligt att du kan använda easy\_install för att installera från ett exe-installationsprogram (en del är zip-kompatibla så easy\_install stöder dem).  Lägg till installationsprogrammet till ditt centrallager och anropa easy\_install genom att ange sökvägen till den körbara filen.

Lägg till det här i distributionsskriptet:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>Inkludera den virtuella miljön i centrallagret (kräver Windows)
Obs: När du använder det här alternativet, se till att använda en virtuell miljö som matchar den plattform/arkitektur/version som används i webbappen i Azure App Service (Windows/32-bitars/2.7 eller 3.4).

Om du inkluderar den virtuella miljön i centrallagret, kan du förhindra att distributionsskriptet utför virtuell miljöhantering i Azure genom att skapa en tom fil:

    .skipPythonDeployment

Vi rekommenderar att du tar bort den befintliga virtuella miljön i appen, för att undvika överblivna filer från när den virtuella miljön hanterades automatiskt.

[Create a Virtual Machine Running Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ Compiler for Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
