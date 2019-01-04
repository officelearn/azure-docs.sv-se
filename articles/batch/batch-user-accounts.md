---
title: Köra uppgifter under användarkonton – Azure Batch | Microsoft Docs
description: Konfigurera användarkonton för att köra uppgifter i Azure Batch
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: b59bb835c9858c6e47b8bb3a3518086e887d0d84
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542840"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Kör aktiviteter på användarkonton i Batch

En aktivitet i Azure Batch körs alltid under ett användarkonto. Uppgifter som körs under standardanvändarkonton utan administratörsbehörighet som standard. De här standardinställningarna är vanligtvis tillräckliga. För vissa scenarier, men är det användbart för att kunna konfigurera användarkonto under vilket du vill att en uppgift ska köras. Den här artikeln beskrivs vilka typer av användarkonton och hur du kan konfigurera dem för ditt scenario.

## <a name="types-of-user-accounts"></a>Användarkontotyper

Azure Batch får du två typer av användarkonton för att köra uppgifter:

- **Automatisk användarkonton.** Automatisk användarkonton är inbyggda användarkonton som skapas automatiskt av Batch-tjänsten. Uppgifter som körs under en automatisk användarkonto som standard. Du kan konfigurera automatisk användare-specifikationen för en aktivitet att indikera en uppgift ska köras under vilket auto-användarkonto. Specifikationen automatiskt användare kan du ange höjning och i omfånget för automatisk-användarkontot som ska köra uppgiften. 

- **En namngiven användarkonto.** Du kan ange en eller flera namngivna användarkonton för en pool när du skapar poolen. Varje användarkonto skapas på varje nod i poolen. Förutom kontonamnet ange lösenordet för användarkontot, höjning nivå och, för Linux-pooler, SSH-privata nyckeln. När du lägger till en uppgift kan du ange namngivna användarkontot som aktiviteten ska köras.

> [!IMPORTANT] 
> Batch service-versionen 2017-01-01.4.0 introducerar en stor förändring som kräver att du uppdaterar din kod för att anropa den här versionen. Om du migrerar kod från en äldre version av Batch, Observera att den **runElevated** egenskapen stöds inte längre i REST API eller Batch-klientbibliotek. Använd den nya **userIdentity** egenskapen för en uppgift att ange höjning. Se avsnittet [uppdatera din kod till det senaste Batch-klientbiblioteket](#update-your-code-to-the-latest-batch-client-library) snabb riktlinjer för att uppdatera din Batch-kod om du använder en av klientbiblioteken.
>
>

> [!NOTE] 
> Användarkonton som beskrivs i den här artikeln stöder inte Remote Desktop Protocol (RDP) eller Secure Shell (SSH), av säkerhetsskäl. 
>
> Om du vill ansluta till en nod som kör Linux VM-konfiguration via SSH, se [Använd Fjärrskrivbord till en Linux-VM i Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Om du vill ansluta till noder som kör Windows via RDP, se [Anslut till en Windows Server VM](../virtual-machines/windows/connect-logon.md).<br /><br />
> Om du vill ansluta till en nod som kör molntjänstkonfigurationen via RDP, se [aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>

## <a name="user-account-access-to-files-and-directories"></a>Användarkontoåtkomst till filer och kataloger

Både en automatisk användarkonto och ett konto för namngiven användare har läs-/ skrivåtkomst till aktivitetens arbetskatalog, delade katalogen och flera instanser uppgifter directory. Båda typerna av konton har läsbehörighet till de nystartade företag och jobbet förberedelse katalogerna.

Om en aktivitet körs under samma konto som användes för att köra en startaktivitet, har skrivskyddad åtkomst till katalogen för aktiviteten starta uppgiften. Om en aktivitet körs under samma konto som användes för att köra en jobbförberedelseaktivitet, på samma sätt har läsåtkomst till aktivitetskatalogen jobbförberedelse av uppgiften. Om en aktivitet körs under ett annat konto än startaktiviteten eller jobbförberedelseaktiviteten, har bara läsbehörighet till katalogen respektive med uppgiften.

Läs mer om hur du använder filer och kataloger från en aktivitet, [utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Utökad åtkomst för aktiviteter 

Användarens höjning nivån anger om en aktivitet körs med utökad åtkomst. Både en automatisk användarkonto och ett konto för namngiven användare kan köra med utökad åtkomst. De två alternativen för höjning nivå är:

- **NonAdmin:** Aktiviteten körs som en vanlig användare utan utökad åtkomst. Standardnivån för höjning för ett Batch-användarkonto är alltid **NonAdmin**.
- **Administratör:** Uppgiften körs som en användare med utökad åtkomst och med fullständig administratörsbehörighet. 

## <a name="auto-user-accounts"></a>Automatisk-användarkonton

Som standard kör uppgifter i Batch under en automatisk-användarkonto som en vanlig användare utan utökad åtkomst och med aktiviteten omfång. När automatisk användare-specifikationen har konfigurerats för uppgiften omfång, skapar Batch-tjänsten ett automatiskt-användarkonto för aktiviteten endast.

Alternativ till uppgiften omfånget är poolen omfång. När automatisk användare-specifikationen för en aktivitet har konfigurerats för poolen omfång, körs aktiviteten under en automatisk-användarkonto som är tillgängliga för alla aktiviteter i poolen. Mer information om poolen scope finns i avsnittet [kör en uppgift som automatiskt-användare med pool omfattning](#run-a-task-as-the-autouser-with-pool-scope).   

Standardvärde skiljer sig på Windows och Linux-noder:

- På Windows-noder kör uppgifter under uppgiften omfång som standard.
- Linux-noder kan alltid köras under pool omfång.

Det finns fyra möjliga konfigurationerna för specifikationen automatiskt användare som motsvarar ett unikt automatisk-användarkonto:

- Icke-administratörsåtkomst med uppgiften omfattning (standard automatiskt användare specifikation)
- Administratörsåtkomst (förhöjd) med uppgiften omfattning
- Icke-administratörsåtkomst med pool omfattning
- Administratörsåtkomst med pool omfattning

> [!IMPORTANT] 
> Aktiviteter som körs under definitionsområdet för aktiviteten har inte faktiska åtkomst till andra aktiviteter på en nod. Men kan en obehörig användare med åtkomst till kontot undvika den här begränsningen genom att skicka in en aktivitet som körs med administratörsbehörighet och har åtkomst till andra kataloger för uppgiften. En obehörig användare kan också använda RDP eller SSH för att ansluta till en nod. Det är viktigt att skydda åtkomsten till ditt Batch-kontonycklar för att förhindra att ett sådant scenario. Om du misstänker att ditt konto har komprometterats, måste du återskapa nycklarna.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Köra en aktivitet som automatiskt-användare med utökad åtkomst

Du kan konfigurera automatisk användare specifikationen för administratörsbehörighet när du vill köra en aktivitet med utökad åtkomst. Till exempel behöva en startaktivitet utökad åtkomst att installera programvara på noden.

> [!NOTE] 
> I allmänhet är det bäst att använda utökad åtkomst vid behov. Bästa praxis rekommenderar beviljar den lägsta behörigheten som krävs för att uppnå önskat utfall. Till exempel om en startaktivitet installerar programvara för den aktuella användaren, i stället för för alla användare du att undvika att bevilja utökad åtkomst till aktiviteter. Du kan konfigurera automatisk användare specifikationen för poolen omfång och icke-administratörer åtkomst för alla aktiviteter som måste köras under samma konto, inklusive startaktiviteten. 
>
>

I följande kodavsnitt visar hur du konfigurerar automatisk användare-specifikationen. Exemplen anger höjningen till `Admin` och omfång till `Task`. Uppgiften omfång är standardinställningen, men ingår här artikelns exempel.

#### <a name="batch-net"></a>.NET för Batch

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Python för Batch

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Köra en aktivitet som automatiskt-användare med pool omfattning

När en nod har etablerats skapas två pool hela automatisk-användarkonton på varje nod i poolen, en med utökad åtkomst och en utan utökad åtkomst. Ställa in automatisk-användarens omfång pool omfattningen för en viss aktivitet kör aktiviteten enligt något av dessa två konton för hela poolen automatiskt användare. 

När du anger poolen omfattning för automatisk-användare, alla aktiviteter som körs med administratörsåtkomst köras under samma pool hela automatisk-användarkonto. På samma sätt köra aktiviteter som körs utan administratörsbehörighet också en enda pool hela automatisk-användarkontot. 

> [!NOTE] 
> De två kontona för hela poolen automatiskt användare är separata konton. Aktiviteter som körs under pool hela administrativt konto kan inte dela data med aktiviteter som körs under kontot som standard, och vice versa. 
>
>

Fördelen med att köras i samma automatisk-användarkontot är att aktiviteter ska kunna dela data med andra aktiviteter som körs på samma nod.

Dela hemligheter mellan aktiviteter är ett scenario där kör uppgifter enligt något av de två kontona för hela poolen automatiskt användare är användbart. Anta exempelvis att en startaktivitet måste etablera en hemlighet till den nod som andra aktiviteter kan använda. Du kan använda Windows Data Protection API (DPAPI), men det krävs administratörsbehörighet. I stället kan du skydda hemligheten på användarnivå. Aktiviteter som körs under kontot för samma användare kan komma åt hemligheten utan utökad åtkomst.

Ett annat scenario där du kanske vill köra uppgifter under en automatisk användarkonto med pool omfattning är en filresurs för Message Passing Interface (MPI). En MPI-filresurs är användbart när noderna i MPI-aktiviteten behöver arbeta med samma fildata. Huvudnoden skapar en filresurs som underordnade noder har åtkomst till om de körs i samma automatisk-användarkontot. 

Följande kodavsnitt anger automatisk-användarens omfång till poolen omfattningen för en aktivitet i Batch .NET. Nivå för höjning utelämnas, så att aktiviteten körs i standard-pool hela automatisk-användarkontot.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Namngivna användarkonton

Du kan definiera namngivna användarkonton när du skapar en pool. En namngiven användarkontot har ett namn och lösenord som du anger. Du kan ange höjning för en namngiven användarkonto. Linux-noder kan du även ange en SSH-privata nyckel.

En namngiven användarkontot finns på alla noder i poolen och är tillgänglig för alla aktiviteter som körs på dessa noder. Du kan definiera valfritt antal namngivna användare för en pool. När du lägger till en aktivitet eller uppgift samling kan du ange att aktiviteten körs under en av de namngivna användarkonton som definierats i poolen.

En namngiven användarkontot är användbart när du vill köra alla uppgifter i ett jobb under samma användarkonto, men kunna isolera dem från aktiviteter som körs i andra jobb på samma gång. Du kan till exempel skapa en namngiven användare för varje jobb och kör varje jobbets aktiviteter under det namngivna användaren-kontot. Varje jobb kan sedan dela en hemlighet med sin egen uppgifter, men inte med aktiviteter som körs i andra jobb.

Du kan också använda ett konto för namngiven användare för att köra en uppgift som anger behörigheter på externa resurser, till exempel filresurser. Med ett konto för namngiven användare, styra användarnas identiteter och kan använda den användaridentiteten för att ange behörigheter.  

Namngivna användarkonton aktivera lösenord utan SSH mellan Linux-noder. Du kan använda en namngiven användarkonto med Linux-noder som måste köra aktiviteter med flera instanser. Varje nod i poolen kan köra uppgifter under ett användarkonto som har definierats för hela poolen. Läs mer om aktiviteter med flera instanser, [använder flera\-instans aktiviteter att köra MPI-program](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Skapa namngiven användarkonton

Lägga till en samling av användarkonton i poolen för att skapa namngiven användarkonton i Batch. I följande kodavsnitt visar hur du skapar namngivna användarkonton i .NET, Java och Python. Följande kodfragment visar hur du skapar både admin och icke-administratörer med namnet konton i en pool. Exemplen skapa pooler med hjälp av molntjänstkonfigurationen, men du kan använda samma metod när du skapar en Windows- eller Linux-pool med konfigurationen av virtuella datorn.

#### <a name="batch-net-example-windows"></a>Batch .NET-exempel (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Batch .NET-exempel (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Batch Java-exemplet

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Batch Python-exempel

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.nonadmin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Köra en aktivitet under ett konto för namngiven användare med utökad åtkomst

Ange om du vill köra en uppgift som en förhöjd aktivitetens **UserIdentity** egenskapen till en namngiven användarkonto som har skapats med dess **ElevationLevel** egenskapen `Admin`.

Det här kodfragmentet anger att aktiviteten ska köras under ett konto för namngiven användare. Det här kontot för namngiven användare har definierats för poolen när poolen skapades. I det här fallet, skapades namngiven användare-kontot med administratörsbehörighet:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Uppdatera din kod till det senaste Batch-klientbiblioteket

Batch service-versionen 2017-01-01.4.0 introducerar en stor förändring, ersätter den **runElevated** egenskap som är tillgänglig i tidigare versioner med den **userIdentity** egenskapen. Följande tabeller innehåller en enkel mappning som du kan använda för att uppdatera din kod från tidigare versioner av klientbiblioteken.

### <a name="batch-net"></a>.NET för Batch

| Om din kod använder...                  | Uppdatera den så att...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` Ej angiven | Ingen uppdatering krävs                                                                                               |

### <a name="batch-java"></a>Batch Java

| Om din kod använder...                      | Uppdatera den så att...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` Ej angiven | Ingen uppdatering krävs                                                                                                                     |

### <a name="batch-python"></a>Python för Batch

| Om din kod använder...                      | Uppdatera den så att...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, där <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin)) `                |
| `run_elevated=False`                      | `user_identity=user`, där <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin)) `             |
| `run_elevated` Ej angiven | Ingen uppdatering krävs                                                                                                                                  |


## <a name="next-steps"></a>Nästa steg

* En detaljerad översikt över Batch finns [utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).