---
title: "Köra uppgifter för start i Azure-molntjänster | Microsoft Docs"
description: "Start hjälper förbereda din molntjänstmiljö för din app. Det här lär du dig hur start aktiviteter fungerar och hur du gör dem"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 886939be-4b5b-49cc-9a6e-2172e3c133e9
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 1c1b3aa86dc8211de0c07c9fb68da5685c86f551
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Konfigurera och köra startade aktiviteter för en tjänst i molnet
Du kan använda Start uppgifter för att utföra åtgärder innan du startar en roll. Åtgärder som du kanske vill utföra inkluderar installerar en komponent, registrerar COM-komponenter, ange registernycklar eller starta en tidskrävande process.

> [!NOTE]
> Start-aktiviteter kan inte användas för virtuella datorer, endast för Cloud Service webb- och arbetsroller.
> 
> 

## <a name="how-startup-tasks-work"></a>Så här fungerar Start uppgifter
Start-aktiviteter är åtgärder som vidtas innan dina roller börjar och definieras i den [ServiceDefinition.csdef] filen med hjälp av den [aktivitet] element i den [Start] element. Uppgifter för start är vanliga batch-filer, men de kan även vara konsolprogram eller kommandofiler som startar PowerShell-skript.

Miljövariabler skickar information till en startåtgärd och lokal lagring kan användas för att överföra information från en startaktivitet. Till exempel en miljövariabel kan ange sökvägen till ett program som du vill installera och filerna skrivs till lokal lagring som kan läsas senare sedan av rollerna.

Din startaktivitet kan logga information och fel i katalogen som anges av den **TEMP** miljövariabeln. Under startaktivitet, den **TEMP** miljövariabeln matchar den *C:\\resurser\\temp\\[guid]. [ rolename]\\RoleTemp* katalogen när den körs i molnet.

Start-aktiviteter kan också köras flera gånger mellan olika omstarter. Till exempel startaktivitet ska köras varje gång rollen återanvänds och rollen återanvänder kanske inte alltid med en omstart. Start uppgifter ska skrivas på ett sätt som gör att de kan köras flera gånger utan problem.

Start-aktiviteter måste avslutas med en **errorlevel** (eller slutkod) noll att slutföra startprocessen. Om en startåtgärd slutar med en icke-noll **errorlevel**, startar inte rollen.

## <a name="role-startup-order"></a>Rollen startordningen
I följande lista visas under rollen startade i Azure:

1. Instansen är markerad som **Start** och tar inte emot trafik.
2. Alla Start-aktiviteter utförs enligt deras **taskType** attribut.
   
   * Den **enkel** åtgärder körs synkront, en i taget.
   * Den **bakgrund** och **förgrunden** uppgifter startas asynkront, parallell Start-aktivitet.  
     
     > [!WARNING]
     > IIS kan inte konfigureras fullständigt startade uppgiften etapp i startprocessen, så rollspecifika data är inte kanske tillgänglig. Start-uppgifter som kräver att rollspecifika data ska använda [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).
     > 
     > 
3. Värdprocess för rollen har startats och att webbplatsen har skapats i IIS.
4. Den [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) metoden anropas.
5. Instansen är markerad som **klar** och trafik dirigeras till instansen.
6. Den [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoden anropas.

## <a name="example-of-a-startup-task"></a>Exempel på en startåtgärd
Start-aktiviteter har definierats i den [ServiceDefinition.csdef] filen den **aktivitet** element. Den **commandLine** attributet anger namnet och parametrarna Start batch-fil eller konsolen kommandot den **executionContext** attributet anger behörighetsnivå för startaktivitet, och **taskType** attributet anger hur aktiviteten ska köras.

I det här exemplet, en miljövariabel **MyVersionNumber**, skapas för startaktivitet och ange värde för ”**1.0.0.0**”.

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

I följande exempel visas den **Startup.cmd** kommandofil skriver raden ”den aktuella versionen är 1.0.0.0” i filen StartupLog.txt i katalogen som anges av miljövariabeln TEMP. Den `EXIT /B 0` rad säkerställer att aktiviteten startade slutar med en **errorlevel** noll.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> I Visual Studio den **kopiera till utdatakatalog** -egenskapen för kommandofilen start ska anges till **Kopiera alltid** för att kontrollera att kommandofilen startades korrekt har distribuerats till ditt projekt i Azure (**approot\\bin** för Web-roller och **approot** för arbetsroller).
> 
> 

## <a name="description-of-task-attributes"></a>Beskrivning av uppgiften attribut
Nedan beskrivs attributen för den **aktivitet** element i den [ServiceDefinition.csdef] fil:

**commandLine** -kommandoraden för startade uppgiften:

* Kommandot med valfria kommandoradsparametrar, vilket startar aktiviteten startades.
* Det här är ofta filnamnet på en batchfil .cmd och .bat.
* Aktiviteten är i förhållande till AppRoot\\Bin-mappen för distributionen. Miljövariabler är inte expanderas för att fastställa sökvägen och filnamnet för aktiviteten. Du kan skapa en liten cmd-skript som anropar din startaktivitet om miljön expansion krävs.
* Kan vara ett konsolprogram eller en batchfil som startar en [PowerShell-skriptet](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** -anger behörighetsnivå för aktiviteten startades. En behörighetsnivå kan begränsad eller utökade:

* **begränsad**  
  Start-aktiviteten körs med samma behörigheter som rollen. När den **executionContext** attributet för den [Runtime] element är också **begränsad**, och sedan användarprivilegier som används.
* **upphöjd**  
  Start-aktiviteten körs med administratörsbehörighet. Detta gör start av aktiviteter som ska installera program, ändra IIS-konfiguration, göra ändringar i registret och andra nivån administratörsåtgärder, utan att öka behörighetsnivå i rollen sig själv.  

> [!NOTE]
> Behörighetsnivå för en startåtgärd behöver inte vara samma som rollen sig själv.
> 
> 

**taskType** – anger hur en startaktivitet körs.

* **enkel**  
  Åtgärder körs synkront, en i taget i den ordning som anges i den [ServiceDefinition.csdef] fil. När en **enkel** startaktivitet slutar med en **errorlevel** noll, nästa **enkel** startaktivitet körs. Om det inte finns några **enkel** Start aktiviteter att köra, och sedan startas rollen sig själv.   
  
  > [!NOTE]
  > Om den **enkel** aktivitet slutar med en icke-noll **errorlevel**, instansen kommer att blockeras. Efterföljande **enkel** Start uppgifter och rollen, startar inte.
  > 
  > 
  
    Att säkerställa att kommandofilen slutar med en **errorlevel** noll, kör du kommandot `EXIT /B 0` i slutet av din fil batchprocess.
* **bakgrund**  
  Åtgärder körs asynkront, parallellt med start i rollen.
* **förgrunden**  
  Åtgärder körs asynkront, parallellt med start i rollen. Den viktigaste skillnaden mellan en **förgrunden** och en **bakgrund** uppgift är att en **förgrunden** aktivitet förhindrar rollen från återvinning eller avslutas förrän den har avslutats. Den **bakgrund** uppgifter har inte den här begränsningen.

## <a name="environment-variables"></a>Miljövariabler
Miljövariabler är ett sätt att skicka information till en startaktivitet. Du kan till exempel placera sökvägen till en blob som innehåller ett program ska installeras, eller portnummer som används i din roll eller inställningar som styr funktioner på din startaktivitet.

Det finns två typer av miljövariabler för start aktiviteter. statisk miljövariabler och miljövariabler baserat på medlemmar i den [ RoleEnvironment] klass. Båda värdena i den [miljö] avsnitt i den [ServiceDefinition.csdef] fil- och båda använda den [variabeln] element och **namn** attribut.

Statisk miljövariabler använder den **värdet** attribut för den [variabeln] element. Exemplet ovan skapar miljövariabeln **MyVersionNumber** som har ett statiskt värde för ”**1.0.0.0**”. Ett annat exempel är att skapa en **StagingOrProduction** miljövariabel som du kan manuellt lägga till värden ”**mellanlagring**” eller ”**produktion**” att utföra olika start åtgärder baserat på värdet för den **StagingOrProduction** miljövariabeln.

Använd inte miljövariabler som baseras på medlemmar i klassen RoleEnvironment den **värdet** attribut för den [variabeln] element. I stället den [RoleInstanceValue] underordnat element med lämplig **XPath** attributvärdet, används för att skapa en miljövariabel baserat på en viss medlem i den [ RoleEnvironment] klass. Värden för den **XPath** attribut för att få åtkomst till olika [ RoleEnvironment] värden finns [här](cloud-services-role-config-xpath.md).

Till exempel för att skapa en miljövariabel som är ”**SANT**” när instansen körs i beräkningsemulatorn, och ”**FALSKT**” när den körs i molnet, använder du följande [variabeln] och [RoleInstanceValue] element:

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
Lär dig hur du utför några [vanliga uppgifter för Start](cloud-services-startup-tasks-common.md) med Molntjänsten.

[Paketet](cloud-services-model-and-package.md) Molntjänsten.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[aktivitet]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Start]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[miljö]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[variabeln]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[ RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
