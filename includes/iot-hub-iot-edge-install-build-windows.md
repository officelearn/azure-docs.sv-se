## <a name="install-the-prerequisites"></a>Installera nödvändiga komponenter

1. Installera [Visual Studio 2015 eller 2017](https://www.visualstudio.com). Du kan använda den kostnadsfria Community Edition om du uppfyller licenskraven. Se till att inkludera Visual C++ och NuGet Package Manager.

1. Installera [git](http://www.git-scm.com) och kontrollera att du kan köra git.exe från kommandoraden.

1. Installera [CMake](https://cmake.org/download/) och kontrollera att du kan köra cmake.exe från kommandoraden. CMake version 3.7.2 eller senare rekommenderas. Den **.msi** installer är det enklaste alternativet i Windows. Lägg till CMake minst till SÖKVÄGEN för den aktuella användaren när du uppmanas av installationsprogrammet.

1. Installera [Python 2.7](https://www.python.org/downloads/release/python-27). Kontrollera att du kan lägga till Python till din `PATH` miljövariabeln. Gå till **Kontrollpanelen** > **System och säkerhet** > **System** > **avancerade systeminställningar**  >  **Miljövariabler**. Lägg till `C:\Python27` till din sökväg. 

1. Kör följande kommando för att klona Azure IoT kant GitHub-lagringsplatsen till den lokala datorn i en kommandotolk:

    ```cmd
    git clone https://github.com/Azure/iot-edge.git
    ```

## <a name="how-to-build-the-sample"></a>Hur du skapar provet

Du kan nu skapa IoT-Edge runtime och exempel på den lokala datorn:

1. Öppna **Developer kommandotolk för VS 2015** eller **Developer kommandotolk för VS 2017**, beroende på din version.

1. Navigera till rotmappen i den lokala kopian av databasen **iot-edge**.

1. Kör skriptet bygga på följande sätt:

    ```cmd
    tools\build.cmd --disable-native-remote-modules
    ```

Det här skriptet skapar en fil för Visual Studio-lösning och bygger lösningen. Du kan hitta Visual Studio-lösning i den **skapa** mapp i den lokala kopian av den **iot-edge** databasen. Om du vill skapa och köra testerna enhet, lägga till den `--run-unittests` parameter. Om du vill skapa och köra testerna slutpunkt till slutpunkt, lägga till den `--run-e2e-tests`.

> [!NOTE]
> Varje gång du kör den **build.cmd** skript, tas bort och återskapas den **skapa** mapp i rotmappen på den lokala kopian av den **iot-edge** databasen.