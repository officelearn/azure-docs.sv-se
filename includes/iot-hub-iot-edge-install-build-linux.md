## <a name="install-the-prerequisites"></a>Installera nödvändiga komponenter

Stegen i den här kursen förutsätter att du kör Ubuntu Linux.

Öppna ett gränssnitt för att installera de nödvändiga paketen och kör följande kommandon:

```bash
sudo apt-get update
sudo apt-get install curl build-essential libcurl4-openssl-dev git cmake pkg-config libssl-dev uuid-dev valgrind libglib2.0-dev libtool autoconf
```

Shell, kör du följande kommando för att klona Azure IoT kant GitHub-lagringsplatsen till den lokala datorn:

```bash
git clone https://github.com/Azure/iot-edge.git
```

## <a name="how-to-build-the-sample"></a>Hur du skapar provet

Du kan nu skapa IoT-Edge runtime och exempel på den lokala datorn:

1. Öppna ett gränssnitt.

1. Navigera till rotmappen i den lokala kopian av databasen **iot-edge**.

1. Kör skriptet bygga på följande sätt:

    ```sh
    tools/build.sh --disable-native-remote-modules
    ```

Det här skriptet använder verktyget **cmake** för att skapa en mapp som kallas **build** i rotmappen på den lokala kopian av databasen **iot-edge** och generera en make-fil. Skriptet bygger sedan lösningen och hoppar över enhetstester och slutpunkt till slutpunkt-tester. Om du vill skapa och köra testerna enhet, lägga till den `--run-unittests` parameter. Om du vill skapa och köra testerna slutpunkt till slutpunkt, lägga till den `--run-e2e-tests`.

> [!NOTE]
> Varje gång du kör skriptet **build.sh** tar det bort och återskapar sedan mappen **build** i rotmappen på den lokala kopian av databasen **iot-edge**.