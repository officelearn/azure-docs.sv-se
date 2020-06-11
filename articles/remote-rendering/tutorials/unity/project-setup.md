---
title: Konfigurera ett Unity-projekt från grunden
description: Förklarar hur du konfigurerar ett tomt Unit-projekt för användning med Azure Remote rendering.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: c05daa998829c4ac0687f75ae5678695127a50b0
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659911"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Självstudie: Konfigurera ett Unity-projekt från grunden

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Konfigurera ett Scratch Unity-projekt för ARR.
> * Skapa och stoppa åter givnings sessioner.
> * Återanvända befintliga sessioner.
> * Ansluta och koppla från sessioner.
> * Läser in modeller till en åter givnings session.
> * Visar anslutnings statistik.

## <a name="prerequisites"></a>Krav

För den här självstudien behöver du:

* Din konto information (konto-ID, konto nyckel, prenumerations-ID). [Skapa ett konto](../../how-tos/create-an-account.md)om du inte har något konto.
* Windows SDK 10.0.18362.0 [(Hämta)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Den senaste versionen av Visual Studio 2019 [(Hämta)](https://visualstudio.microsoft.com/vs/older-downloads/). 
* [Visual Studio Tools för Mixad verklighet](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). Mer specifikt är följande *arbets belastnings* installationer obligatoriska:
  * **Skriv bords utveckling med C++**
  * **Universell Windows-plattform (UWP) utveckling**
* GIT [(nedladdning)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(Hämta)](https://unity3d.com/get-unity/download)
  * Installera de här modulerna i Unity:
    * **UWP** -stöd för Build-universell Windows-plattform
    * **IL2CPP** – stöd för Windows-build (IL2CPP)

> [!TIP]
> [Databasen arr samples](https://github.com/Azure/azure-remote-rendering) innehåller för beredda Unity-projekt för alla självstudier. Du kan använda dessa projekt som referens.

## <a name="create-a-new-unity-project"></a>Skapa ett nytt Unity-projekt

Skapa ett nytt projekt från Unity Hub.
I det här exemplet antar vi att projektet skapas i en mapp med namnet `RemoteRendering` .

![nytt projekt fönster](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Konfigurera projektets manifest

Du måste ändra filen som finns `Packages/manifest.json` i din Unity Project-mapp. Öppna filen i en text redigerare och Lägg till raderna i listan nedan:

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

Paketet för Universal rendering-pipeline är valfritt men rekommenderas av prestanda skäl.
När du har ändrat och sparat manifestet uppdateras Unity automatiskt. Bekräfta att paketen har lästs in i *projekt* fönstret:

![bekräfta paket importer](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Se till att du har den senaste versionen av paketet

Följande steg säkerställer att ditt projekt använder den senaste versionen av Remote rendering-paketet.
1. Välj paketet i projekt fönstret och klicka på :::no-loc text="package"::: ikonen: ![ välja paket ikonen](media/package-icons.png)
1. I kontrollen klickar du på "Visa i paket hanteraren": ![ paket kontroll](media/package-properties.png)
1. På sidan Package Manager för Remote rendering Package, se om uppdaterings knappen är tillgänglig. Om så är fallet uppdaterar paketet till den senaste tillgängliga versionen: ![ arr-paketet i paket hanteraren](media/package-manager.png)
1. Ibland kan det leda till fel i-konsolen vid uppdatering av paketet. Om detta inträffar kan du försöka stänga och öppna projektet igen.

## <a name="configure-the-camera"></a>Konfigurera kameran

Välj den **primära Camera** -noden.

1. Återställ dess *transformering*:

    ![Återställ kamera omvandling](media/camera-reset-transform.png)

1. Ange **:::no-loc text="Clear flags":::** till*:::no-loc text="Solid Color":::*

1. Ange **:::no-loc text="Background":::** till*:::no-loc text="Black":::*

1. Ange **:::no-loc text="Clipping Planes":::** till *nära = 0,3* och *mycket = 20*. Det innebär att rendera kommer att vara en klipps geometri som är närmare än 30 cm eller längre än 20 meter.

    ![Egenskaper för Unity-kamera](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Justera projekt inställningarna

1. Öppna *redigera > projekt inställningar...*
1. I listan till vänster väljer du kvalitet.
1. Ändra den **aktiva kvalitets nivån** till *låg* genom att markera den
1. Ändra **standard kvalitets nivån** till *låg*

    ![Ändra inställningar för projekt kvalitet](media/settings-quality.png)

1. Välj **grafik** till vänster.
1. Ändra inställningen för inställnings **pipelinen för skript åter givning** till *HybridRenderingPipeline*. Hoppa över det här steget om den universella rendera pipelinen inte används.

    ![ändra inställningarna för Project Graphics ](media/settings-graphics-lwrp.png) ibland-gränssnittet fyller inte i listan över tillgängliga pipelin typer från paketen. i så fall måste *HybridRenderingPipeline* -till gången dras till fältet manuellt: ![ ändra inställningarna för Project-grafik](media/hybrid-rendering-pipeline.png)
1. Välj **spelare** till vänster.
1. Välj fliken **universell Windows-plattform inställningar**
1. Ändra **XR-inställningarna** så att de stöder Windows Mixed Reality: ![ Player-inställningar](media/xr-player-settings.png)
1. Välj inställningarna som i skärm bilden ovan:
    1. Aktivera **virtuell verklighet som stöds**
    1. Ange **djup formatet** *16-bitars djup*
    1. Aktivera **delning av djup buffert**
    1. Ställ in **stereo åter givnings läge** på *en enskild pass instans*

1. I samma fönster, över *XR-inställningar*, expanderar du **publicerings inställningar**
1. Rulla ned till **funktioner** och välj:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * Valfritt för utveckling: **PrivateNetworkClientServer**

      Det här alternativet behövs om du vill ansluta enhetens fjärrfelsökning till enheten.

1. I **enhets familjer som stöds**aktiverar du **Holographic** och **Desktop**

1. Om du vill använda verktyget för Mixad verklighet läser du [MRTK-dokumentationen](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview)för mer information om rekommenderade inställningar och funktioner.

## <a name="validate-project-setup"></a>Verifiera projekt konfiguration

Utför följande steg för att kontrol lera att projekt inställningarna är korrekta.

1. Välj posten ValidateProject på RemoteRendering-menyn i verktygsfältet i Unity Editor.
1. Använd fönstret ValidateProject för att söka efter och åtgärda projekt inställningar där det behövs.

    ![Project-validering av Unit Editor](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Skapa ett skript för att initiera Azure Remote rendering

Skapa ett [nytt skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) och ge det namnet **RemoteRendering**. Öppna skript filen och ersätt hela innehållet med koden nedan:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

Det här skriptet initierar Azure-fjärrrendering, anger vilket Camera-objekt som ska användas för åter givning och placerar knappen **Skapa session** i visnings området när *uppspelnings läget* är aktiverat.

> [!CAUTION]
> Att ändra skriptet och spara det medan uppspelnings läget är aktivt i Unity kan resultera i uppsatta upplåsningar och du tvingas stänga ned det via aktivitets hanteraren. Stoppa därför alltid uppspelnings läget innan du redigerar *RemoteRendering* -skriptet.

## <a name="test-azure-remote-rendering-session-creation"></a>Testa genereringen av Azure fjärrrendering-sessionen

Skapa en ny GameObject i scenen och Lägg till *RemoteRendering* -komponenten till den. Fyll i lämplig *konto domän*, *konto-ID*och *konto nyckel* för fjärråter givnings kontot:

![Egenskaper för fjärråter givnings komponent](media/remote-rendering-component.png)

Starta programmet i redigeraren (**Tryck på Play** eller Ctrl + P). Du bör se knappen **Skapa session** visas i visnings området. Starta din första ARR-session genom att klicka på den:

![Skapa en första session](media/test-create.png)

Om detta Miss lyckas, se till att du har angett konto informationen korrekt i egenskaperna för RemoteRendering-komponenten. Annars visas ett meddelande i konsol fönstret som visar det sessions-ID som tilldelats dig och som anger att sessionen för närvarande är i *Start* läge:

![Session som startar utdata](media/create-session-output.png)

I det här läget har Azure etablering av en server för dig och start av en virtuell dator för virtuell åter givning. Detta tar vanligt vis **3 till 5 minuter**. När den virtuella datorn är klar körs vårt Unity-skript `OnSessionStatusChanged` återanrop och kommer att skriva ut den nya sessionens status:

![Redo utdata för session](media/create-session-output-2.png)

Detta! För tiden är inget fler att ske. För att förhindra avgifter bör du alltid stoppa sessioner när de inte behövs längre. I det här exemplet kan du antingen göra detta genom att klicka på knappen **Stoppa session** eller genom att stoppa Unity-simuleringen. På grund av egenskapen **auto-stop session** på *ARRServiceUnity* -komponenten, som är aktive ras, stoppas sessionen automatiskt åt dig. Om allting Miss lyckas, på grund av krascher eller anslutnings problem, kan sessionen köras så länge din *MaxLeaseTime* innan den stängs av av servern.

> [!NOTE]
> Att stoppa en session börjar gälla omedelbart och kan inte återställas. När du har stoppat måste du skapa en ny session med samma start kostnad.

## <a name="reusing-sessions"></a>Återanvända sessioner

Att skapa en ny session är tyvärr en tids krävande åtgärd. Därför bör ett försök att skapa sessioner sällan och återanvända dem när det är möjligt.

Infoga följande kod i *RemoteRendering* -skriptet och ta bort de gamla versionerna av de duplicerade funktionerna:

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> Innan du kör den här koden ser du till att inaktivera alternativet för att **stoppa automatiskt** i RemoteRendering-komponenten. Annars stoppas varje session som du skapar automatiskt när du stoppar simuleringen och om du försöker att återanvända den kommer att Miss lyckas.

När du trycker på *Play*får du nu tre knappar i visnings området: **Skapa session**, **fråga aktiva sessioner**och **Använd den befintliga sessionen**. Den första knappen skapar alltid en ny session. Den andra knappen frågar vilka *aktiva* sessioner som finns. Om du inte manuellt angett ett sessions-ID för att försöka använda, kommer den här åtgärden automatiskt att välja sessions-ID för framtida användning. Den tredje knappen försöker ansluta till en befintlig session. Antingen en som du angav manuellt via komponent egenskapen *sessions-ID* , eller en som upptäckts av *fråga aktiva sessioner*.

Funktionen **AutoStartSessionAsync** används för att simulera knappens tryck utanför redigeraren.

> [!TIP]
> Det går att öppna sessioner som har stoppats, som har upphört att gälla eller som är i fel tillstånd. Även om de inte kan användas för rendering längre, kan du fråga efter information när du har öppnat en inaktiv session. Koden ovan kontrollerar en sessions status i `ARRService_OnSessionStarted` , för att stoppa automatiskt när sessionen har blivit oanvändbar.

Med den här funktionen kan du nu skapa och återanvända sessioner, vilket bör förbättra ditt utvecklings arbets flöde avsevärt.

Normalt utlöses skapandet av en session utanför klient programmet på grund av den tid det tar att sätta upp servern.

## <a name="connect-to-an-active-session"></a>Ansluta till en aktiv session

Hittills har vi skapat eller öppnat sessioner. Nästa steg är att *ansluta* till en session. När du är ansluten skapar åter givnings servern avbildningar och skickar en video ström till vårt program.

Infoga följande kod i *RemoteRendering* -skriptet och ta bort de gamla versionerna av de duplicerade funktionerna:

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

Testa den här funktionen:

1. Tryck på **Play** i Unity.
1. Öppna en session:
    1. Om du redan har en session trycker du på **fråga aktiva sessioner** och **använder sedan den befintliga sessionen**.
    1. Annars trycker du på **Skapa session**.
1. Tryck på **Anslut**.
1. Efter några sekunder bör konsolens utdata skrivas ut som du är ansluten.
1. För närvarande bör inget annat inträffa.
1. Tryck på **Koppla från** eller stoppa uppunionens uppspelnings läge.

>[!NOTE]
> Flera användare kan *Öppna* en session för att fråga dess information, men bara en användare kan vara *ansluten* till en session i taget. Om en annan användare redan är ansluten fungerar inte anslutningen med ett **hand skaknings fel**.

## <a name="load-a-model"></a>Läs in en modell

Infoga följande kod i *RemoteRendering* -skriptet och ta bort de gamla versionerna av de duplicerade funktionerna:

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

När du nu trycker på Play, öppnar en session och ansluter till den, visas knappen **Läs in modell** . När du har klickat på den visas inläsnings förloppet i konsolens utdata och när den når 100% bör modellen för en motor visas:

![Modell som läses in i redigeraren](media/model-loaded-replace-me.png)

[WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) är en viktig komponent som används för [hologram stabilitet](https://docs.microsoft.com/windows/mixed-reality/hologram-stability). Den kommer dock endast att ha en inverkan när den distribueras på en enhet med Mixad verklighet.

> [!TIP]
> Om du följde [snabb starten: konvertera en modell för åter givning](../../quickstarts/convert-model.md)vet du redan hur du konverterar dina egna modeller. Allt du behöver göra nu för att rendera det, är att skicka URI till en konverterad modell till egenskapen *modell namn* .

## <a name="display-frame-statistics"></a>Visa ram statistik

Azure-fjärrrenderingen spårar olika uppgifter om anslutningens kvalitet. För ett snabbt sätt att visa den här informationen gör du följande:

Skapa ett [nytt skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) och ge det namnet **RemoteFrameStats**. Öppna skript filen och ersätt hela innehållet med koden nedan:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

Skapa en GameObject och ge den namnet *FrameStats*. Koppla den som en underordnad nod till *huvud kamerans* objekt och ange dess position till **x = 0, y = 0, z = 0,325**. Lägg till **RemoteFrameStats** -komponenten i objektet.

Lägg till ett **användar gränssnitt >** ett underordnat objekt för arbets ytan till *FrameStats* -objektet och ange dess egenskaper så här:

![egenskaper för arbets yta](media/framestats-canvas.png)

Lägg till ett **gränssnitt > text** objekt som underordnat arbets ytan och ange dess egenskaper så här:

![text egenskaper](media/framestats-text.png)

Välj *FrameStats* -objektet och fyll i **fältet FrameStats** genom att klicka på cirkel-ikonen och markera objektet **text** :

![anger text egenskap](media/framestats-set-text.png)

När du nu är ansluten till fjärrsessionen ska texten Visa strömmande statistik:

![Frame statistik-utdata](media/framestats-output.png)

Koden inaktiverar statistik uppdateringen utanför redigeraren som en huvud låst text ruta skulle vara störande. En mer avancerad implementering finns i [snabb starts](../../quickstarts/render-model.md) projektet.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig alla steg som krävs för att ta ett tomt Unity-projekt och få det att arbeta med Azure Remote rendering. I nästa självstudie tar vi en närmare titt på hur du arbetar med fjärrentiteter.

> [!div class="nextstepaction"]
> [Självstudie: arbeta med fjär entiteter i enhet](working-with-remote-entities.md)
