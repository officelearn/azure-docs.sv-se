---
title: Köra startåtgärder i Azure-molntjänster | Microsoft Docs
description: Startåtgärder hjälper dig att förbereda din cloud service-miljö för din app. Detta får du lära dig hur startåtgärder fungerar och hur du gör dem
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 886939be-4b5b-49cc-9a6e-2172e3c133e9
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 59bfa83ab3432adb7a4df5112367f87014a0b292
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405995"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Hur du konfigurerar och köra startåtgärder för en molntjänst
Du kan använda startåtgärder för att utföra åtgärder innan en roll startas. Åtgärder som du kanske vill utföra omfattar installera en komponent, registrerar COM-komponenter, ange registernycklar eller starta en tidskrävande process.

> [!NOTE]
> Startåtgärder kan inte användas för virtuella datorer, endast Cloud Service-Web och Worker-roller.
> 
> 

## <a name="how-startup-tasks-work"></a>Hur fungerar startåtgärder
Startåtgärder är åtgärder som vidtas innan dina roller startas och definieras i den [ServiceDefinition.csdef] filen med hjälp av den [Aktivitet] element i den [Start] element. Startåtgärder är ofta batch-filer, men de kan även vara konsolprogram eller batchfiler som startar PowerShell-skript.

Miljövariabler skickar information till en startåtgärd och lokal lagring som kan användas för att överföra information från en startåtgärd. Till exempel en miljövariabel kan ange sökvägen till ett program som du vill installera och går att skriva filer till lokal lagring som kan läsas senare sedan av dina roller.

Din startåtgärd kan logga information och fel till den katalog som anges av den **TEMP** miljövariabeln. Under startåtgärden, den **TEMP** miljövariabeln som motsvarar den *C:\\resurser\\temp\\[guid]. [ rolename]\\RoleTemp* directory när du kör i molnet.

Startåtgärder kan även köras flera gånger mellan omstarter. Startåtgärden körs till exempel varje gång rollen återanvänds och rollåteranvändningar kanske inte alltid innefattar en omstart. Startåtgärder måste skrivas i ett sätt som gör att de kan köras flera gånger utan problem.

Startåtgärder måste avslutas med en **errorlevel** (eller slutkod) på noll för startprocessen ska slutföras. Om en startåtgärd avslutas med en noll **errorlevel**, startar inte rollen.

## <a name="role-startup-order"></a>Rollen startordningen
Här nedan listas startproceduren roll i Azure:

1. Instansen är markerad som **startar** och tar inte emot trafik.
2. Alla startåtgärder utförs enligt deras **taskType** attribut.
   
   * Den **enkel** åtgärder körs synkront, en i taget.
   * Den **bakgrund** och **förgrunden** aktiviteterna startas asynkront, parallell till startåtgärden.  
     
     > [!WARNING]
     > IIS kan inte konfigureras fullständigt under start uppgift valet i startprocessen, så rollspecifika data är inte kanske tillgänglig. Startåtgärder som kräver att rollspecifika data bör använda [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. Värdprocess för rollen har startats och webbplatsen skapas i IIS.
4. Den [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) metoden anropas.
5. Instansen är markerad som **redo** och trafiken dirigeras till instansen.
6. Den [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](/previous-versions/azure/reference/ee772746(v=azure.100)) metoden anropas.

## <a name="example-of-a-startup-task"></a>Exempel på en startåtgärd
Startåtgärder är definierade i den [ServiceDefinition.csdef] filen den **uppgift** element. Den **commandLine** attributet anger namnet och parametrarna Start batch-fil eller konsolen kommandot den **executionContext** attributet anger behörighetsnivå för startåtgärden, och **taskType** attributet anger hur aktiviteten körs.

I det här exemplet, en miljövariabel **MyVersionNumber**, är skapade för startåtgärden och inställd på värdet ”**1.0.0.0**”.

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

I följande exempel visas den **Startup.cmd** kommandofil skriver raden ”den aktuella versionen är 1.0.0.0” i filen StartupLog.txt i katalogen som anges av miljövariabeln TEMP. Den `EXIT /B 0` rad säkerställer att startåtgärden avslutas med en **errorlevel** noll.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> I Visual Studio i **kopiera till utdatakatalog** egenskapen Start batch-filen ska vara inställd på **Kopiera alltid** att se till att dina batch-startfilen distribueras korrekt i projektet på Azure (**approot\\bin** för webbroller och **approot** för worker-roller).
> 
> 

## <a name="description-of-task-attributes"></a>Beskrivning av uppgiftsattribut
Nedan beskrivs attributen för den **uppgift** elementet i den [ServiceDefinition.csdef] fil:

**commandLine** -kommandoraden för startåtgärden:

* Kommandot med valfria kommandoradsparametrar, vilket startar startåtgärden.
* Det här är ofta filnamnet på en .cmd eller .bat kommandofilen.
* Aktiviteten är i förhållande till i AppRoot\\Bin-mappen för distributionen. Miljövariabler visas inte fastställa sökvägen och filnamnet för uppgiften. Om miljön expansion krävs måste skapa du ett litet cmd-skript som anropar din startåtgärd.
* Kan vara ett konsolprogram eller en batchfil som startar en [PowerShell-skript](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** – anger en behörighetsnivå för startåtgärden. En behörighetsnivå kan vara begränsad eller utökade:

* **limited**  
  Startåtgärden körs med samma behörigheter som rollen. När den **executionContext** attributet för den [Runtime] element är också **begränsad**, användaren privilegier som används.
* **upphöjd**  
  Startåtgärden körs med administratörsbehörighet. Detta gör startåtgärder för att installera program, göra ändringar i IIS-konfigurationen, utföra ändringar i registret och andra nivån administratörsåtgärder, utan att öka behörighetsnivå för rollen själva.  

> [!NOTE]
> Behörighetsnivå för en startåtgärd behöver inte vara samma som rollen själva.
> 
> 

**taskType** – anger hur en startåtgärd körs.

* **simple**  
  Åtgärder körs synkront, en i taget i den ordning som anges i den [ServiceDefinition.csdef] fil. När en **enkel** startåtgärd avslutas med en **errorlevel** noll, nästa **enkel** startåtgärden körs. Om det inte finns några **enkel** startåtgärder för att köra, och sedan rollen själva kommer att startas.   
  
  > [!NOTE]
  > Om den **enkel** uppgiften avslutas med en noll **errorlevel**, instansen kommer att blockeras. Efterföljande **enkel** startåtgärder och rollen, startar inte.
  > 
  > 
  
    Att säkerställa att din batchfil som slutar med en **errorlevel** noll, kör du kommandot `EXIT /B 0` i slutet av din fil batchprocess.
* **background**  
  Åtgärder körs asynkront, parallellt med start i rollen.
* **förgrund**  
  Åtgärder körs asynkront, parallellt med start i rollen. Den viktigaste skillnaden mellan en **förgrunden** och en **bakgrund** uppgift är att en **förgrunden** uppgift förhindrar roll från återvinning eller stänga av förrän den har avslutats. Den **bakgrund** uppgifter har inte den här begränsningen.

## <a name="environment-variables"></a>Miljövariabler
Miljövariabler är ett sätt att skicka information till en startåtgärd. Du kan till exempel ange sökvägen till en blob som innehåller ett program att installera, eller portnummer som ska använda för din roll eller inställningar för att styra funktioner på dina startåtgärd.

Det finns två typer av miljövariabler för startåtgärder; statisk miljövariabler och miljövariabler baserat på medlemmar i den [RoleEnvironment] klass. Båda finns i den [miljö] delen av den [ServiceDefinition.csdef] fil- och båda använda den [Variabel] element och **namn** attributet.

Statisk miljövariabler använder den **värdet** attributet för den [Variabel] element. Exemplet ovan skapar miljövariabeln **MyVersionNumber** som har ett statiskt värde för ”**1.0.0.0**”. Ett annat exempel är att skapa en **StagingOrProduction** miljövariabeln som du kan manuellt lägga till värden för ”**mellanlagring**” eller ”**produktion**” att utföra olika startåtgärderna baserat på värdet för den **StagingOrProduction** miljövariabeln.

Använd inte miljövariabler baserat på medlemmar i klassen RoleEnvironment den **värdet** attributet för den [Variabel] element. I stället den [RoleInstanceValue] underordnat element med lämplig **XPath** attributvärde, används för att skapa en miljövariabel som baseras på en viss medlem i den [RoleEnvironment] klass. Värden för den **XPath** attribut för att få åtkomst till olika [RoleEnvironment] värden finns [här](cloud-services-role-config-xpath.md).

Till exempel vill skapa en miljövariabel som är ”**SANT**” när instansen körs i beräkningsemulatorn, och ”**FALSKT**” vid körning i molnet kan du använda följande [Variabel] och [RoleInstanceValue] element:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Nästa steg
Lär dig hur du utför några [vanliga startuppgifter](cloud-services-startup-tasks-common.md) med Molntjänsten.

[Paketet](cloud-services-model-and-package.md) din molntjänst.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Aktivitet]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Start]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Miljö]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variabel]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
