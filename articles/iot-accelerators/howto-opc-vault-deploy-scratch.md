---
title: Så här distribuerar du Azure IoT OPC UA certifikatet management-modulen från grunden | Microsoft Docs
description: Så här distribuerar OPC valvet från grunden.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: a3a9d21b70f16482f05d27aa0df8d8865459aeb4
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759627"
---
# <a name="deploy-opc-vault-from-scratch"></a>Distribuera OPC-valvet från grunden

Azure IoT OPC UA certifikathantering, även kallade OPC-valv är en mikrotjänst som kan konfigurera registreras, och hantera certifikatets livscykel för OPC UA-servern och klienten program i molnet. Den här artikeln visar hur du distribuerar OPC valvet från grunden.

## <a name="configuration-and-environment-variables"></a>Konfigurations- och variabler

Tjänstkonfigurationen lagras med ASP.NET Core configuration kort i appsettings.ini. INI-format kan lagra värden i ett lättläst format, med kommentarer.
Programmet stöder också infoga miljövariabler, till exempel autentiseringsuppgifter och nätverk information. (Det här avsnittet heter ursprungligen TODO konfigurations- och variabler).

Konfigurationsfilen i databasen refererar till några miljövariabler som måste skapas minst en gång. Det finns flera sätt att hantera miljövariabler beroende på ditt operativsystem och IDE:

- För Windows-användare behöver env-vars-setup.cmd skriptet vara förberedd och körs bara en gång. När den exekveras, beständig inställningarna i alla sessioner från en terminal och omstarter.

- För Linux och OS x-miljöer måste env-vars-setup-skriptet som ska köras varje gång en ny konsol öppnas. Det finns sätt att spara värdena globalt, mer information som dessa sidor ska hjälpa beroende på Operativsystemet och terminal:

  - https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
  
  - https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
  
  - https://help.ubuntu.com/community/EnvironmentVariables
  

- Visual Studio: Miljövariabler kan anges också från Visual Studio, under projektegenskaperna, i det vänstra fönstret väljer du ”konfigurationsegenskaper” och ”miljö” för att få till ett avsnitt där du kan lägga till flera variabler.

- IntelliJ Rider: Miljövariabler kan anges i varje kör konfiguration på samma sätt som IntelliJ IDEA https://www.jetbrains.com/help/idea/run-debug-configuration-application.html

## <a name="run-and-debug-with-visual-studio"></a>Köra och felsöka med Visual Studio

Visual Studio kan du snabbt vill öppna programmet utan att använda en kommandotolk, utan att konfigurera något utanför IDE.

Steg med Visual Studio 2017:

1. Öppna lösningen med hjälp av den `iot-opc-gds-service.sln` filen.

1. När lösningen har lästs in, högerklicka på den `WebService` projektet, Välj och gå till den `Debug` avsnittet.

1. Definiera miljövariabler som behövs i samma avsnitt.

1. Tryck på **F5**, eller **kör** ikon. Visual Studio ska öppna webbläsaren som visar status för tjänsten i JSON-format.

## <a name="run-and-debug-with-intellij-rider"></a>Köra och felsöka med IntelliJ föraren

1. Öppna lösningen med hjälp av den `iot-opc-gds-service.sln` filen.

1. När lösningen har lästs in, går du till `Run > Edit Configurations` och skapa en ny `.NET Project` konfiguration.

1. Välj WebService-projektet i konfigurationen.

1. Spara inställningarna och kör sedan konfigurationen som har skapats från IDE-verktygsfältet.

1. Du bör se tjänsten bootstrap meddelanden i IntelliJ körningsfönstret med information om till exempel URL: en där webbtjänsten körs plus tjänsten loggar.

## <a name="build-and-run-from-the-command-line"></a>Skapa och köra från kommandoraden

Skriptmappen innehåller vissa skript för vanliga uppgifter:

- `build`: Kompilera alla projekt och kör testerna.

- `compile`: Kompilera alla projekt.

- `run`: Kompilera projekten och kör tjänsten som ber om utökade privilegier i Windows för att köra webbtjänsten.

Skripten Kontrollera variabler miljökonfiguration. Du kan ange miljövariabler globalt i ditt operativsystem eller Använd ”env-vars-setup” skript i skriptmappen.

### <a name="sandbox"></a>Begränsat läge

Skripten förutsätter att du har konfigurerat utvecklingsmiljön med .NET Core och Docker. Du kan undvika att installera .NET Core och installera endast Docker och använder du kommandoradsparametern `--in-sandbox` (eller kortformen `-s`), till exempel:

- `build --in-sandbox`: Kör denna build task inuti en Docker-behållare (kort form `build -s`).

- `compile --in-sandbox`: Kör aktiviteten kompilering inuti en Docker-behållare (kort form `compile -s`).

