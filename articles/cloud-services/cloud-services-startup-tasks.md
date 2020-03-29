---
title: Köra startuppgifter i Azure Cloud Services | Microsoft-dokument
description: Startuppgifter hjälper till att förbereda din molntjänstmiljö för din app. Detta lär dig hur startuppgifter fungerar och hur du gör dem
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: fa48953e5e86ffa758fe556b7fb1072be9d74647
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360318"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Konfigurera och köra startuppgifter för en molntjänst
Du kan använda startuppgifter för att utföra åtgärder innan en roll startar. Åtgärder som du kanske vill utföra är att installera en komponent, registrera COM-komponenter, ange registernycklar eller starta en tidskrävande process.

> [!NOTE]
> Startuppgifter är inte tillämpliga på virtuella datorer, bara på Molntjänstwebb- och arbetarroller.
> 
> 

## <a name="how-startup-tasks-work"></a>Så här fungerar startuppgifter
Startaktiviteter är åtgärder som vidtas innan dina roller börjar och definieras i filen [ServiceDefinition.csdef] med hjälp av [aktivitetselementet] i [startelementet.] Ofta är startuppgifter batchfiler, men de kan också vara konsolprogram eller kommandofiler som startar PowerShell-skript.

Miljövariabler skickar information till en startuppgift och lokal lagring kan användas för att skicka information från en startuppgift. En miljövariabel kan till exempel ange sökvägen till ett program som du vill installera och filer kan skrivas till lokal lagring som sedan kan läsas senare av dina roller.

Startuppgiften kan logga information och fel i **TEMP** den katalog som anges av TEMP-miljövariabeln. Under startaktiviteten **TEMP** matchas TEMP-miljövariabeln till *C:\\Resources\\temp\\[guid].[ rolename]\\RoleTemp-katalog* när du kör på molnet.

Startåtgärder kan även köras flera gånger mellan omstarter. Startåtgärden körs till exempel varje gång rollen återanvänds och rollåteranvändningar kanske inte alltid innefattar en omstart. Startuppgifter bör skrivas på ett sätt som gör att de kan köras flera gånger utan problem.

Startaktiviteter måste avslutas med en **felnivå** (eller avslutningskod) på noll för att startprocessen ska slutföras. Om en startaktivitet avslutas med en **felnivå**som inte är noll startar inte rollen.

## <a name="role-startup-order"></a>Startordning för roll
Följande listar rollstartproceduren i Azure:

1. Instansen är markerad som **Start** och tar inte emot trafik.
2. Alla startuppgifter körs enligt attributet **TaskType.**
   
   * De **enkla** uppgifterna utförs synkront, en i taget.
   * **Bakgrunds-** och **förgrundsaktiviteterna** startas asynkront, parallellt med startaktiviteten.  
     
     > [!WARNING]
     > IIS kanske inte är helt konfigurerat under startaktivitetsfasen i startprocessen, så rollspecifika data kanske inte är tillgängliga. Startuppgifter som kräver rollspecifika data bör använda [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. Rollvärdprocessen startas och platsen skapas i IIS.
4. [Metoden Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) anropas.
5. Förekomsten markeras som **Klar** och trafiken dirigeras till instansen.
6. [Metoden Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](/previous-versions/azure/reference/ee772746(v=azure.100)) anropas.

## <a name="example-of-a-startup-task"></a>Exempel på en startuppgift
Startuppgifter definieras i filen [ServiceDefinition.csdef] i **aktivitetselementet.** **Attributet commandLine** anger namn och parametrar för startbatchfilen eller konsolkommandot, **attributet executionContext** anger behörighetsnivån för startuppgiften och **taskType-attributet** anger hur aktiviteten ska utföras.

I det här exemplet skapas en miljövariabel, **MyVersionNumber,** för startaktiviteten och anges till värdet "**1.0.0.0**".

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

I följande exempel skriver **batchfilen Startup.cmd** raden "Den aktuella versionen är 1.0.0.0" till startuplog.txt-filen i katalogen som anges av TEMP-miljövariabeln. Raden `EXIT /B 0` säkerställer att startaktiviteten slutar med en **felnivå på** noll.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> I Visual Studio ska egenskapen **Kopiera till utdatakatalog** för startbatchfilen ställas in på **Kopiera alltid** för att vara säker på att startbatchfilen har distribuerats korrekt till projektet på Azure (**\\approtslagerplats** för webbroller och **approt** för arbetsroller).
> 
> 

## <a name="description-of-task-attributes"></a>Beskrivning av uppgiftsattribut
Nedan beskrivs attributen för **Task** aktivitetselementet i filen [ServiceDefinition.csdef:]

**commandLine** - Anger kommandoraden för startuppgiften:

* Kommandot, med valfria kommandoradsparametrar, som startar startuppgiften.
* Ofta är detta filnamnet för en .cmd- eller BAT-kommandofil.
* Uppgiften är relativ till\\mappen AppRoot Bin för distributionen. Miljövariablera expanderas inte vid fastställandet av aktivitetens sökväg och fil. Om det krävs miljöexpansion kan du skapa ett litet CMD-skript som anropar startuppgiften.
* Kan vara ett konsolprogram eller en kommandofil som startar ett [PowerShell-skript](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** - Anger behörighetsnivån för startuppgiften. Behörighetsnivån kan begränsas eller höjas:

* **Begränsad**  
  Startaktiviteten körs med samma behörighet som rollen. När **attributet executionContext** för [runtime-elementet] också är **begränsat**används användarbehörighet.
* **Förhöjda**  
  Startuppgiften körs med administratörsbehörighet. På så sätt kan startuppgifter installera program, göra IIS-konfigurationsändringar, utföra registerändringar och andra uppgifter på administratörsnivå, utan att öka behörighetsnivån för själva rollen.  

> [!NOTE]
> Förmånsnivån för en startuppgift behöver inte vara samma som själva rollen.
> 
> 

**taskType** - Anger hur en startaktivitet körs.

* **Enkel**  
  Aktiviteter utförs synkront, en i taget, i den ordning som anges i filen [ServiceDefinition.csdef.] När en **enkel** startuppgift avslutas med en **felnivå på** noll körs nästa **enkla** startuppgift. Om det inte finns några fler **enkla** startuppgifter att utföra startas själva rollen.   
  
  > [!NOTE]
  > Om den **enkla** aktiviteten slutar med en **felnivå**som inte är noll blockeras instansen. Efterföljande **enkla** startuppgifter och själva rollen startar inte.
  > 
  > 
  
    Om du vill vara säkra på att kommandofilen slutar `EXIT /B 0` med en **felnivå** på noll kör du kommandot i slutet av batchfilprocessen.
* **Bakgrund**  
  Aktiviteter utförs asynkront, parallellt med starten av rollen.
* **Förgrunden**  
  Aktiviteter utförs asynkront, parallellt med starten av rollen. Den viktigaste skillnaden mellan en **förgrunds-** och en **bakgrundsaktivitet** är att en **förgrundsaktivitet** förhindrar att rollen återvinns eller stängs av tills aktiviteten har avslutats. **Bakgrundsaktiviteterna** har inte den här begränsningen.

## <a name="environment-variables"></a>Miljövariabler
Miljövariabler är ett sätt att skicka information till en startuppgift. Du kan till exempel placera sökvägen till en blob som innehåller ett program som ska installeras, eller portnummer som din roll ska använda, eller inställningar för att styra funktionerna i startuppgiften.

Det finns två typer av miljövariabler för startaktiviteter. statiska miljövariabler och miljövariabler baserat på medlemmar i [klassen RoleEnvironment.] Båda finns i avsnittet [Miljö] i filen [ServiceDefinition.csdef] och båda använder [attributet Variabel] och **namn.**

Statiska miljövariabler använder **värdeattributet** för [variabelelementet.] Exemplet ovan skapar miljövariabeln **MyVersionNumber** som har ett statiskt värde på "**1.0.0.0**". Ett annat exempel är att skapa en **miljövariabel för StagingOrProduction** som du manuellt kan ange till värden för "**mellanlagring**" eller "**produktion**" för att utföra olika startåtgärder baserat på värdet för miljövariabeln **StagingOrProduction.**

Miljövariabler baserade på medlemmar i klassen RoleEnvironment använder inte **värdeattributet** för [variabelelementet.] I stället används [det underordnade elementet RoleInstanceValue,] med rätt **XPath-attributvärde,** för att skapa en miljövariabel baserat på en specifik medlem i [klassen RoleEnvironment.] Värden för **XPath-attributet** för att komma åt olika [rollmiljövärden] finns [här](cloud-services-role-config-xpath.md).

Om du till exempel vill skapa en miljövariabel som är "**true**" när förekomsten körs i beräkningsemulatorn och "**false**" när du kör i molnet använder du följande [variableelement] och [RoleInstanceValue:]

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
Läs om hur du utför några [vanliga startuppgifter](cloud-services-startup-tasks-common.md) med din Molntjänst.

[Paketera](cloud-services-model-and-package.md) din molntjänst.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Aktivitet]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Start]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Körmiljö]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Miljö]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variabel]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RollInstanceVärde]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RollMiljö]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx



