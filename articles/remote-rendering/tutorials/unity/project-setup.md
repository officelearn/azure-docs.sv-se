---
title: Ställa in ett Unity-projekt från grunden
description: I artikeln beskrivs hur du konfigurerar ett tomt Unity-projekt för användning med Azure Remote Rendering.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 33801316e4c0446865169560bb42f98052acba70
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679599"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Självstudiekurs: Ställa in ett Unity-projekt från grunden

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Konfigurera ett scratch Unity-projekt för ARR.
> * Skapa och stoppa renderingssessioner.
> * Återanvända befintliga sessioner.
> * Ansluta och koppla från sessioner.
> * Laddar modeller i en renderingssession.
> * Visar anslutningsstatistik.

## <a name="prerequisites"></a>Krav

För den här guiden behöver du:

* Din kontoinformation (konto-ID, kontonyckel, prenumerations-ID). Om du inte har ett konto [skapar du ett konto](../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(hämta)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Den senaste versionen av Visual Studio 2019 [(hämtad)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(ladda ner)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(ladda ner)](https://unity3d.com/get-unity/download)
  * Installera dessa moduler i Unity:
    * **UWP** – Stöd för universal windows-plattformsbygge
    * **IL2CPP** - Stöd för Windows Build (IL2CPP)

> [!TIP]
> [ARR-exempeldatabasen](https://github.com/Azure/azure-remote-rendering) innehåller förberedda Unity-projekt för alla självstudier. Du kan använda dessa projekt som referens.

## <a name="create-a-new-unity-project"></a>Skapa ett nytt Unity-projekt

Skapa ett nytt projekt från Unity Hub.
I det här exemplet antar vi att projektet skapas i en mapp som heter `RemoteRendering`.

![nytt projektfönster](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Konfigurera projektets manifest

Du måste ändra `Packages/manifest.json` filen som finns i enhetsprojektmappen. Öppna filen i en textredigerare och lägg till raderna nedan:

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

Det universella renderingspipellinepaketet är valfritt men rekommenderas av prestandaskäl.
När du har ändrat och sparat manifestet uppdateras Unity automatiskt. Bekräfta att paketen har lästs in i *projektfönstret:*

![bekräfta paketimport](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Se till att du har den senaste versionen av paketet

Följande steg säkerställer att projektet använder den senaste versionen av fjärrrenderingspaketet.
1. Välj paketet i projektfönstret och klicka på ![paketikonen: Välja paketikonen](media/package-icons.png)
1. Klicka på "Visa i pakethanteraren" ![i inspektören: paketinspektör](media/package-properties.png)
1. På sidan pakethanterare för fjärrrenderingspaket, se om uppdateringsknappen är tillgänglig. Om så är fallet uppdateras paketet till den senaste tillgängliga versionen genom att klicka på det: ![ARR-paketet i pakethanteraren](media/package-manager.png)
1. Ibland kan uppdatering av paketet leda till fel i konsolen. Om detta inträffar kan du prova att stänga och öppna projektet igen.

## <a name="configure-the-camera"></a>Konfigurera kameran

Välj noden **Huvudkamera.**

1. Återställ dess *transformering:*

    ![återställa kameratransformering](media/camera-reset-transform.png)

1. Ange **rensa flaggor** till *enfärgad*

1. Ange **bakgrund** till *svart*

1. Ställ in **urklippsplan** på *Nära = 0,3* och *Långt = 20*. Detta innebär rendering kommer klipp geometri som är närmare än 30 cm eller längre än 20 meter.

    ![Unity-kameraegenskaper](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Justera projektinställningarna

1. Öppna *Redigera > Projektinställningar...*
1. Välj Kvalitet i listan till vänster.
1. Ändra **standardkvalitetsnivån** till *låg*

    ![ändra inställningar för projektkvalitet](media/settings-quality.png)

1. Välj **Grafik** till vänster.
1. Ändra inställningen **skriptåtergivningspipeline** till *HybridRenderingPipeline*. Hoppa över det här steget om universalåtergivningspipelinen inte används.

    ![ändra inställningar](media/settings-graphics-lwrp.png) för projektgrafik Ibland fyller användargränssnittet inte i listan över tillgängliga pipelinetyper från paketen, i vilket fall HybridRenderingPipeline-tillgången måste dras in i fältet manuellt: ändra inställningar för projektgrafik från paketen, i vilket fall *HybridRenderingPipeline-tillgången* måste dras in i fältet manuellt: ![ändra inställningar för projektgrafik](media/hybrid-rendering-pipeline.png)
1. Välj **Spelare** till vänster.
1. Välj **fliken Inställningar för universella inställningar för Windows Platform**
1. Ändra **XR-inställningarna** för att ![stödja Windows Mixed Reality: spelarinställningar](media/xr-player-settings.png)
1. Välj inställningarna som i skärmbilden ovan:
    1. Aktivera **virtual reality-stöd**
    1. Ange **djupformat** till *16-bitars djup*
    1. Aktivera **djupbuffertdelning**
    1. Ange **stereorenderingsläge** på *single pass-instans*

1. Expandera **Publiceringsinställningarna** i samma fönster ovanför *XR-inställningar*
1. Bläddra ned till **Funktioner** och välj:
    * **InternetClient (InternetClient)**
    * **InternetClientServer**
    * **SpatialPerception**
    * Valfritt för utveckling: **PrivateNetworkClientServer**

      Det här alternativet behövs om du vill ansluta enhetsfjärrfelsökaren till enheten.

1. Aktivera **Holografiska** och **stationära enheter i** **enhetsfamiljer som stöds**

1. Om du vill använda Verktygslådan för mixed reality läser du [MRTK-dokumentationen](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview)för mer information om rekommenderade inställningar och funktioner.

## <a name="validate-project-setup"></a>Validera projektinställningar

Utför följande steg för att verifiera att projektinställningarna är korrekta.

1. Välj posten ValidateProject på Fjärrrenderingsmenyn i verktygsfältet Enhetsredigerare.
1. Använd fönstret Valideraprojekt för att kontrollera och åtgärda projektinställningarna där det behövs.

    ![Validering av unity-redigerare](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Skapa ett skript för att initiera Azure Remote Rendering

Skapa ett [nytt skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) och ge det namnet **RemoteRendering**. Öppna skriptfilen och ersätt hela innehållet med koden nedan:

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

Det här skriptet initierar Azure Remote Rendering, talar om för vilket kameraobjekt som ska användas för rendering och placerar en **knappen Skapa session** i visningsområdet när *uppspelningsläget* är aktiverat.

> [!CAUTION]
> Om du ändrar skriptet och sparar det medan uppspelningsläget är aktivt i Unity kan unity frysas och du tvingas stänga av det via Aktivitetshanteraren. Stoppa därför alltid uppspelningsläget innan du redigerar *RemoteRendering-skriptet.*

## <a name="test-azure-remote-rendering-session-creation"></a>Testa skapandet av Azure Remote Rendering-session

Skapa ett nytt GameObject i scenen och lägg till *RemoteRendering-komponenten* i den. Fyll i rätt *kontodomän,* *konto-ID*och *kontonyckel* för ditt fjärråtergivningskonto:

![Egenskaper för fjärrrenderingskomponent](media/remote-rendering-component.png)

Starta programmet i redigeraren (**tryck på Spela upp** eller CTRL+P). Du bör se knappen **Skapa session** visas i visningsområdet. Klicka på den för att starta din första ARR-session:

![Skapa en första session](media/test-create.png)

Om detta misslyckas kontrollerar du att du har angett dina kontouppgifter korrekt i egenskaperna för RemoteRendering-komponenten. Annars visas ett meddelande i konsolfönstret som visar det sessions-ID som tilldelats *Starting* dig och anger att sessionen för närvarande är i starttillståndet:

![Startutdata för session](media/create-session-output.png)

Nu etablerar Azure en server åt dig och startar en virtuell fjärrrenderingsdator. Detta tar vanligtvis **3 till 5 minuter.** När den virtuella datorn är klar `OnSessionStatusChanged` körs vårt Unity-skripts motringning och den nya sessionsstatusen skrivs ut:

![Sessionsklar utmatning](media/create-session-output-2.png)

Nu gäller det! För närvarande kommer inget mer att hända. För att förhindra avgifter bör du alltid stoppa sessioner när de inte behövs längre. I det här exemplet kan du antingen göra detta genom att klicka på knappen **Stoppa session** eller genom att stoppa Unity-simuleringen. På grund av egenskapen **Auto-Stop Session** på komponenten *ARRServiceUnity,* som är aktiverat som standard, stoppas sessionen automatiskt åt dig. Om allt misslyckas kan sessionen pågå så länge som *MaxLeaseTime* innan den stängs av av servern på grund av krascher eller anslutningsproblem.

> [!NOTE]
> Om du stoppar en session får du omedelbar verkan och kan inte ångras. När du har stoppats måste du skapa en ny session, med samma startomkostnader.

## <a name="reusing-sessions"></a>Återanvända sessioner

Att skapa en ny session är tyvärr en tidskrävande åtgärd. Därför bör man försöka skapa sessioner sällan, och återanvända dem när det är möjligt.

Infoga följande kod i *RemoteRendering-skriptet* och ta bort de gamla versionerna av dubblettfunktionerna:

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
> Innan du kör den här koden måste du inaktivera alternativet **Automatisk stoppsession** i RemoteRendering-komponenten. Annars stoppas varje session som du skapar automatiskt när du stoppar simuleringen, och försök att återanvända den misslyckas.

När du trycker på *Spela*upp får du nu tre knappar i visningsområdet: **Skapa session,** **Öppna sessioner**för frågor och Använd **befintlig session**. Den första knappen skapar alltid en ny session. Den andra knappen frågar vilka *aktiva* sessioner som finns. Om du inte angav ett sessions-ID manuellt för att försöka använda, väljer den här åtgärden automatiskt sessions-ID för framtida bruk. Den tredje knappen försöker ansluta till en befintlig session. Antingen en som du har angett manuellt via komponentegenskapen *Sessions-ID* eller en som hittades av *Query Active Sessions*.

Funktionen **AutoStartSessionAsync** används för att simulera knapptryckningar utanför redigeraren.

> [!TIP]
> Det är möjligt att öppna sessioner som har stoppats, upphört att gälla eller är i feltillstånd. Även om de inte längre kan användas för rendering kan du fråga efter deras uppgifter när du har öppnat en inaktiv session. Koden ovan kontrollerar en sessions `ARRService_OnSessionStarted`status i , för att automatiskt stoppa när sessionen har blivit oanvändbar.

Med den här funktionen kan du nu skapa och återanvända sessioner, vilket bör förbättra utvecklingsarbetsflödet avsevärt.

Vanligtvis utlöses skapandet av en session utanför klientprogrammet på grund av den tid som krävs för att snurra upp servern.

## <a name="connect-to-an-active-session"></a>Ansluta till en aktiv session

Hittills har vi skapat eller öppnat sessioner. Nästa steg är att *ansluta* till en session. När den är ansluten kommer renderingsservern att producera bilder och skicka en videoström till vårt program.

Infoga följande kod i *RemoteRendering-skriptet* och ta bort de gamla versionerna av dubblettfunktionerna:

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

Så här testar du den här funktionen:

1. Tryck **på Spela** i enighet.
1. Öppna en session:
    1. Om du redan har en session trycker du på **Fråga aktiva sessioner** och sedan använder befintlig **session**.
    1. Annars trycker du på **Skapa session**.
1. Tryck på **Anslut**.
1. Efter några sekunder ska konsolutgången skriva ut att du är ansluten.
1. För tillfället får inget annat hända.
1. Tryck på **Koppla från** eller stoppa Unitys uppspelningsläge.

>[!NOTE]
> Flera användare kan *öppna* en session för att fråga sin information, men bara en användare kan *anslutas* till en session åt gången. Om en annan användare redan är ansluten misslyckas anslutningen med ett **handskakningsfel**.

## <a name="load-a-model"></a>Ladda en modell

Infoga följande kod i *RemoteRendering-skriptet* och ta bort de gamla versionerna av dubblettfunktionerna:

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

När du nu trycker på play, öppnar en session och ansluter till den visas knappen **Läs in modell.** När du har klickat på den visar konsolens utgång belastningsförloppet och när den når 100 % bör du se modellen för en motor visas:

![Modell som läses in i redigeraren](media/model-loaded-replace-me.png)

[WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) är en viktig komponent som används för [hologramstabilitet](https://docs.microsoft.com/windows/mixed-reality/hologram-stability). Det kommer dock bara att ha effekt när den distribueras på en mixed reality-enhet.

> [!TIP]
> Om du har följt [snabbstarten: Konvertera en modell för rendering](../../quickstarts/convert-model.md)vet du redan hur du konverterar dina egna modeller. Allt du behöver göra nu för att återge det är att placera URI till en konverterad modell i egenskapen *Modellnamn.*

## <a name="display-frame-statistics"></a>Visa bildrutestatistik

Azure Remote Rendering spårar olika information om anslutningens kvalitet. För ett snabbt sätt att visa denna information gör du följande:

Skapa ett [nytt skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) och ge det namnet **RemoteFrameStats**. Öppna skriptfilen och ersätt hela innehållet med koden nedan:

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

Skapa en GameObject och namnge den *FrameStats*. Fäst den som en underordnad nod på *huvudkameraobjektet* och ställ in dess position på **x = 0, y = 0, z = 0,325**. Lägg till **RemoteFrameStats-komponenten** i objektet.

Lägg till ett **objekt för ett användargränssnitt > Canvas-objektet** i *FrameStats-objektet* och ange dess egenskaper så här:

![egenskaper för arbetsyta](media/framestats-canvas.png)

Lägg till ett **användargränssnitt > textobjekt** som underordnad på arbetsytan och ange dess egenskaper så här:

![textegenskaper](media/framestats-text.png)

Markera *FrameStats-objektet* och fyll i **framestats-fältet** genom att klicka på cirkelikonen och markera **textobjektet:**

![ange textegenskap](media/framestats-set-text.png)

När texten är ansluten till fjärrsessionen ska den nu visa direktuppspelningsstatistiken:

![ram statistik utdata](media/framestats-output.png)

Koden inaktiverar statistikuppdateringen utanför redigeraren eftersom en huvudlåst textruta skulle vara distraherande. En mer sofistikerad implementering finns i [Quickstart-projektet.](../../quickstarts/render-model.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig alla nödvändiga åtgärder för att ta ett tomt Unity-projekt och få det att fungera med Azure Remote Rendering. I nästa handledning kommer vi att ta en närmare titt på hur man arbetar med avlägsna enheter.

> [!div class="nextstepaction"]
> [Självstudiekurs: Arbeta med fjärrenheter i Unity](working-with-remote-entities.md)