- `run --in-sandbox`: Startar tjänsten inuti en Docker-behållare (kort form `run -s`).

Docker-avbildningar som används för sandbox-miljön finns på Docker Hub [här](https://hub.docker.com/r/azureiotpcs/code-builder-dotnet).

## <a name="package-the-application-to-a-docker-image"></a>Paketera program i en dockeravbildning

Den `scripts` mappen innehåller en docker-undermapp med de filer som krävs för att paketera tjänsten till en Docker-avbildning:

- `Dockerfile`: Specifikationer för docker-avbildningar.
- `build`: Skapa en Docker-behållare och lagra avbildningen i det lokala registret.
- `run`: Kör Docker-behållare från avbildningen lagras i det lokala registret.
- `content`: En mapp med filer som kopieras till avbildningen, inklusive startpunktsskriptet.

## <a name="azure-iot-hub-setup"></a>Konfigurationen av Azure IoT Hub

Om du vill använda mikrotjänst, ställer du in Azure IoT Hub för utveckling och integration tester.

Projektet innehåller vissa Bash-skript för att hjälpa dig med den här konfigurationen:

- Skapa ny IoT-hubb: `./scripts/iothub/create-hub.sh`

- Lista över befintliga hubbar: `./scripts/iothub/list-hubs.sh`

- Visa IoT Hub information (till exempel nycklar): `./scripts/iothub/show-hub.sh`

Och om du har flera Azure-prenumerationer:

- Visa prenumerationslista: `./scripts/iothub/list-subscriptions.sh`

- Ändra aktuell prenumeration: `./scripts/iothub/select-subscription.sh`

## <a name="development-setup"></a>Utveckling av installationsprogrammet

### <a name="net-setup"></a>Installationen av .NET

Projektarbetsflödet hanteras via [.NET Core](https://dotnet.github.io) 1.x, som du måste installera i din miljö, så att du kan köra alla skript och se till att din IDE fungerar som förväntat.

Vi ger även en [Java-version](https://github.com/Azure/iot-opc-gds-service-dotnet) av det här projektet och andra komponenter i Azure IoT-datorer.

### <a name="ide"></a>IDE

Här är några av de IDE: er som du kan använda för att arbeta med Azure IoT-datorer:

- [Visual Studio](https://www.visualstudio.com)
- [Visual Studio för Mac](https://www.visualstudio.com/vs/visual-studio-mac)
- [IntelliJ Rider](https://www.jetbrains.com/rider)
- [Visual Studio Code](https://code.visualstudio.com)

### <a name="git-setup"></a>Git-installation

Projektet innehåller en Git-hooken, för att automatisera vissa kontroller innan du godkänner en kodändring. Du kan köra testerna manuellt eller låta CI-plattform för att köra testerna. Vi använder följande Git-hooken att automatiskt köra alla tester innan du skickar ändringar i koden till GitHub och snabba upp arbetsflöde för utveckling.

Om du vill ta bort hooken när som helst, helt enkelt ta bort filen installerad `.git/hooks`. Du kan också kringgå före genomförandet hook med hjälp av den `--no-verify` alternativet.

#### <a name="pre-commit-hook-with-sandbox"></a>Före genomförandet hook med begränsat läge

Om du vill konfigurera ingår hookar, öppna en Windows/Linux/MacOS-konsol och kör:

```
cd PROJECT-FOLDER
cd scripts/git
setup --with-sandbox
```

Med den här konfigurationen, när du checkar in filer, verifierar Git att programmet klarar alla tester, kör versionen och tester i en Docker-behållare som är konfigurerad med alla krav för utveckling.

#### <a name="pre-commit-hook-without-sandbox"></a>Före genomförandet hook utan begränsat läge

> [!NOTE] 
> Hook utan sandbox-miljön kräver [.NET Core](https://dotnet.github.io) i systemet sökväg.

Om du vill konfigurera ingår hookar, öppna en Windows/Linux/MacOS-konsol och kör:

```
cd PROJECT-FOLDER
cd scripts/git
setup --no-sandbox
```
Med den här konfigurationen, när du checkar in filer, verifierar Git att programmet klarar alla tester, kör versionen och testerna i arbetsstationen med hjälp av verktygen som installeras i ditt operativsystem.

Vägledning för projekt-kodformat:

- Där det är rimligt, är rader längden begränsad till högst 80 tecken för att Kodgranskning och kommandoradsverktyget redigerare.

- Koden blockerar indrag fyra blanksteg. Fliken char bör undvikas.

- Textfiler använder Unix slutet av raden format (LF).

- Beroendeinmatning hanteras med [Autofac](https://autofac.org).

- Web service-API: er fält är CamelCased förutom metadata.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar OPC valvet från grunden, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Distribuera OPC-Twin från grunden](howto-opc-twin-deploy-modules.md)