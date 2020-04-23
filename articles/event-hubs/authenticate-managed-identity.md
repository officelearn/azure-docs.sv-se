---
title: Autentisering av en hanterad identitet med Azure Active Directory
description: Den här artikeln innehåller information om hur du autentiserar en hanterad identitet med Azure Active Directory för att komma åt Azure Event Hubs-resurser
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: dfc60fbc03021e72dccc0f60a7ac34d204ef6df9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025194"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Autentisera en hanterad identitet med Azure Active Directory för att komma åt eventhubbarresurser
Azure Event Hubs stöder Azure Active Directory (Azure AD) autentisering med [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md). Hanterade identiteter för Azure-resurser kan auktorisera åtkomst till eventhubbar-resurser med Azure AD-autentiseringsuppgifter från program som körs i virtuella Azure-datorer ( virtuella datorer), funktionsappar, skalningsuppsättningar för virtuella datorer och andra tjänster. Genom att använda hanterade identiteter för Azure-resurser tillsammans med Azure AD-autentisering kan du undvika att lagra autentiseringsuppgifter med dina program som körs i molnet.

Den här artikeln visar hur du godkänner åtkomst till en händelsehubb med hjälp av en hanterad identitet från en Virtuell Azure.This article shows how to authorize access to an event hub by using a managed identity from an Azure VM.

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator
Innan du kan använda hanterade identiteter för Azure-resurser för att auktorisera eventhubbar resurser från din virtuella dator måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Mer information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure Portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-klientbibliotek](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Bevilja behörigheter till en hanterad identitet i Azure AD
Om du vill auktorisera en begäran till Event Hubs-tjänsten från en hanterad identitet i ditt program konfigurerar du först RBAC-inställningar (Role-Based Access Control) för den hanterade identiteten. Azure Event Hubs definierar RBAC-roller som omfattar behörigheter för att skicka och läsa från eventhubbar. När RBAC-rollen tilldelas en hanterad identitet beviljas den hanterade identiteten åtkomst till Event Hubs-data i lämpligt omfång.

Mer information om hur du tilldelar RBAC-roller finns i [Autentisera med Azure Active Directory för åtkomst till eventhubbar-resurser](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Använd Event Hubs med hanterade identiteter
Om du vill använda händelsehubbar med hanterade identiteter måste du tilldela rollen och lämpligt scope. Proceduren i det här avsnittet använder ett enkelt program som körs under en hanterad identitet och får åtkomst till eventhubbar-resurser.

Här använder vi ett exempel på ett webbprogram som finns i [Azure App Service](https://azure.microsoft.com/services/app-service/). Stegvisa instruktioner för hur du skapar ett webbprogram finns [i Skapa en ASP.NET Core-webbapp i Azure](../app-service/app-service-web-get-started-dotnet.md)

När programmet har skapats gör du så här: 

1. Gå till **Inställningar** och välj **Identitet**. 
1. Välj den **status som** ska vara **på**. 
1. Spara inställningen genom att välja **Spara**. 

    ![Hanterad identitet för en webbapp](./media/authenticate-managed-identity/identity-web-app.png)

När du har aktiverat den här inställningen skapas en ny tjänstidentitet i din Azure Active Directory (Azure AD) och konfigureras till programtjänstvärden.

Tilldela nu den här tjänstidentiteten till en roll i det nödvändiga omfånget i dina Event Hubs-resurser.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Så här tilldelar du RBAC-roller med Azure-portalen
Om du vill tilldela en roll till eventhubbar-resurser navigerar du till den resursen i Azure-portalen. Visa inställningarna för åtkomstkontroll (IAM) för resursen och följ dessa instruktioner för att hantera rolltilldelningar:

> [!NOTE]
> I följande steg tilldelas en tjänstidentitetsroll till namnområdena Event Hubs. Du kan följa samma steg för att tilldela en roll som omfattas av en eventhubbarresurs. 

1. I Azure-portalen navigerar du till namnområdet Event Hubs och visar **översikten** för namnområdet. 
1. Välj **Åtkomstkontroll (IAM)** på den vänstra menyn för att visa åtkomstkontrollinställningar för händelsehubben.
1.  Välj fliken **Rolltilldelningar** om du vill visa listan över rolltilldelningar.
3.  Välj **Lägg till** om du vill lägga till en ny roll.
4.  På sidan **Lägg till rolltilldelning** väljer du de roller för händelsehubbar som du vill tilldela. Sök sedan för att hitta tjänstidentiteten som du hade registrerat för att tilldela rollen.
    
    ![Lägg till sidan för rolltilldelning](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Välj **Spara**. Identiteten som du har tilldelat rollen visas under den rollen. Följande bild visar till exempel att tjänstidentitet har ägare till Event Hubs Data.
    
    ![Identitet som tilldelats en roll](./media/authenticate-managed-identity/role-assigned.png)

När du har tilldelat rollen har webbprogrammet åtkomst till eventhubbarresurserna under det definierade scopet. 

### <a name="test-the-web-application"></a>Testa webbprogrammet
1. Skapa ett namnområde för händelsehubbar och en händelsehubb. 
2. Distribuera webbappen till Azure. Se följande avsnitt med flikar för länkar till webbprogrammet på GitHub. 
3. Kontrollera att SendReceive.aspx är inställt som standarddokument för webbappen. 
3. Aktivera **identitet** för webbappen. 
4. Tilldela den här identiteten till rollen **Händelsehubbar dataägare** på namnområdesnivå eller händelsenavnivå. 
5. Kör webbprogrammet, ange namnområdesnamn och händelsehubbnamn, ett meddelande och välj **Skicka**. Om du vill ta emot händelsen väljer du **Ta emot**. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.Messaging.EventHubs (senaste)](#tab/latest)
Du kan nu starta webbprogrammet och peka din webbläsare på exempelsapxsidan. Du hittar exempelwebbprogrammet som skickar och tar emot data från Event Hubs-resurser i [GitHub-repoen](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp).

Installera det senaste paketet från [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)och börja skicka händelser till eventhubbar med **EventHubProducerClient** och ta emot händelser med **EventHubConsumerClient**. 

> [!NOTE]
> Ett Java-exempel som använder en hanterad identitet för att publicera händelser i en händelsehubb finns i [Publicera händelser med Azure-identitetsexempel på GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs).

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (äldre)](#tab/old)
Du kan nu starta webbprogrammet och peka din webbläsare på exempelsapxsidan. Du hittar exempelwebbprogrammet som skickar och tar emot data från Event Hubs-resurser i [GitHub-repoen](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Installera det senaste paketet från [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)och börja skicka till och ta emot data från eventhubbar med eventhubclient som visas i följande kod: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Event Hubs för Kafka
Du kan använda Apache Kafka-program för att skicka meddelanden till och ta emot meddelanden från Azure Event Hubs med hjälp av hanterad identitet OAuth. Se följande exempel på GitHub: [Event Hubs for Kafka – skicka och ta emot meddelanden med den hanterade identiteten OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Exempel
- Exempel på **Azure.Messaging.EventHubs**
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Exempel på Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Dessa exempel använder det gamla **Microsoft.Azure.EventHubs-biblioteket,** men du kan enkelt uppdatera det till det senaste **Azure.Messaging.EventHubs-biblioteket.** Information om hur du flyttar exemplet från att använda det gamla biblioteket till ett nytt finns i [guiden för att migrera från Microsoft.Azure.EventHubs till Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
    Det här exemplet har uppdaterats för att använda det senaste **Azure.Messaging.EventHubs-biblioteket.**
- [Event Hubs for Kafka – skicka och ta emot meddelanden med hanterad identitet OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Nästa steg
- Se följande artikel om hanterade identiteter för Azure-resurser: [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)
- Se följande relaterade artiklar:
    - [Autentisera begäranden till Azure Event Hubs från ett program med Azure Active Directory](authenticate-application.md)
    - [Autentisera begäranden till Azure Event Hubs med signaturer för delad åtkomst](authenticate-shared-access-signature.md)
    - [Auktorisera åtkomst till eventhubbar-resurser med Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Auktorisera åtkomst till eventhubbar-resurser med signaturer för delad åtkomst](authorize-access-shared-access-signature.md)